# Routing

A code task goes through the `route-work` skill before any specialist edits files.

The work record lives in `backlog/BL-NNN.md`, `specs/BL-NNN.md`, and `reports/BL-NNN.md`.

Status order: proposed, ready, in-progress, in-test, done. blocked may be set from any state.

Route: backlog-specialist, then prod-specialist, then dev-specialist, then test-specialist, then backlog-specialist.

## Write boundaries

- Backlog writes only `backlog/`.
- Product writes `specs/` and the status line on that backlog item. Product owns acceptance criteria and what the demo shows. Product does not write application code or tests.
- Development implements one ready spec in ordinary application code and does not change the spec or the tests.
- Testing writes `tests/` and `reports/`. A failure goes back to Development with the failing check named. Testing does not patch application code.

Specialists read `agents/<role>/GOAL.md` and `agents/INTERACTION.md`, and follow the skills named on their agent card.

Skills live only in `.claude/skills/`.
