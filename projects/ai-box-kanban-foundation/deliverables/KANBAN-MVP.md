# Shared Kanban MVP contract — AIK-07B

**Status:** D-1–D-5 owner directions integrated for three distinct audit/refinement rounds and final owner review. Design only; no implementation authorized. [Owner decisions](../receipts/AIK-07B-OWNER-DECISIONS.md).

## Purpose and boundary

Provide one authenticated, board-scoped task record and recoverable change feed for humans and agents. One dedicated logical shared-task service/store is authoritative; Echo operates it, while Eddie retains owner and policy authority. Database, runtime, hosting, and deployment remain unselected and unauthorized. Mind retains memory and internal `work_items` / `stage_executions`, which are not the shared board. Relay may present the board after integration is separately authorized; its current todo/goal displays are not the service. Control owns oversight, findings, and bounded approvals; its records and chat do not create task authority or authorize execution. Onoes-Inference-Control is a separate dashboard with an open security gate.

MVP includes task create, edit, complete, reopen, removal request/approval, authorized reads, immutable history, and a replayable board feed. It does not include execution commands, automatic task creation from Mind or chat, multi-board moves, dependencies, comments, attachments, notifications, or Relay/Control UI changes. It does not choose an event transport, API protocol, database, runtime, hosting, deployment, vendor, concrete identity source, or storage technology.

## Task and actor contract

Each task has: `id` (stable opaque identifier), `board_id`, `project_scope` (board-local project/scope label), `title`, optional `body`, `status`, `priority` (`low|normal|high`), optional `source_ref` (opaque reference, never authority), `creator_actor_id`, optional `assignee_actor_id`, `created_at`, `updated_at`, optional `completed_at`, and monotonic `revision`. A removal request has its own requester, time, reason, and pending state; approval records a distinct authorized decision actor, decision time, reason, and the current task revision reviewed. Timestamps are service-assigned UTC values. Removed tasks retain a tombstone, history, and feed event; removal hides normal board listing but does not erase audit evidence.

```json
{
  "id": "task_7K2Q",
  "board_id": "board_ops",
  "project_scope": "AI box planning",
  "title": "Review shared board recovery contract",
  "body": "Check replay after a client disconnect and service restart.",
  "status": "open",
  "priority": "normal",
  "source_ref": "planning-note-17",
  "creator_actor_id": "actor_h_42",
  "assignee_actor_id": "actor_a_09",
  "created_at": "2026-09-28T15:00:00Z",
  "updated_at": "2026-09-28T15:00:00Z",
  "completed_at": null,
  "revision": 1
}
```

The service derives a stable human or agent actor ID from a verified authentication context and binds it to every read, mutation, decision, and feed subscription. A shared service key, browser field, request-supplied actor name, display name, or source reference cannot establish actor identity. Service-to-service access must preserve a verified end actor or use a separately identified service actor with only its own grants. An actor must have a current grant on the specific board: read to view tasks/history/feed; write to create/edit/complete/reopen/request removal; approve-removal to decide a pending request. Grants are checked on every operation, including replay; denial reveals no other board's task data. Eddie owns board-grant policy; Echo administers grants only under Eddie's direction. The concrete verified identity source and lifecycle are selected through evidence-backed implementation discovery. An approver needs a current approve-removal grant and must differ from the requester; self-approval is prohibited.

## Lifecycle and writes

Normal task states are `open` and `done`. Create starts `open`; edit changes content/assignee/priority in either state without changing status; complete changes `open → done` and sets `completed_at`; reopen changes `done → open` and clears `completed_at`. Repeating a transition from its destination state is a safe no-op or returns the original result, never a second mutation. A removal request can be made against an active task and becomes `pending` without changing task status. A denial closes the request and keeps the task active. Approval of a pending request by a distinct authorized approver, with a recorded reason and the current task revision, changes the task to `removed` and records a tombstone; removed tasks cannot be edited, completed, reopened, or removed again. A new request after denial is a new decision record. Stale revisions fail with a conflict and require refresh; the service must not silently overwrite another actor's edit.

Every accepted mutation, removal request, and decision is attributed to the authenticated actor, board, task, operation, service time, prior/new revision, and outcome in append-only audit history. History cannot be rewritten by normal task operations. A client supplies a unique idempotency key for each intended write within a board/actor scope. Repeating the same key and same payload returns the original outcome and identifiers without adding a history entry or feed event. Reusing the key with a different payload fails. A retry after a lost response is safe across restart. A rejected authorization or stale-revision attempt cannot become an accepted mutation through replay of its key.

## Event feed and recovery

Every committed task change and removal decision produces a durable event with board ID, task ID, resulting revision, event kind, and an opaque cursor. For one board, events have a stable total order consistent with committed changes; an event becomes visible only after its task/history change is durable. Clients obtain a consistent JSON board snapshot plus an opaque cursor, then request events after that cursor. Replay returns missed events in order, including removal tombstones, after disconnect or service restart. Reconnect may redeliver an event; clients deduplicate by stable event ID/cursor and task revision. Cursor contents are opaque and cannot be used as authorization. A cursor outside retention produces an explicit expired-cursor result and requires a fresh snapshot; silent gaps are forbidden. During a private, bounded trial, audit records, tombstones, events, and idempotency outcomes have no automatic expiry and survive restart. Removed-history reads require an authorized reader. This trial rule does not authorize production, public exposure, or broader multi-user use. Numeric production retention periods and their capacity/deletion implications require evidence and Eddie's approval before broader use. Snapshot/cursor handoff mechanics and event transport require evidence-backed implementation discovery and review; neither is assumed here. Recovery must preserve committed task state, audit entries, idempotency outcomes, and replayable events across restart.

## Threat notes and decisions

- Threats to test: forged actor labels, cross-board reads/writes, shared-key impersonation, concurrent/stale edits, duplicate retries, approval bypass, and lost or reordered events.
- **D-1 — decided:** one dedicated logical shared-task service/store; Echo operates; Eddie retains owner/policy authority. Database, runtime, hosting, and deployment require later evidence-backed selection and authorization.
- **D-2 — decided:** verified per-actor identity; Eddie owns board-grant policy and Echo administers only under his direction. Select the concrete identity source/lifecycle during implementation discovery. Shared keys are not end-actor identity.
- **D-3 — decided:** a distinct authorized approver, no self-approval, recorded reason and current task revision.
- **D-4 — decided for private bounded trial:** no automatic expiry for audit, tombstones, events, or idempotency outcomes; preserve them across restart; only authorized readers see removed history; expired cursors require a fresh snapshot. Numeric production retention needs evidence and Eddie's approval before broader use.
- **D-5 — decided:** JSON snapshot with opaque cursor, ordered durable event/replay, fresh snapshot after cursor expiry, `low|normal|high` priority, and optional opaque `source_ref`. Choose transport after stack discovery.

The [acceptance matrix](../reports/KANBAN-ACCEPTANCE-MATRIX.md) maps each guarantee to a pass/fail case. Three distinct audit/refinement rounds and Eddie's final approval precede any implementation task. Technical selection requires discovery evidence and its own approval gate.
