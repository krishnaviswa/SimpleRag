---
name: implement-spec
description: "Implements one ready spec in ordinary application code, then sets the backlog item to in-test with owner test-specialist. Use when development should build a ready or in-progress item without changing the spec, the tests, or adding an in-app LLM agent."
---

# Implement spec

Implement one spec in ordinary application code. The product is application code. An in-app LLM agent, specialist router, or agent tooling is out of this work.

## Start

Read `backlog/BL-NNN.md` and `specs/BL-NNN.md`.

- Status `ready`: set `status: in-progress` and `owner: dev-specialist` before editing code.
- Status `in-progress`: continue the same spec.
- Any other status: stop.

Move `ready` to `in-progress` first. The handoff to testing is a later step.

## Build

Implement the acceptance criteria and the demo behavior. Check the change with `scope-check`. When something falls outside, stop and say what is out of scope. Leave the spec unchanged.

Edit application code only. The spec, the tests, and backlog text other than `status:` and `owner:` stay as they are.

## Hand off

When implementation is handed to testing, set `status: in-test` and `owner: test-specialist`. Leave tests, test runs, and the done mark to the later roles.
