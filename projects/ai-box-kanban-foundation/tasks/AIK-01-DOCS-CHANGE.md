# AIK-01-DOCS — Apply delegated Control boundary wording

**Project:** `ai-box-kanban-foundation`
**Task ID:** `AIK-01-DOCS`
**Status:** `COMPLETE` — remote file readback matched the verified commit; exactly two docs changed; Control checkout is clean.
**Owner:** Eddie
**Manager/executor:** Echo
**Authority:** Eddie delegated wording selection and project management in the project chat.
**Execution:** documentation only; no Codex run is assigned to this task.

## Objective

Apply the verified AIK-01 recommendations that prevent Control's own oversight workflows/backend from being read as ownership of a shared Kanban service or as execution authority from Mind data.

## Exact writable files

In the private `Onoes-Control` repository, and nowhere else:

- `docs/MIND-INTEGRATION-READINESS.md`
- `docs/SECURITY-FOUNDATION.md`

The AI Box project packet may also be updated with this task's evidence, status, receipt, and handoff. No other source file may change.

## Required edits

1. In `MIND-INTEGRATION-READINESS.md`, scope the ownership-table workflow row to **Control-owned oversight workflows/stages, findings, and approvals**, explicitly excluding Mind's internal execution records and any shared task store.
2. Add a clearly labeled shared-task/execution boundary stating:
   - Mind's `work_items` and `stage_executions` are internal execution records, not a qualified general-purpose shared task backend.
   - Relay's current todo/goal displays are not a shared task service; Relay remains a candidate UI.
   - Control approvals are bounded oversight decisions; no Mind record/event or chat message authorizes command execution.
   - The shared task store, authenticated actor identity, board authorization, mutation/removal attribution, and durable feed/replay contract remain separately to be specified and verified.
   - The Control operations freeze and Mind G0–G7 remain open.
3. In `SECURITY-FOUNDATION.md`, clarify that its “one authoritative backend” gate is for Control-owned oversight, approval, command, and audit records only; it does not choose a shared task backend or feed.

## Invariants

- Preserve the frozen operations state, fail-closed command route, existing gates, and all other system behavior.
- No code/config/schema edits, tests requiring services, runtime actions, external integrations, credential access, or scope expansion.
- Do not change `GATE-2-INVARIANTS.md` or `CHAT-PHASE3-LIVE-MESSAGING.md`; AIK-01 found their boundaries adequate for this clarification.

## Branch and verification

- Work from the verified `master` baseline on branch `docs/ai-box-task-boundary`.
- Before committing, prove only the two named Control Markdown files changed; run `git diff --check`, check local Markdown links, inspect the exact diff, and scan those changed docs for credentials/private machine paths.
- Commit/push the branch only after those checks pass. Echo verifies remote readback, then fast-forwards `master` only if it remains at the expected base and no workflow/billing side effect is enabled.
- Record exact commit, changed paths, checks, and source-repo clean status in the project receipt.

## Completion condition

Mark this task complete only after remote file readback matches the verified commit, the branch diff contains exactly the two named docs, and the Control source checkout is clean after promotion. **Complete:** commit `12633d4c2ccc06a7dc9b25af80b56f3e249a05ab`; exactly two files; remote blobs matched; Control worktree clean. AIK-03 is now active as a design-only task.
