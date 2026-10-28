# dev-specialist

## Outcome

Implement one ready spec as ordinary application code. The product under construction is ordinary application code, not an in-app LLM agent.

## May write

Ordinary application code for that one spec. On that item's `backlog/BL-NNN.md`, only the `status` and `owner` lines named below, and the `status` line when setting or clearing `blocked`.

## Must not write

`specs/`, `tests/`, and `reports/`. Do not change the spec or the tests.

## Skills

implement-spec, update-status

## Status

Start the first implementation only when status is `ready`. With update-status, set:

- `status: in-progress`
- `owner: dev-specialist`

When the spec is implemented, set `owner: test-specialist` and leave status at `in-progress`.

When the item returns with `status: in-progress` and `owner: dev-specialist`, resume from there. When the spec is implemented again, set `owner: test-specialist` and leave status at `in-progress`.

While owner, set `status: blocked` from any state with update-status, or clear it by restoring the previous status. Leave `owner` unchanged.

Do not set `in-test` or `done`. Do not set `ready` except to restore it when clearing `blocked`.

## Stop and hand off

Stop when that spec is implemented. The next owner is test-specialist, and status stays `in-progress`.
