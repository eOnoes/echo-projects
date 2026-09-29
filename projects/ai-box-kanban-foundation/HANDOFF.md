# Project Handoff

**Project ID:** `ai-box-kanban-foundation`
**Canonical entrypoint:** `GOAL.md`
**Current status:** `RUNNING` — AIK-01 and AIK-01-DOCS complete; AIK-03 draft ready for Echo review.
**Current phase:** Phase 3 — AIK-03 Kanban MVP design.
**Current task:** `AIK-03`, `READY_FOR_REVIEW` on `codex/AIK-03` (`gpt-6-sol`, medium).
**Manager:** Echo
**Executor:** Codex; Echo independently verifies before accepting.

## TL;DR

The AIK-03 spec and acceptance matrix are drafted for Echo's independent review. They define authenticated board actors, task lifecycle, mutation history, safe retries, and recoverable events without choosing an authoritative store or identity provider. Eddie must resolve D-1–D-5 before implementation scope is opened. See the [AIK-03 Markdown handoff](reports/AIK-03-HANDOFF.md) and [HTML handoff](reports/AIK-03-HANDOFF.html).

**Last completed action**

Codex drafted the AIK-03 contract, A-01–A-12 acceptance cases, and task receipt under the project packet. AIK-01-DOCS remains the last independently accepted milestone.

## Verified context

- Mind’s execution ledger is internal infrastructure, not a qualified shared board.
- Relay is a candidate UI, not an authenticated shared task service yet.
- Control is an oversight/approval surface, not an execution authority from Mind data.
- Onoes-Inference-Control has a sanitized private repo; current security findings block runtime use.

See `EVIDENCE.md` for claim labels and pointers.

## Current constraint

AIK-03 remains design-only. Its proposed contract is not evidence of implementation or runtime qualification. Control's operation freeze and Mind G0–G7 remain open.

## Next safe action

Echo reads back the scoped branch, checks the draft and acceptance coverage independently, and records review findings. Eddie then decides D-1–D-5 or explicitly keeps them open. No backend choice, code change, or live integration follows automatically.

## Do not do

- Do not edit any Control file outside the two exact Markdown paths authorized by AIK-01-DOCS.
- Do not implement a Kanban backend or modify Relay, Control, or Mind code.
- Do not read or handle credentials; do not rotate the existing key.
- Do not inspect the dirty Mind workspace or legacy dashboard configuration.
- Do not start any service or run live model operations.
- Do not use paid GitHub features or push a task branch directly to `main`.

## Required executor return

Return task ID/status, files changed, exact checks/results, commit/branch, receipt and HTML/Markdown report paths, scope confirmation, blockers, and next action. Echo verifies the remote content before accepting a milestone.
