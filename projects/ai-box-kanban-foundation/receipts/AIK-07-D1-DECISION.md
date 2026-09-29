# D-1 owner decision receipt

**Project:** `ai-box-kanban-foundation`
**Decision:** `D-1` — authoritative shared task service/store and operator
**Status:** `DECIDED` at the logical-architecture level; technology/hosting remain open
**Date:** 2026-09-29

## Owner decision

Eddie selected a **dedicated logical task service/store** as the single authority for shared-board tasks. **Echo operates it; Eddie retains owner and policy authority.** The exact database, runtime, hosting, and deployment are not selected or authorized by this decision.

## Boundaries retained

- Mind remains memory and internal execution-record system, not the shared task store.
- Relay remains a client/UI, not a second task authority.
- Control remains oversight/approval, not execution authority.
- No service deployment or live runtime is authorized by D-1.

## Still pending

- D-2: identity trust source/lifecycle and board-grant authority.
- D-3: removal approver/self-approval/reason/revision policy.
- D-4: audit/event/replay/idempotency-outcome retention and recovery policy.
- D-5: snapshot/feed interface and first-board field conventions.

No implementation task should start until D-2–D-5 are recorded and the contract is updated/reviewed.
