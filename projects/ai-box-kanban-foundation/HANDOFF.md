# Project Handoff

**Project ID:** `ai-box-kanban-foundation`
**Canonical entrypoint:** `GOAL.md`
**Current status:** `RUNNING` — AIK-01 is the sole active task.
**Current phase:** 1 — Control boundary audit
**Current task:** `AIK-01`, assigned to Codex (`gpt-6-sol`, medium reasoning)
**Manager:** Echo
**Executor:** Codex

## TL;DR

Eddie approved AIK-01. Codex (`gpt-6-sol`, medium reasoning) is assigned the read-only Control-doc audit. No source edits are authorized.

## Last completed action

Published the initial packet commit `c975022afe3080f7e6bf1c6b612ed242acffd271` to public `main`. Remote readback confirmed the commit and all 12 project-packet files; staged scope, links, diff checks, secret scan, and path scan passed. The canonical dirty clone and its unrelated work remain untouched. Phase 0 is complete.

## Verified context

- Mind’s execution ledger is internal infrastructure, not a qualified shared board.
- Relay is a candidate UI, not an authenticated shared task service yet.
- Control is an oversight/approval surface, not an execution authority from Mind data.
- Onoes-Inference-Control has a sanitized private repo; current security findings block runtime use.

See `EVIDENCE.md` for claim labels and pointers.

## Current constraint

No source edits are authorized. Codex must return the cited matrix and proposed wording; Echo will verify them before AIK-01 is accepted.

## Next safe action

Codex completes AIK-01 by returning the cited matrix, proposed wording, receipt, and scoped branch. Echo verifies the citations/diff before the task closes.

## Do not do

- Do not edit Control source docs until Eddie approves proposed wording.
- Do not implement a Kanban backend or modify Relay, Control, or Mind code.
- Do not read or handle credentials; do not rotate the existing key.
- Do not inspect the dirty Mind workspace or legacy dashboard configuration.
- Do not start any service or run live model operations.
- Do not use paid GitHub features or push a task branch directly to `main`.

## Required executor return

Return task ID/status, files changed, exact checks/results, commit/branch, receipt and HTML/Markdown report paths, scope confirmation, blockers, and next action. Echo verifies the remote content before accepting a milestone.
