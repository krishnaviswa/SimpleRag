---
name: write-tests
description: "Maps every acceptance criterion in specs/BL-NNN.md to a test under tests/. Use once a backlog item is ready, including before development finishes, when the testing specialist should draft tests without changing application code or the spec."
---

# Write tests

Map every acceptance criterion to a test under `tests/`.

This is allowed once status is `ready`. Status may be `ready`, `in-progress`, or `in-test`. If status is `proposed`, `blocked`, or `done`, stop.

## Tests

Read the acceptance-criteria checklist in `specs/BL-NNN.md`. For each criterion, add a test that fails when that criterion is unmet. Keep the criterion recognizable in the test name.

Cover every criterion. Leave each criterion's meaning intact.

Application code, the spec, and the backlog item stay unchanged.
