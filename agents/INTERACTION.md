# Handoff contract

Specialists pass one item through `backlog/BL-NNN.md`. The spec for that item is `specs/BL-NNN.md`. The report for that item is `reports/BL-NNN.md`.

## Status

Status moves in this order:

`proposed` → `ready` → `in-progress` → `in-test` → `done`

`blocked` may be set from any state. The current owner sets `status: blocked` with the update-status skill and leaves `owner` unchanged. The same owner clears `blocked` by restoring the previous status with update-status.

An item is `ready` only when `specs/BL-NNN.md` exists and has acceptance criteria. Development does not start before `ready`.

## Order

Default order is sequential: backlog-specialist, prod-specialist, dev-specialist, test-specialist, then backlog-specialist closes the item or sends it back.

Once status is `ready`, test-specialist may draft tests before dev-specialist finishes. Drafting does not change status or owner. test-specialist still does not change application code.

## Handoff file

```
# BL-001
status: ready
spec: specs/BL-001.md
report:
owner: dev-specialist
```

- `status` is `proposed`, `ready`, `in-progress`, `in-test`, `done`, or `blocked`.
- `spec` is empty, or `specs/BL-NNN.md`.
- `report` is empty, or `reports/BL-NNN.md`.
- `owner` is `backlog-specialist`, `prod-specialist`, `dev-specialist`, or `test-specialist`.

Each role may update the status line only as specified in its goal, using the update-status skill. That same skill is how the role sets `owner` and `report` for a handoff its goal allows. capture-item writes the new handoff file, including its first status line. Later status changes use update-status.

## Transitions

backlog-specialist creates the file with capture-item:

```
# BL-NNN
status: proposed
spec:
report:
owner: prod-specialist
```

prod-specialist sets `ready` only after `specs/BL-NNN.md` exists and has acceptance criteria:

- `status: ready`
- `spec: specs/BL-NNN.md`
- `owner: dev-specialist`

dev-specialist starts the first implementation only from `ready` and sets:

- `status: in-progress`
- `owner: dev-specialist`

When that spec is implemented, dev-specialist sets `owner: test-specialist` and leaves status at `in-progress`.

After a return to development, status is already `in-progress` and owner is already `dev-specialist`. dev-specialist resumes from there. When the spec is implemented again, it sets `owner: test-specialist` and leaves status at `in-progress`.

test-specialist may draft tests once status is `ready`, including while status is `in-progress` and owner is still `dev-specialist`. Drafting does not change status or owner.

test-specialist sets `status: in-test` while running tests. It runs tests only after owner is `test-specialist`.

On test failure, test-specialist sets:

- `status: in-progress`
- `owner: dev-specialist`
- `report: reports/BL-NNN.md`

The report file names the failing acceptance check.

On pass, test-specialist leaves status at `in-test` and sets:

- `owner: backlog-specialist`
- `report: reports/BL-NNN.md`

The report file records the passing result. test-specialist does not set `done`.

Status becomes `done` only when backlog-specialist records the outcome. backlog-specialist does that by setting `status: done`. Owner stays `backlog-specialist`.

backlog-specialist sends the item back, instead of recording `done`, by setting `status: in-progress` and `owner: dev-specialist`. It leaves the `report` line unchanged.

## Product

prod-specialist owns specs, acceptance criteria, and what the demo shows. It writes no application code.
