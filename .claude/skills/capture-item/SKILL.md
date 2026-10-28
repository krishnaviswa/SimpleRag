---
name: capture-item
description: "Creates the next backlog/BL-NNN.md from a user request, with status proposed, empty spec and report lines, and owner prod-specialist. Use when a code task has no backlog item yet and must be captured without designing a solution."
---

# Capture item

Create the next backlog item from the user request. Stop when the file is written. Product writes the spec after this.

## Next id

Read the ids in existing `backlog/BL-*.md` files. Add 1 to the highest `NNN` and zero-pad it to three digits. If none exist, use `BL-001`. Do not fill gaps or reuse an id.

## File

Write `backlog/BL-NNN.md` in this shape:

```markdown
# BL-001: Short title

status: proposed
spec:
report:
owner: prod-specialist

Request: one or two sentences restating the request.
```

- The title line is the heading: the new id and a short title taken from the request.
- `status` is `proposed`.
- `spec:` and `report:` stay empty.
- `owner` is `prod-specialist`.
- The request summary restates what the user asked.

Leave solution design out of the file. That includes acceptance criteria, task lists, file layouts, and code.
