# Project Handoff

**Project ID:** `ai-box-kanban-foundation`
**Canonical entrypoint:** `GOAL.md`
**Current status:** `RUNNING` — AIK-01 submitted for review; it is the sole active task.
**Current phase:** 1 — Control boundary audit
**Current task:** `AIK-01`, assigned to Codex (`gpt-6-sol`, medium reasoning)
**Manager:** Echo
**Executor:** Codex

## TL;DR

Codex prepared the [cited matrix](reports/CONTROL-BOUNDARY-MATRIX.md), [receipt](receipts/AIK-01.md), and [Markdown](reports/AIK-01-HANDOFF.md)/[HTML](reports/AIK-01-HANDOFF.html) review handoffs. Echo must verify citations and branch scope. Eddie must decide the proposed boundary wording. No source edits are authorized.

## Last completed action

AIK-01 read the four approved Control documents and prepared a source-cited matrix under this packet. The matrix limits Control's proposed ownership to its own oversight, approval, chat, command, and audit records; it leaves the shared task store and feed undecided. Phase 0's initial packet publication remains recorded in `EVIDENCE.md` E-009.

## Verified context

- Mind’s execution ledger is internal infrastructure, not a qualified shared board.
- Relay is a candidate UI, not an authenticated shared task service yet.
- Control is an oversight/approval surface, not an execution authority from Mind data.
- Onoes-Inference-Control has a sanitized private repo; current security findings block runtime use.

See `EVIDENCE.md` for claim labels and pointers.

## Current constraint

The proposed wording is not approved. AIK-01 remains submitted for review, and AIK-03 stays queued. The Control operations freeze and Mind G0–G7 remain open.

## Next safe action

Echo verifies the source citations and scoped branch diff. Eddie then approves the proposed boundary wording for a separate exact-file documentation-edit task or supplies a correction. Echo records acceptance before AIK-03 begins.

## Do not do

- Do not edit Control source docs until Eddie approves proposed wording.
- Do not implement a Kanban backend or modify Relay, Control, or Mind code.
- Do not read or handle credentials; do not rotate the existing key.
- Do not inspect the dirty Mind workspace or legacy dashboard configuration.
- Do not start any service or run live model operations.
- Do not use paid GitHub features or push a task branch directly to `main`.

## Required executor return

Return task ID/status, files changed, exact checks/results, commit/branch, receipt and HTML/Markdown report paths, scope confirmation, blockers, and next action. Echo verifies the remote content before accepting a milestone.
