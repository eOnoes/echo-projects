# AIK-01 — Codex Task Handoff

**Project ID:** `ai-box-kanban-foundation`
**Task:** `AIK-01` — Control boundary document audit
**Status:** `SUBMITTED_FOR_REVIEW` — Codex execution complete; Echo verification passed; Eddie’s wording decision pending.
**Executor:** Codex, `gpt-6-sol`, medium reasoning
**Manager:** Echo
**Owner approval:** received in the project chat.

## Objective

Produce a source-cited contradiction matrix and proposed wording that reconciles Control’s task ownership, persistence, oversight, approval, and execution-authority documents with the supplied read-only Mind audit conclusion.

## Read only these source documents

From the `Onoes-Control` checkout supplied privately by Echo:

- `docs/MIND-INTEGRATION-READINESS.md`
- `docs/SECURITY-FOUNDATION.md`
- `docs/GATE-2-INVARIANTS.md`
- `docs/CHAT-PHASE3-LIVE-MESSAGING.md`

Also read this packet’s `EVIDENCE.md`, `CHARTER.md`, `AUTHORITY.md`, and `PLAN.md`.

If any source file cannot be read, stop and report `BLOCKED` with the exact missing file. Do not fetch another repository or change checkout.

## Audit baseline

Treat these as supplied conclusions, not assumptions to broaden:

- Mind’s current `work_items` / `stage_executions` are internal execution/child-stage records, not a qualified general-purpose Kanban backend.
- The audit did not verify stable authenticated actor identity, board-level authorization, complete mutation/removal attribution, a durable task event feed, or delivery/replay semantics.
- Relay is a candidate task/chat UI; its current todo/goal displays are not a shared task service.
- Control should provide oversight and approvals, not authorize execution from Mind data.
- Mind G0–G7 remain open; no integration/rollout readiness is established.

## Required deliverables

Create only under this project packet:

1. `reports/CONTROL-BOUNDARY-MATRIX.md` — each source statement cited by exact file and section/line range; agreement/conflict; relation to the audit baseline; proposed wording; confidence label (`PROVEN`, `INFERRED`, `UNRESOLVED`, `DISPROVEN`, `BLOCKED`).
2. `receipts/AIK-01.md` — exact files read, checks run, findings, scope check, and unresolved questions.
3. `reports/AIK-01-HANDOFF.md` and `.html` — TL;DR first, drill-down below, exact next decision.
4. Update `TASKS.md`, append evidence to `EVIDENCE.md`, and refresh `HANDOFF.md` with your result. Leave AIK-03 queued.

Do not edit any file in the `Onoes-Control` checkout. Propose text only; Eddie must approve it before a later documentation-edit task.

## Error and stop rules

- Preserve exact command/check failure and affected file; diagnose only within this task scope.
- Fix Markdown/link issues only in the new project deliverables, then rerun the focused check and record exact output.
- Do not skip checks, lower a threshold, erase a failure, or report a source claim without a citation.
- Stop as `BLOCKED` if sources conflict materially, access is denied, a private/secret value appears, the task needs code/service/provider work, or resolving an issue requires a broader scope decision.
- Never inspect credentials or the dirty Mind workspace; do not use live services or contact model runtimes.

## Branch and return contract

Work in branch `codex/AIK-01`. Commit/push only `projects/ai-box-kanban-foundation/**`; never force-push or push to `main`. GitHub billing-safe mode: repository content only; no Actions, Codespaces, Packages, deployments, runners, or paid features.

Return project ID, task ID/status, exact files changed, tests/checks and outputs, branch/commit, receipt/report paths, scope confirmation, blockers, and next safe action. Echo will verify the remote diff and source citations before accepting the milestone.
