# Kanban D-1–D-5 — owner decision brief

**Status:** recommendation only. **D-1: PENDING · D-2: PENDING · D-3: PENDING · D-4: PENDING · D-5: PENDING.** Eddie has made none of these choices in this packet.

## Phone TL;DR

The reviewed contract describes one authenticated, board-scoped task authority with durable history and replay. No current Mind, Relay, or Control component is qualified as that authority. To unlock a first testable slice, Eddie needs to name the single service/store and operator; identify the trusted actor and board-grant authority; set removal approval policy; set audit, event, replay, and idempotency-outcome retention; and choose the snapshot/feed interface plus priority/source-reference conventions. The packet supports design recommendations below, but contains no qualified backend, identity provider, numeric retention period, or transport choice. [Contract](../deliverables/KANBAN-MVP.md) · [Acceptance matrix](KANBAN-ACCEPTANCE-MATRIX.md) · [Evidence E-019–E-024](../EVIDENCE.md)

## Minimal owner answer set

| Gate | Answer Eddie needs to supply | Current state |
|---|---|---|
| D-1 | Which **one** service/store is authoritative, and who operates it? | **PENDING** |
| D-2 | What verified identity source/lifecycle supplies stable human and agent IDs, and who grants/revokes board read, write, and approve-removal roles? | **PENDING** |
| D-3 | Who may approve removal, may a requester approve their own request, and what reason/revision rule applies? | **PENDING** |
| D-4 | How long are audit/history, events, replay cursors, removed-task history, and idempotency outcomes retained; who may read removed-task history; what is the expired-cursor policy? | **PENDING** |
| D-5 | What concrete snapshot/feed interface and handoff will clients use, and are `low|normal|high` priority and optional opaque `source_ref` suitable for the first board? | **PENDING** |

These answers select policy and implementation scope; this brief does not record an owner decision. Any candidate technology or numerical period needs evidence and an explicit owner choice before implementation. [Contract, “Threat notes and decisions”](../deliverables/KANBAN-MVP.md) · [Review note E-024](../EVIDENCE.md)

## D-1 — authoritative service/store and operator · **PENDING**

**Exact choice.** Name the single authoritative shared task service/store and its operator. **Packet fact.** Mind's internal execution records, Relay's todo/goal display, and Control's oversight records are not qualified as the shared board. The contract requires a durable task/history/idempotency/event authority; the charter bars a competing store. [Contract, “Purpose and boundary” and “Event feed and recovery”](../deliverables/KANBAN-MVP.md) · [Charter, items 4–6](../CHARTER.md) · [E-001–E-003, E-019](../EVIDENCE.md)

**Supported options.** Establish a separate service/store for the reviewed contract; or qualify a proposed existing component against that same contract and acceptance matrix before naming it. The packet supports neither a named candidate nor a product comparison. **Recommendation.** Favor one dedicated logical task authority with a named operator, while leaving technology and hosting open. This keeps Relay a client and Control an oversight consumer; it adds an operated component and requires durability/recovery evidence. **Unknown.** Candidate service, store, operator, operational capacity, and qualification evidence. **Unblocks.** A bounded service implementation task and the later Relay/Control adapters, after the other gates and contract review. [Contract](../deliverables/KANBAN-MVP.md) · [Matrix A-09–A-12](KANBAN-ACCEPTANCE-MATRIX.md) · [Plan, phases 7–8](../PLAN.md)

## D-2 — identity and grants · **PENDING**

**Exact choice.** Name the trust source and lifecycle for stable human/agent IDs and the authority that grants/revokes board roles. **Packet fact.** The service must derive actor IDs from verified authentication, check current board grants on each operation and replay, and reject a request-supplied name or shared key as end-actor identity. [Contract, “Task and actor contract”](../deliverables/KANBAN-MVP.md) · [Matrix A-01–A-04](KANBAN-ACCEPTANCE-MATRIX.md)

**Supported options.** Verified end-actor context for each request; or separately identified service actors limited to their own grants where end-actor context cannot be preserved. The packet does not qualify any identity provider or grant administrator. **Recommendation.** Require verified per-actor identity, an explicitly named board-grant authority, and per-operation grant checks. Preserve the verified end actor through adapters when possible. This gives auditable attribution but requires identity lifecycle and grant-revocation work. **Unknown.** Provider, enrollment/deprovisioning process, grant issuer, and service-actor use cases. **Unblocks.** Authenticated board access and attribution tests for the first slice. [Contract, “Task and actor contract”](../deliverables/KANBAN-MVP.md) · [Matrix A-01–A-04](KANBAN-ACCEPTANCE-MATRIX.md)

## D-3 — removal approval · **PENDING**

