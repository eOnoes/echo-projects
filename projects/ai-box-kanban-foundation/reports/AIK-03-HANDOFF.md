# AIK-03 — Review handoff

## TL;DR

The shared Kanban MVP contract and A-01–A-12 acceptance cases are drafted for Echo's independent review. They require authenticated board-scoped actors, attributed and idempotent writes, approval-controlled removal, and a durable replayable feed. This is a design proposal; no service, source edit, backend selection, or runtime claim follows from it. AIK-03 is `READY_FOR_REVIEW`, not accepted or complete.

## What to review

- [Compact contract](../deliverables/KANBAN-MVP.md): task shape, minimal lifecycle, authorization, audit, retry, and event recovery guarantees.
- [Acceptance matrix](KANBAN-ACCEPTANCE-MATRIX.md): A-01–A-12 observable pass/fail cases and coverage map.
- [Receipt](../receipts/AIK-03.md): exact input/output files, checks, and scope.

## Exact decisions required from Eddie

1. **D-1:** Name the future authoritative shared task service/store and operator. The packet does not qualify Mind, Relay, or Control for this role.
2. **D-2:** Choose the trusted identity source and lifecycle for human/agent IDs, plus the authority that grants and revokes board roles.
3. **D-3:** Set removal approver eligibility, self-approval policy, and reason/revision requirements. No removal approval implementation is authorized while these remain open.
4. **D-4:** Set audit/event retention and replay window, expired-cursor handling, and access to removed-task history.
5. **D-5:** Choose snapshot/feed interface semantics and confirm priority/source-reference conventions for the first board.

## Gate and next action

Echo should verify the remote branch's exact project-scoped diff, links, coverage, and evidence claims, then request an independent review. Eddie can decide D-1–D-5 after seeing findings. A separate authorized packet is required for implementation. Control's operations freeze and Mind G0–G7 remain open; Onoes-Inference-Control's security gate is separate.
