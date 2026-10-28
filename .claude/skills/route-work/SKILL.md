---
name: route-work
description: "Reads backlog item status and names the single next specialist for a code task. Use in the parent session before any specialist edits files. A specialist already doing the task does not use this skill."
---

# Route work

Used by the parent session, not by a specialist doing the task. Read the backlog item and name one next specialist. Capture, spec, implementation, tests, and file edits belong to that specialist.

Read `backlog/BL-NNN.md` and, when present, `specs/BL-NNN.md` and `reports/BL-NNN.md`. Apply the first matching row:

1. **blocked** — Stop and report the blocker recorded on the item. When no blocker text is written, say the item is blocked and quote its request summary. Name no specialist to continue.
2. **Missing item** — `backlog-specialist` (`capture-item`). `prod-specialist` comes after `backlog/BL-NNN.md` exists. Name only `backlog-specialist` now.
3. **proposed** — `prod-specialist` (`write-spec`). Capture is already done, so this is the next step after `backlog-specialist`.
4. **Pass report waiting** — status is `in-test` and `reports/BL-NNN.md` records a pass for every acceptance criterion. `backlog-specialist` (`update-status`) sets `done`.
5. **ready** — `dev-specialist` (`implement-spec`). `test-specialist` may draft tests; the single next owner is `dev-specialist`.
6. **in-progress** — `dev-specialist` (`implement-spec`).
7. **in-test** — `test-specialist` (`run-tests`). A pass report already matched row 4.
8. **done** — Stop. The item is already done.

Reply with the one specialist, the skill they follow, and the backlog id.
