# Retail RAG on Railway

Build the retail knowledge assistant as one cheap Railway service: Streamlit chat, FastAPI mock retail API, SQLite, embedded Qdrant, LangGraph, FastEmbed retrieval, Groq Llama 3.1 for answers, plus a RAGAS script and optional Langfuse tracing.

Implement it in this repo on a feature branch `feature/retail-rag`. One public URL. Groq supplies Llama 3.1. The source specification is the open-source retail RAG build plan: product catalog, policies, reviews, inventory and order APIs, LangGraph routing, reranking, context grading, and role-based access.

## Hosting choices

Railway bills for RAM and uptime. A 7B model, the BGE-m3 reranker, a separate Qdrant server, and a self-hosted Langfuse stack would blow a cheap account. The running service stays near **1 GB RAM**:

- **LLM:** Groq `llama-3.1-8b-instant` via `GROQ_API_KEY` (free tier). Model name is env-configurable.
- **Embeddings:** FastEmbed ONNX `BAAI/bge-small-en-v1.5` inside the container (query-time embeddings, no extra API).
- **Reranker:** FastEmbed ONNX `Xenova/ms-marco-MiniLM-L-6-v2`. Same two-stage design as the source plan (top 20, keep top 5). `RERANKER_MODEL` can later point at `BAAI/bge-reranker-v2-m3` on a larger instance.
- **Vectors:** embedded Qdrant (`QdrantClient(path=...)`) on a Railway volume at `/data`. Metadata filters stay real Qdrant filters.
- **Structured data:** SQLite on the same volume.
- **Tracing:** Langfuse Cloud free tier, attached only when `LANGFUSE_PUBLIC_KEY` and `LANGFUSE_SECRET_KEY` are set.
- **Public port:** Streamlit binds `$PORT`. FastAPI listens on `127.0.0.1:8000` in the same container. The agent calls that mock API over HTTP, so the inventory and order tools are real API calls.

```mermaid
flowchart TD
  user[Browser] --> streamlit[Streamlit on Railway]
  streamlit --> graph[LangGraph]
  graph --> route[route_query via Groq]
  route --> products[retrieve_products]
  route --> policies[retrieve_policies]
  route --> reviews[retrieve_reviews]
  route --> inventory[inventory_tool]
  route --> orders[order_tool]
  route --> chat[small_talk]
  products --> qdrant[Embedded Qdrant]
  policies --> qdrant
  reviews --> qdrant
  inventory --> api[FastAPI on localhost]
  orders --> api
  api --> sqlite[SQLite]
  products --> rerank[FastEmbed reranker]
  policies --> rerank
  reviews --> rerank
  rerank --> grade[grade_context via Groq]
  grade -->|sufficient| answer[generate_answer via Groq]
  grade -->|weak and retries left| rewrite[rewrite_query]
  rewrite --> route
  answer --> langfuse[Langfuse when keys exist]
```

## Layout

Files land at the repo root:

- [backend/main.py](../backend/main.py) — starts nothing by itself; Railway uses [scripts/start.sh](../scripts/start.sh)
- [backend/ingest.py](../backend/ingest.py) — CSV, Markdown, and API rows into SQLite and Qdrant
- [backend/auth.py](../backend/auth.py) — bcrypt check and role helpers
- [backend/rag/graph.py](../backend/rag/graph.py) — LangGraph `RetailState` and edges
- [backend/rag/router.py](../backend/rag/router.py), [retriever.py](../backend/rag/retriever.py), [reranker.py](../backend/rag/reranker.py), [grader.py](../backend/rag/grader.py), [llm.py](../backend/rag/llm.py)
- [backend/api/retail_mock_api.py](../backend/api/retail_mock_api.py) — `GET /inventory/{product_id}`, `GET /products`, `GET /orders/{order_id}`, `POST /ingest/stock-update`, `POST /ingest/price-update`
- [frontend/app.py](../frontend/app.py), [login.py](../frontend/login.py), [chat.py](../frontend/chat.py)
- [data/products.csv](../data/products.csv), [data/reviews.csv](../data/reviews.csv), [data/policies/](../data/policies/)
- [Dockerfile](../Dockerfile), [railway.toml](../railway.toml), [docker-compose.yml](../docker-compose.yml) — one `app` service, volume `/data`
- [eval/golden.json](../eval/golden.json), [eval/run_ragas.py](../eval/run_ragas.py)
- [requirements.txt](../requirements.txt), [.env.example](../.env.example), [README.md](../README.md)

`docker-compose.yml` is the same single service for anyone who later runs Docker. It does not add Ollama or a Qdrant server.

