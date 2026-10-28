---
name: update-status
description: "Changes only the status and owner lines on an existing backlog/BL-NNN.md item, and refuses a status skip the handoff rules do not allow. Use when a specialist hands an item to the next role, marks it blocked, or the backlog specialist records done."
---

# Update status

Change only the `status:` and `owner:` lines on an existing `backlog/BL-NNN.md`. Leave the title, `spec:`, `report:`, and request summary as they are. Leave specs, reports, tests, and application code untouched.

Read `agents/INTERACTION.md` first. Refuse any move those rules do not allow. Leave the file unchanged and name the refused move.

## Allowed statuses

`proposed`, `ready`, `in-progress`, `in-test`, `done`, `blocked`.

## Allowed moves

One forward step:

- `proposed` → `ready`, owner `dev-specialist`
- `ready` → `in-progress`, owner `dev-specialist`
- `in-progress` → `in-test`, owner `test-specialist`
- `in-test` → `done`, owner `backlog-specialist`

Also allowed:

- Any status → `blocked`. Keep the current owner unless the caller names one.
- `in-test` → `in-progress`, owner `dev-specialist`, only when `reports/BL-NNN.md` names a failing acceptance check.

Refuse every other move. A skip such as `proposed` → `in-progress`, `ready` → `in-test`, `ready` → `done`, or `in-progress` → `done` is refused. A move out of `blocked` is refused unless `agents/INTERACTION.md` lists that exact move.

## Gates

- `ready` requires `specs/BL-NNN.md` to exist and to contain an acceptance-criteria checklist with at least one item. Otherwise leave the status unchanged.
- `done` requires current status `in-test`, a pass for every acceptance criterion in `reports/BL-NNN.md`, and the backlog specialist as the caller. Testing leaves that mark to the backlog specialist.
