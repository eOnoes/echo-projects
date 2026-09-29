# Kanban D-1–D-5 — owner decision brief

**Current status:** Eddie recorded all five policy directions on 2026-09-29 in the [owner decision receipt](../receipts/AIK-07B-OWNER-DECISIONS.md). The [contract](../deliverables/KANBAN-MVP.md) and [acceptance matrix](KANBAN-ACCEPTANCE-MATRIX.md) integrate them for review. This brief authorizes no implementation.

## Phone summary

One dedicated logical shared-task service/store is the task authority. Echo operates it; Eddie retains owner and policy authority. Verified per-actor identity and Eddie-directed board grants govern access. Removal needs a different authorized approver, reason, and current task revision. A private bounded trial has no automatic expiry of audit, tombstone, event, or idempotency records. Clients use a JSON snapshot with opaque cursor and ordered durable replay. Three separate design-audit rounds and Eddie's final approval still precede implementation.

## Recorded decisions and observable checks

| Gate | Owner direction | Acceptance case |
|---|---|---|
| D-1 | Dedicated logical task service/store; Echo operates; Eddie retains policy authority. | [A-12–A-13](KANBAN-ACCEPTANCE-MATRIX.md): one authority; Mind remains memory/internal execution records, Relay a client, Control oversight/approval only. |
| D-2 | Verified per-actor identity; Eddie owns board-grant policy, Echo administers grants only under his direction; shared keys are not end-actor identity. | [A-01–A-03, A-14](KANBAN-ACCEPTANCE-MATRIX.md): forgery, cross-board denial, directed grants and revocation. |
| D-3 | Distinct authorized removal approver; no self-approval; record reason and current task revision. | [A-08, A-15](KANBAN-ACCEPTANCE-MATRIX.md): bypass, self-approval and stale revision deny. |
| D-4 | Private bounded trial only: no automatic expiry; audit, tombstones, events, idempotency outcomes survive restart; removed-history reads require authorization; expired cursor forces fresh snapshot. | [A-06, A-10–A-11, A-16–A-17](KANBAN-ACCEPTANCE-MATRIX.md): recovery, privacy, expiry and broader-use gate. |
| D-5 | JSON snapshot with opaque cursor; ordered durable events/replay; expired cursor requires fresh snapshot; `low|normal|high` and optional opaque `source_ref`. | [A-09–A-11, A-18](KANBAN-ACCEPTANCE-MATRIX.md): format, ordered recovery, fields and no cursor/reference authority. |

## Technical choices and gates

| Choice left open | Required evidence and approval gate |
|---|---|
| Database, runtime, hosting, deployment | Implementation discovery must demonstrate durability, recovery, authority separation, and operability; select and authorize the stack separately. No platform is presumed. |
| Concrete verified identity source and lifecycle | Discovery must show stable human/agent identity, enrollment, revocation, and service-actor handling against D-2 and the matrix; Eddie approves the proposed identity approach. |
| Numeric production retention and capacity/deletion behavior | Before broader, public, multi-user, or production use, provide evidence for audit, tombstone, event/replay, and idempotency periods and obtain Eddie's approval. The private-trial no-expiry rule does not establish a production policy. |
| Event transport and snapshot/cursor handoff mechanics | Select after stack discovery; demonstrate consistent snapshot, ordered durable replay, authorization, restart recovery, and expiry behavior against the matrix, then review the chosen interface. |

The sequential design review/refinement work is recorded: Round 1 findings were corrected, and Rounds 2–3 returned `PASS` with limitations recorded in their receipts. Eddie's final approval and a separately scoped implementation task remain before AIK-08. No source, service, runtime, or deployment work is authorized.
