# Project Handoff

**Project ID:** `ai-box-kanban-foundation`
**Canonical entrypoint:** `GOAL.md`
**Current status:** `RUNNING` — AIK-01 and AIK-01-DOCS complete; AIK-03 in progress.
**Current phase:** Phase 3 — AIK-03 Kanban MVP design.
**Current task:** `AIK-03`, Codex drafting specification/acceptance matrix (`gpt-6-sol`, medium).
**Manager:** Echo
**Executor:** Codex; Echo independently verifies before accepting.

## TL;DR

AIK-01-DOCS clarified the Control boundary in exactly two Markdown files; remote readback and the clean source checkout were verified. Codex (`gpt-6-sol`, medium) is now drafting the AIK-03 Kanban MVP specification and acceptance matrix. No backend or implementation is in scope.

**Last completed action**

Codex's AIK-01 matrix and Echo's source-document verification are complete. The delegated wording change is also complete; see `reports/AIK-01-DOCS-HANDOFF.html` and its receipt.

## Verified context

- Mind’s execution ledger is internal infrastructure, not a qualified shared board.
- Relay is a candidate UI, not an authenticated shared task service yet.
- Control is an oversight/approval surface, not an execution authority from Mind data.
- Onoes-Inference-Control has a sanitized private repo; current security findings block runtime use.

See `EVIDENCE.md` for claim labels and pointers.

## Current constraint

AIK-03 may write only the project packet deliverables named in its task handoff. Do not select a backend, implement code, change Control/Mind/Relay sources, or perform runtime integration.

## Next safe action

Codex will complete AIK-03 with the spec, acceptance matrix, receipt, and HTML handoff. Echo will verify all claims and scope before the next phase. No backend choice, code change, or live integration is authorized.

## Do not do

- Do not edit any Control file outside the two exact Markdown paths authorized by AIK-01-DOCS.
- Do not implement a Kanban backend or modify Relay, Control, or Mind code.
- Do not read or handle credentials; do not rotate the existing key.
- Do not inspect the dirty Mind workspace or legacy dashboard configuration.
- Do not start any service or run live model operations.
- Do not use paid GitHub features or push a task branch directly to `main`.

## Required executor return

Return task ID/status, files changed, exact checks/results, commit/branch, receipt and HTML/Markdown report paths, scope confirmation, blockers, and next action. Echo verifies the remote content before accepting a milestone.
