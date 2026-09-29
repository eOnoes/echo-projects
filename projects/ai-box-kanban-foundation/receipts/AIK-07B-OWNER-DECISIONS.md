# AIK-07B owner decision receipt — D-1–D-5

**Project:** `ai-box-kanban-foundation`
**Decision date:** 2026-09-29
**Status:** All five logical policy directions are recorded; implementation details remain explicitly gated.

## D-1 — authoritative service and operator

Eddie selected one **dedicated logical shared-task service/store**. Echo operates it; Eddie retains owner and policy authority. Database, runtime, hosting, and deployment were not selected or authorized.

## D-2 — identity and board grants

Require verified per-actor identity. Eddie owns board-grant policy; Echo administers grants only under Eddie's direction. Select the concrete identity source during implementation discovery. A shared key must never be treated as an end-actor identity.

## D-3 — removal approvals

A different authorized approver is required; self-approval is prohibited. Record the approval reason and the current task revision.

## D-4 — private-trial retention and recovery

During the private, bounded trial, use no automatic expiry. Retain audit records, tombstones, events, and idempotency outcomes across restart. Restrict removed-history reads to authorized readers. An expired replay cursor requires a fresh snapshot. Choose numeric production retention periods before broader use.

## D-5 — snapshot/feed and fields

Use a JSON board snapshot with an opaque cursor plus ordered durable events/replay. An expired cursor requires a fresh snapshot. Keep `low|normal|high` priority and optional opaque `source_ref`. Choose the transport after stack discovery.

## Scope and remaining gates

These choices authorize updating the design contract and acceptance matrix only. They do not authorize a live service, public exposure, deployment, external data, or production retention policy. The contract must preserve the open technical decisions: database/runtime/hosting, concrete verified identity provider and lifecycle, numeric production retention, and event transport. The revised design must complete three distinct audit/refinement rounds and receive final owner review before any implementation build.
