---
name: scope-check
description: "Compares a proposed code or test change to specs/BL-NNN.md and stops when the change falls outside the acceptance criteria or the demo behavior. Use before implementing or adding tests, or when asked whether a change belongs in the current backlog item."
---

# Scope check

Compare a proposed code or test change with `specs/BL-NNN.md`. Leave application code, tests, the spec, and the backlog unchanged.

Read the acceptance criteria, what the demo shows, and out of scope.

Treat the change as in scope only when every part is required by a criterion or by the demo behavior, and no part is listed out of scope.

When any part is outside that boundary, stop. Name the out-of-scope part and the criterion or out-of-scope line it misses. Leave the spec as written.

When the change is in scope, name the criteria it covers. Implementation belongs to `implement-spec`, not this skill.
