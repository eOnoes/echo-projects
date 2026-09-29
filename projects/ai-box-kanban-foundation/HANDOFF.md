# Project Handoff

**Project ID:** `ai-box-kanban-foundation`
**Canonical entrypoint:** `GOAL.md`
**Status:** `READY_FOR_REVIEW`
**Current phase:** 0 — packet prepared
**Current task:** `AIK-01`, awaiting Eddie’s start approval
**Manager:** Echo
**Conditional executor:** Codex

## TL;DR

The first-three project packet is drafted. Dashboard discovery/import is complete, but the app remains blocked from runtime use by its security gate. Control boundary reconciliation and Kanban MVP design remain open. Codex has not been dispatched.

## Last completed action

Created the packet in an isolated clean clone of the public project-control repository. The initial commit and remote readback are still required before Phase 0 is complete.

## Verified context

- Mind’s execution ledger is internal infrastructure, not a qualified shared board.
- Relay is a candidate UI, not an authenticated shared task service yet.
- Control is an oversight/approval surface, not an execution authority from Mind data.
- Onoes-Inference-Control has a sanitized private repo; current security findings block runtime use.

See `EVIDENCE.md` for claim labels and pointers.

## Current blocker

Eddie’s approval is needed to start `AIK-01` (read-only Control document audit).

## Next safe action

Eddie reviews the packet. If approved, Echo dispatches Codex with the exact AIK-01 scope in `CODEX-START-HERE.md` and `AUTHORITY.md`.

## Do not do

- Do not edit Control source docs until Eddie approves proposed wording.
- Do not implement a Kanban backend or modify Relay, Control, or Mind code.
- Do not read or handle credentials; do not rotate the existing key.
- Do not inspect the dirty Mind workspace or legacy dashboard configuration.
- Do not start any service or run live model operations.
- Do not use paid GitHub features or push a task branch directly to `main`.

## Required executor return

Return task ID/status, files changed, exact checks/results, commit/branch, receipt and HTML/Markdown report paths, scope confirmation, blockers, and next action. Echo verifies the remote content before accepting a milestone.
