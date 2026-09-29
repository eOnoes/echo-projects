# AIK-03 — Codex task handoff

**Project:** `ai-box-kanban-foundation`
**Task:** `AIK-03` — define shared Kanban MVP requirements
**Status:** `COMPLETE_WITH_LIMITATIONS` — Echo verified the branch and a separate `mimo-v2.5` reviewer returned PASS; Eddie's D-1–D-5 remain open.
**Executor:** Codex, `gpt-6-sol`, medium reasoning
**Manager/reviewer:** Echo
**Owner direction:** Eddie asked to work through items 1–3 and delegated project management to Echo.

## Objective

Write a compact, reviewable design for a separate authenticated shared-task contract and an acceptance matrix. Specify **what the MVP must guarantee**, not a backend or implementation.

## Read first

- `GOAL.md`, `CHARTER.md`, `AUTHORITY.md`, `PLAN.md`, `TASKS.md`, `EVIDENCE.md`, `HANDOFF.md`
- `reports/CONTROL-BOUNDARY-MATRIX.md`
- `reports/AIK-01-DOCS-HANDOFF.md`
- The just-completed `receipts/AIK-01-DOCS.md`

Use the updated Control boundary as recorded in the packet; do not modify or fetch the private Control repository for AIK-03.

## Required deliverables

1. `deliverables/KANBAN-MVP.md` — compact spec, target **under 180 lines**. Include:
   - purpose, MVP scope, explicit non-goals, and separation of Mind / Relay / Control roles;
   - task fields and one realistic JSON example;
   - stable authenticated human/agent actor identity and board-scoped authorization; a shared service key or request-supplied actor name is not identity;
   - a small task lifecycle with allowed transitions, including create/edit/complete/reopen and removal-request/removal-approval; avoid a sprawling workflow;
   - mutation attribution, append-only audit history, idempotency and safe duplicate behavior;
   - durable ordered event delivery, opaque cursor, replay/reconnect, retention, and restart recovery;
   - concise open decisions for Eddie; do not silently choose an authoritative backend, identity provider, or storage technology.
2. `reports/KANBAN-ACCEPTANCE-MATRIX.md` — concrete pass/fail scenarios for authorization, attribution, lifecycle, repeated writes, removal approval, missed-event replay, disconnect/restart recovery, and privacy/scope separation.
3. `receipts/AIK-03.md` — exact files read/written, checks, unresolved choices, scope confirmation, and next gate.
4. Update `TASKS.md`, append new evidence IDs in `EVIDENCE.md`, and refresh `HANDOFF.md`.
5. `reports/AIK-03-HANDOFF.md` and `.html` — TL;DR first, drill-down below, exact decisions required.

## Non-goals and hard limits

- No database/backend/vendor choice; leave it explicitly undecided.
- No code, API implementation, Relay UI, Control/Mind source edits, migrations, or runtime testing.
- No live services, model runtimes, credentials, provider/web lookups, paid resources, Actions, deployments, or CI.
- Do not inspect the dirty Mind workspace.
- Keep the spec lean and explicit; this is not an enterprise governance manual.

## Verification and branch

- Work only in `projects/ai-box-kanban-foundation/**` on `codex/AIK-03`.
- Run Markdown link checks and `git diff --check`; verify every proposed criterion maps to an acceptance case and no backend is implicitly selected.
- Scan the packet changes for secrets, private paths, IPs, and unrelated files.
- Commit/push only the scoped project directory; no force-push or main push. Echo independently reviews citations, scope, completeness, and remote readback before acceptance.

## Stop conditions

Stop as `BLOCKED` if a policy or identity decision must be assumed, required context conflicts with E-001–E-003 or the verified Control boundary, the task needs code/runtime work, or the output cannot meet the scope without selecting a backend. Record the exact issue; do not guess or widen scope.
