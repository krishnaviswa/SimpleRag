---
name: run-tests
description: "Runs the project tests and writes reports/BL-NNN.md with pass or fail for each acceptance criterion, sending failures back to development and passes to the backlog specialist. Use when an item is in test and results must be recorded without patching application code."
---

# Run tests

Run the project tests for one backlog item. Record the result. Patching application code to make a test pass is outside this skill. Leave the spec and the tests unchanged except to run them.

## When

Status must be `in-progress` or `in-test`. From `in-progress`, set `status: in-test` and `owner: test-specialist` before running. For any other status, stop.

Run the test command the repository already defines. When no command is defined, run the existing tests under `tests/` with the runner those files already use. When they cannot be executed, mark each acceptance criterion fail and name that reason.

## Report

Write `reports/BL-NNN.md` with one line per acceptance criterion: the criterion text and `pass` or `fail`. On failure, name the failing check in the report. Set `report: reports/BL-NNN.md` on the backlog item.

## Result

Follow `update-status` for the status and owner lines.

- Any failure: set `status: in-progress` and `owner: dev-specialist`. The report names each failing acceptance check.
- All pass: leave `status: in-test`, set `owner: backlog-specialist`, and record the pass in the report. Leave the final `done` mark to the backlog specialist.
