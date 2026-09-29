# AIK-07B Round 1 remediation receipt

**Status:** `COMPLETE_WITH_LIMITATIONS` — five scoped corrections are applied and Echo-verified at Codex commit `7020c77469585062ed6fe9136a0dbf4c8e7f2e53`. Remote branch/ref and all 11 changed blobs match; Round 2 has not started.
**Branch:** `docs/AIK-07B-KANBAN-CONTRACT-UPDATE`
**Starting HEAD:** `8d8589b476f6f3d60a7084dd3f18a717874670a1`
**Audit-bound ancestor:** `13ebdac3691edb27fdebe1bbfc6c5961955afb02`

## Corrections

- **R1-01:** The contract limits Control to authorized task/history/approval reads and an eligible removal-approval decision. General task writes are absent from its surface. Relay ordinary task operations remain subject to verified actor and board grants. A-13 states observable pass and fail outcomes.
- **R1-02:** A stale approval decision conflicts, leaves the request pending, and requires refresh/re-review before a new decision. A-15 matches.
- **R1-03:** Distinct new requests against a pending request or removed task conflict without duplicate request, decision, tombstone, or task-change event. The original idempotency-key/payload retry retains its original outcome. A-08 matches.
- **R1-04:** The full Queue table has consistent Markdown cells and a row for this remediation; AIK-07B remains `NEEDS_CHANGES`, R1 remediation is `COMPLETE_WITH_LIMITATIONS`, and AIK-08 is `BLOCKED`.
- **R1-05:** Private-trial audit, tombstone, event, and idempotency records have no automatic or age-based expiry. Invalid, unknown, or explicitly invalidated cursors require a fresh snapshot without implying record deletion. The exact invalidation trigger remains open. A-11, A-16, and A-18 align.

## Scope and verification

Changed paths, all under `projects/ai-box-kanban-foundation/`: `CLOSURE.md`, `CODEX-START-HERE.md`, `EVIDENCE.md`, `GOAL.md`, `HANDOFF.md`, `PLAN.md`, `TASKS.md`, `deliverables/KANBAN-MVP.md`, `reports/KANBAN-ACCEPTANCE-MATRIX.md`, `tasks/AIK-07B-R1-REMEDIATION.md`, and this receipt. Codex's correction commit was `7020c77469585062ed6fe9136a0dbf4c8e7f2e53`; this receipt records Echo's independent verification of that commit.

Changed paths are exactly the 11 paths listed above; Codex commit `7020c77469585062ed6fe9136a0dbf4c8e7f2e53` was pushed to `docs/AIK-07B-KANBAN-CONTRACT-UPDATE`. Echo fetched that ref, confirmed it resolves to the same SHA, and matched the remote blobs for every changed path. Independent checks: `git diff --check`; 13 Queue table lines (header and delimiter included), all 5 columns consistent with no malformed leading pipes; queue statuses reflect contract `NEEDS_CHANGES`, remediation complete, AIK-08 blocked; JSON example parses; A-01–A-18 present in order; all relative Markdown links in changed files resolve; the frozen Round 1 report/receipt are not changed. No implementation, runtime behavior, or source repository was tested or modified.

The frozen Round 1 audit report, audit receipt, owner-decision receipt, Charter, and Authority were not edited. These are design-document corrections only; no implementation, source repository, service, runtime, deployment, or Round 2 work occurred. A-01–A-18 are future acceptance cases; no runtime behavior was tested.

Open technical choices remain database/runtime/hosting/deployment, concrete verified identity source and lifecycle, numeric production retention and capacity/deletion behavior, event transport, snapshot/cursor handoff mechanics, and the exact cursor invalidation trigger. Two further distinct audit/refinement rounds and Eddie's final design approval precede AIK-08.
