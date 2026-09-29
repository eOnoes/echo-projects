# AIK-07B — Apply owner decisions to Kanban contract

**Project:** `ai-box-kanban-foundation`
**Task:** `AIK-07B-KANBAN-CONTRACT-UPDATE`
**Status:** `IN_PROGRESS` — Round 1 Kimi K3 findings are corrected and Echo-verified. Round 2 DSH/Gemini audit returned `PASS` with process limitations recorded in [`receipts/AIK-07B-ROUND-2.md`](../receipts/AIK-07B-ROUND-2.md). Round 3 and Eddie's final approval remain; AIK-08 is blocked.
**Repository:** `eOnoes/echo-projects`, branch `docs/AIK-07B-KANBAN-CONTRACT-UPDATE`
**Base:** `b220c5e02e5451f3ef068ac125d80e913f5581fd`
**Executor:** Codex, `gpt-6-sol`, medium reasoning.

## Read first

1. `CODEX-START-HERE.md`
2. `GOAL.md`, `CHARTER.md`, `AUTHORITY.md`, `PLAN.md`, `TASKS.md`, `EVIDENCE.md`, `HANDOFF.md`, and `PROJECT-OVERRIDES.md`
3. `receipts/AIK-07B-OWNER-DECISIONS.md`
4. `deliverables/KANBAN-MVP.md` and `reports/KANBAN-ACCEPTANCE-MATRIX.md`
5. `reports/KANBAN-DECISION-BRIEF.md` and `.html`

## Objective

Integrate Eddie's five recorded policy directions into one internally consistent design contract and acceptance matrix. Update the owner brief to show these decisions as recorded, while keeping unresolved technical implementation choices explicit. This is design-only; do not build a service or modify any source repository.

## Owner decisions to preserve exactly

Use `receipts/AIK-07B-OWNER-DECISIONS.md` as the authority:

- D-1: dedicated logical task service/store; Echo operates; Eddie retains owner/policy authority. Database, runtime, hosting, and deployment remain undecided and unauthorized.
- D-2: verified per-actor identity; Eddie owns board-grant policy; Echo administers grants only under Eddie's direction; concrete identity source is selected during implementation discovery; shared keys are not end-actor identity.
- D-3: distinct authorized approver; no self-approval; record reason and current task revision.
- D-4: private bounded trial only; no automatic expiry; retain audit, tombstones, events, and idempotency outcomes across restart; removed-history reads only by authorized readers; expired cursor forces a fresh snapshot; numeric production retention must be chosen before broader use.
- D-5: JSON snapshot with opaque cursor; ordered durable event/replay; expired cursor requires fresh snapshot; keep `low|normal|high` and optional opaque `source_ref`; transport is selected after stack discovery.

## Allowed writes — only these paths

- `deliverables/KANBAN-MVP.md`
- `reports/KANBAN-ACCEPTANCE-MATRIX.md`
- `reports/KANBAN-DECISION-BRIEF.md`
- `reports/KANBAN-DECISION-BRIEF.html`
- `receipts/AIK-07B-OWNER-DECISIONS.md`
- `receipts/AIK-07B-CONTRACT-UPDATE.md` (create)
- `tasks/AIK-07B-KANBAN-CONTRACT-UPDATE.md`
- `TASKS.md`, `PLAN.md`, `EVIDENCE.md`, `HANDOFF.md`, `CODEX-START-HERE.md`, `GOAL.md`, `CHARTER.md`, `CLOSURE.md`

No other files. Do not edit Doctrine or any source/application repository.

## Constraints

- Do not choose a database, runtime, hosting, deployment target, identity provider, numeric production-retention period, or event transport by assumption. Mark each unresolved item as a later evidence-backed implementation decision.
- Keep D-4's no-expiry policy confined to a private, bounded trial; do not imply suitability for production or public/multi-user deployment.
- Preserve one authority: Mind remains memory/internal execution records; Relay is a client; Control is oversight/approval only.
- No code, migration, service, credentials, external data, runtime, deployment, public exposure, or real user data.
- Do not perform any of the three design-audit rounds in this task. The manager will launch each distinct round separately against a frozen, reviewed document state.
- If a decision cannot be represented without adding an unapproved policy, stop and report the exact gap rather than inventing one.

## Acceptance

1. The contract and matrix state D-1–D-5 as owner-decided and map each choice to observable acceptance cases.
2. Every technical detail intentionally left open is clearly named with its evidence/approval gate; no accidental default is introduced.
3. D-4 explicitly separates private-trial retention from numeric production retention and marks production use blocked until the latter is approved.
4. The owner brief (Markdown and HTML) reflects current decision state and links the decision receipt.
5. Task ledger, plan, evidence, handoff, and closure accurately distinguish this contract-update task from the still-blocked implementation task.
6. Markdown relative links resolve, HTML parses, `git diff --check` passes, and a public-content scan finds no secrets or local absolute paths.
7. Commit and push only this task branch. Report exact changed paths, commit, checks, unresolved technical choices, and confirm no source/service/runtime work occurred.

Do not merge this branch into `main`; Echo will review and promote only after verification.

## Execution record

The design update is recorded in [the task receipt](../receipts/AIK-07B-CONTRACT-UPDATE.md). No audit round was run. AIK-08 remains blocked pending three distinct audits, final owner approval, and separately scoped implementation discovery.