**Exact choice.** Set approver eligibility, self-approval rule, and reason/revision rule. **Packet fact.** A removal request stays pending without changing task status; approval of a valid pending request creates a tombstone, while denial preserves the task. The reviewed revision must be recorded. Until policy is chosen, no removal-approval implementation is authorized. [Contract, “Task and actor contract,” “Lifecycle and writes,” and D-3](../deliverables/KANBAN-MVP.md) · [Matrix A-08](KANBAN-ACCEPTANCE-MATRIX.md)

**Supported options.** Allow or forbid self-approval for an actor with the approve-removal grant; require a reason and current reviewed revision, or define a different explicit reason/revision rule. **Recommendation.** Use a distinct approver, a recorded reason, and approval only at the reviewed current revision. This limits accidental or stale removal but adds a second actor and may slow the first removal workflow. **Unknown.** Eligible approvers, whether requester/approver separation is feasible, required reason content, and the exact revision policy. **Unblocks.** Removal request, denial, approval, tombstone, and bypass tests; until then, keep approval out of the first implementation task. [Contract](../deliverables/KANBAN-MVP.md) · [Matrix A-08](KANBAN-ACCEPTANCE-MATRIX.md)

## D-4 — retention and recovery · **PENDING**

**Exact choice.** Set audit/event retention, replay window, expired-cursor behavior, access to removed-task history, and idempotency-outcome retention/expiry. **Packet fact.** Committed tasks, audit entries, outcomes, and events must survive restart; an expired cursor must explicitly require a fresh consistent snapshot, never silently skip events. E-024 adds idempotency outcomes to the owner retention decision. [Contract, “Lifecycle and writes” and “Event feed and recovery”](../deliverables/KANBAN-MVP.md) · [Matrix A-06, A-09–A-11](KANBAN-ACCEPTANCE-MATRIX.md) · [E-024](../EVIDENCE.md)

**Supported options.** Choose a longer or shorter explicit retention/replay window and an explicit removed-history reader policy; both require acceptance against restart, retry, and expiry cases. **Recommendation.** Keep audit and tombstone evidence available to authorized readers; retain idempotency outcomes through the chosen retry window across restart; make cursor expiry an explicit resnapshot result. This favors recoverability and reviewability but increases retained data and storage/operations burden. **Unknown.** Every numeric period, retry horizon, storage capacity, deletion policy, and which granted actors may read removed-task history. **Unblocks.** Deterministic retry, restart, replay, and expiry tests. No period is selected here. [Contract](../deliverables/KANBAN-MVP.md) · [Matrix A-06, A-10–A-11](KANBAN-ACCEPTANCE-MATRIX.md)

## D-5 — snapshot/feed and field conventions · **PENDING**

**Exact choice.** Choose the concrete snapshot/feed interface and handoff mechanics; confirm priority and source-reference conventions for the first board. **Packet fact.** The contract requires a consistent board snapshot with cursor, ordered durable events after it, board-scoped authorization on replay, deduplication, and opaque cursors. Its proposed fields are `low|normal|high` priority and optional opaque `source_ref`, which grants no authority. The packet selects no protocol. [Contract, “Task and actor contract” and “Event feed and recovery”](../deliverables/KANBAN-MVP.md) · [Matrix A-02, A-09–A-12](KANBAN-ACCEPTANCE-MATRIX.md)

**Supported options.** Expose the snapshot/feed through one documented interface chosen by Eddie; retain the proposed three-level priority and opaque optional reference, or revise them explicitly before implementation. No transport candidate is qualified in the packet. **Recommendation.** Keep the proposed field conventions for the first board and require one snapshot/cursor handoff with ordered replay and explicit resnapshot on expiry. This keeps the initial model small but limits priority expressiveness and leaves transport and handoff mechanics to be specified. **Unknown.** Protocol, exact handoff, client acknowledgement, first-board vocabulary, and source-reference format. **Unblocks.** Snapshot/replay client work and A-09–A-11 recovery tests. [Contract](../deliverables/KANBAN-MVP.md) · [Matrix A-09–A-12](KANBAN-ACCEPTANCE-MATRIX.md)

## First testable slice after the gates

Once Eddie resolves D-1–D-5 and a separate implementation task is scoped, test **one board, one authoritative task store, verified human/agent actors, board read/write grants, create/read/edit/complete/reopen, append-only attribution, idempotent retry across restart, a consistent snapshot, and ordered replay after disconnect**. Include denial and cross-board fixtures so the slice demonstrates the contract's authority boundary. Use A-01–A-07 and A-09–A-12 as observable checks; add A-08 when D-3 authorizes the removal path. Relay may later present the same service; Control may later show oversight and approvals. Neither creates a second task authority or execution permission. This is a proposed test scope, not authorization to implement or a claim of passing tests. [Contract](../deliverables/KANBAN-MVP.md) · [Matrix](KANBAN-ACCEPTANCE-MATRIX.md) · [Charter, items 4–6](../CHARTER.md) · [Plan, phases 7–8](../PLAN.md)