## Data

About 20 products across jackets and shoes, stores `mumbai` and `delhi`, prices in INR, `visibility` of `public` or `internal`. Reviews reference those product ids. Markdown docs: return, shipping, warranty, size guide, FAQ, plus one **internal** staff policy.

SQLite tables: `users`, `products`, `reviews`, `inventory`, `orders`. Demo orders belong to the customer user so “Where is order ORD-1023?” is answerable. Passwords are stored as bcrypt hashes. README lists the demo logins (`customer@demo.com` / `customer123`, manager, admin).

Qdrant collections: `products`, `policies`, `reviews`. Every point keeps metadata (`source`, `product_id`, `category`, `price`, `store_id`, `visibility`) plus `text`. Chunk size about 800 characters with overlap, which is enough for these short docs. Ingest runs on boot when `/data/retail.db` is missing. An admin sidebar action forces a rebuild. A manager/admin “Simulate stock update” button POSTs a fake update, writes SQLite, and patches the Qdrant payload.

## Authorization

Enforced in retrieval filters and API tools, and mirrored in the UI.

- **customer:** public catalog, policies, and reviews for their `store_id`; inventory returns public availability only (in stock / out), not counts; orders only when `user_id` matches.
- **store_manager:** customer access, plus quantities for their store and internal docs for their store.
- **admin:** `store_id=all` skips the store filter, sees every order, and can rebuild the index.

Sidebar filters (max price, category) are added to the Qdrant filter on product search.

## LangGraph

`RetailState` fields: `user_id`, `session_id`, `role`, `store_id`, `question`, `route`, `candidates`, `reranked_docs`, `inventory`, `answer`, `sources`, `retry_count`.

Routes: `product_search`, `policy_search`, `review_search`, `inventory_tool`, `order_tool`, `small_talk`. The router is a Groq call with a fixed label set. Product answers then call the inventory API for the retrieved ids so the chat can say “In stock”. Compare-style questions stay on `product_search`; the generator compares the retrieved rows.

After rerank, the grader asks Groq whether the context is enough. Weak context rewrites the query and retrieves again, at most twice. After that the answer is: “I could not find this in the retail knowledge base.” Generation must cite only retrieved context and the tool payload. Sources render in an `st.expander`.

Groq errors and rate limits surface as a short chat message with a retry, and the UI shows a confirmation-style error state rather than a stack trace.

## Deploy

[Dockerfile](../Dockerfile) installs Python deps and pre-downloads the two FastEmbed models so the first request does not depend on Hugging Face. [scripts/start.sh](../scripts/start.sh) starts FastAPI, runs ingest if needed, then Streamlit on `$PORT`.

[railway.toml](../railway.toml) plus README steps: new Railway project from this repo, one volume mounted at `/data`, variables `GROQ_API_KEY`, optional `GROQ_MODEL`, optional Langfuse keys. Target **1 GB** RAM, one replica. Expected cost is the hobby usage for a single small web service plus Groq’s free tier. Langfuse stays on Langfuse Cloud.

README includes the interview paragraph rewritten for this deployment: Groq Llama 3.1, embedded Qdrant, FastEmbed reranker, role filters inside tools.

## Evaluation

[eval/golden.json](../eval/golden.json) holds about eight questions (product filter, policy, review, stock, order, unauthorized order, missing fact, greeting). [eval/run_ragas.py](../eval/run_ragas.py) runs the graph and scores faithfulness, answer relevancy, and context precision with RAGAS, using Groq as the judge. It is a manual script, not a second service.

## Verification

Pytest covers auth hashes, role filters, order scoping, ingest into a temp SQLite and temp Qdrant, and the graph with a fake chat model (no Groq). A live Groq call and the Streamlit UI need the deployed URL; browser checks wait until that URL exists. Deployment itself waits until asked, because it needs a Railway account and `GROQ_API_KEY`.

## Work items

- Add sample catalog, reviews, policies, SQLite schema, bcrypt demo users, and role/store helpers
- Build FastAPI inventory, product, order, and stock/price update endpoints on SQLite
- Ingest structured rows and chunked docs into SQLite and embedded Qdrant with metadata
- Implement LangGraph router, filtered retrieval, FastEmbed rerank, Groq grader/rewriter/generator, and inventory/order tools
- Build login, session, sidebar filters, chat with citations, admin rebuild, and stock-update simulation
- Add Dockerfile, start script, railway.toml, compose for the single service, env example, and README
- Add RAGAS script, optional Langfuse callbacks, and unit tests that do not need a live Groq key
