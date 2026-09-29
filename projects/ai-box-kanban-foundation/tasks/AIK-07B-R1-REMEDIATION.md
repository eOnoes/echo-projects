# AIK-07B Round 1 finding remediation

**Project:** `ai-box-kanban-foundation`
**Task:** `AIK-07B-R1-REMEDIATION`
**Status:** `RUNNING` — scoped packet corrections applied for Echo verification; Round 2 has not started.
**Repository/branch:** `eOnoes/echo-projects`, `docs/AIK-07B-KANBAN-CONTRACT-UPDATE`
**Base:** `13ebdac3691edb27fdebe1bbfc6c5961955afb02`
**Executor:** Codex `gpt-6-sol`, medium reasoning.

## Read first

1. `CODEX-START-HERE.md`
2. `AUTHORITY.md`
3. `receipts/AIK-07B-OWNER-DECISIONS.md`
4. `receipts/AIK-07B-ROUND-1.md`
5. `reports/AIK-07B-ROUND-1-AUDIT.md`
6. `deliverables/KANBAN-MVP.md`
7. `reports/KANBAN-ACCEPTANCE-MATRIX.md`
8. `TASKS.md`

## Mission

Apply only the five Round 1 corrections below to the design packet, preserve every D-1–D-5 owner decision and every technical option explicitly left open, and record an execution receipt. This is still design-only. Do not run Round 2; Echo will freeze/hash the revised packet and dispatch it separately.

## Accepted corrections

1. **R1-01 — Control surface boundary:** Follow Charter item 6. In this MVP the Control surface may read task/history/approval views and submit only a removal-approval decision when the authenticated actor holds the current `approve-removal` grant and satisfies separation of duties. It must not expose general task create/edit/complete/reopen/removal-request writes. Relay remains a client for ordinary task operations, always subject to verified actor and board grants. Update normative contract and A-13 with explicit observable pass/fail outcomes. Do not create a second task authority or execution permission.
2. **R1-02 — stale approval:** Normatively require an approval decision against a non-current task revision to fail with a conflict, leave the removal request pending, and require refresh/re-review before a new decision. Update A-15 consistently.
3. **R1-03 — duplicate/invalid removal requests:** A new removal request against an already-removed task and a distinct new request while an existing request is `pending` both fail with a conflict and do not create a second request, decision, tombstone, or task-change event. A retry with the original idempotency key/payload still returns its original outcome under existing idempotency rules. Add these cases to A-08.
4. **R1-04 — queue-table syntax:** Repair the full `TASKS.md` Queue table so every header, delimiter, and body row is a valid, consistent Markdown table; do not leave `||`/`|||` leading-pipe corruption.
5. **R1-05 — cursor vs record expiry:** Preserve D-4's no-automatic-expiry rule for audit, tombstones, events, and idempotency outcomes during the private bounded trial. Clarify that an invalid/unknown/explicitly invalidated cursor may require a fresh snapshot, but this does not imply retained history was automatically deleted. No age-based trial expiry may be introduced. The exact cursor invalidation trigger remains an implementation-discovery decision. Align A-11/A-16 with that distinction; do not claim actual expiry behavior was runtime-tested.

## Allowed changes

- `deliverables/KANBAN-MVP.md`
- `reports/KANBAN-ACCEPTANCE-MATRIX.md`
- `TASKS.md`
- `PLAN.md`, `GOAL.md`, `HANDOFF.md`, `CLOSURE.md`, `EVIDENCE.md`, `CODEX-START-HERE.md` (status/evidence/handoff consistency only)
- `tasks/AIK-07B-KANBAN-CONTRACT-UPDATE.md`
- `tasks/AIK-07B-R1-REMEDIATION.md`
- New `receipts/AIK-07B-R1-REMEDIATION.md`
- `reports/KANBAN-DECISION-BRIEF.md` and `.html` only if required to keep the summary consistent; if one changes, update both and preserve valid links/HTML.

Do not modify the frozen Round 1 report or audit receipt. Do not change the owner decision receipt, D-1–D-5 policy, project Charter, Authority, or any source repository. Do not use external sources, select database/runtime/hosting/identity provider/transport, or authorize implementation. If any correction appears to require a new owner policy decision, stop and report the exact question.

## Acceptance

- The contract and matrix explicitly resolve R1-01 through R1-05 without conflicting with the owner-decision receipt or Charter.
- A-01–A-18 remain covered; new cases are added where required without claiming tests were run.
- Queue table parses as a consistent Markdown table; relative links resolve; JSON example remains valid; changed HTML parses and matches Markdown if touched.
- Update the task queue to show `AIK-07B` as `NEEDS_CHANGES`, the current R1 remediation as `READY`/`RUNNING` according to actual process state, and AIK-08 still `BLOCKED`. Repair all malformed pipe syntax in the Queue table.
- Record exact changed paths, commit, checks, open technical choices, and confirmation of no implementation/source/runtime work.
- Run `git diff --check`; commit and push only this branch. Do not merge `main`.

## Execution status

R1-01–R1-05 are recorded in the revised contract, matrix, and Queue table. The [remediation receipt](../receipts/AIK-07B-R1-REMEDIATION.md) records scope and local acceptance checks. Echo verification, Rounds 2–3, and final owner approval remain pending; AIK-08 stays blocked.
