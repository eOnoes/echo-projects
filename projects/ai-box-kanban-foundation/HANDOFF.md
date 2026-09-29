# Project Handoff

**Project ID:** `ai-box-kanban-foundation`
**Canonical entrypoint:** `GOAL.md`
**Current status:** `RUNNING` — AIK-01 audit is complete; AIK-01-DOCS applies Eddie-delegated wording to two exact docs.
**Current phase:** 1 — Control boundary reconciliation
**Current task:** `AIK-01-DOCS`, Echo executing documentation-only change
**Manager:** Echo
**Executor:** Echo for AIK-01-DOCS; Codex completed AIK-01

## TL;DR

The verified AIK-01 matrix identified three wording ambiguities. Eddie delegated the decision to Echo. The bounded AIK-01-DOCS task is updating exactly two Control Markdown files; no code or runtime work is in scope.

**Last completed action**

Codex's AIK-01 matrix and receipt are verified. The current AIK-01-DOCS task will apply the delegated wording to exactly two Control documents.

## Verified context

- Mind’s execution ledger is internal infrastructure, not a qualified shared board.
- Relay is a candidate UI, not an authenticated shared task service yet.
- Control is an oversight/approval surface, not an execution authority from Mind data.
- Onoes-Inference-Control has a sanitized private repo; current security findings block runtime use.

See `EVIDENCE.md` for claim labels and pointers.

## Current constraint

Only the two named Control Markdown docs may change under AIK-01-DOCS. The operation freeze, G0–G7 gates, credentials, and AIK-03 queue remain untouched until this documentation gate passes.

## Next safe action

Create the two-file diff, run doc/scope checks, push the task branch, and verify remote readback. Do not begin AIK-03 until this documentation gate is complete.

## Do not do

- Do not edit any Control file outside the two exact Markdown paths authorized by AIK-01-DOCS.
- Do not implement a Kanban backend or modify Relay, Control, or Mind code.
- Do not read or handle credentials; do not rotate the existing key.
- Do not inspect the dirty Mind workspace or legacy dashboard configuration.
- Do not start any service or run live model operations.
- Do not use paid GitHub features or push a task branch directly to `main`.

## Required executor return

Return task ID/status, files changed, exact checks/results, commit/branch, receipt and HTML/Markdown report paths, scope confirmation, blockers, and next action. Echo verifies the remote content before accepting a milestone.
