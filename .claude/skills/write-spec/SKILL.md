---
name: write-spec
description: "Writes specs/BL-NNN.md for one backlog item with the problem, demo behavior, acceptance-criteria checklist, and out of scope, then sets that item to ready with owner dev-specialist. Use when a proposed item needs a product spec and no application code or tests should be written."
---

# Write spec

Write `specs/BL-NNN.md` for one backlog item whose status is `proposed`. If the status is anything else, stop.

Application code, tests, and an in-app agent stay out of this step.

## Spec sections

- **Problem** — what is missing or broken for the user.
- **What the demo shows** — the behavior a viewer can see.
- **Acceptance criteria** — a checklist (`- [ ]`), one testable outcome per item.
- **Out of scope** — work this item does not include.

Keep the spec to those sections. Leave out file layouts, code, and task breakdowns.

## Handoff

When the acceptance-criteria checklist has at least one item:

1. Set `spec: specs/BL-NNN.md` on `backlog/BL-NNN.md`.
2. Set `status: ready` and `owner: dev-specialist`, using the `update-status` rules.

When the checklist is missing, leave the backlog status at `proposed` and say that acceptance criteria are still required.
