# Shared Kanban MVP contract — AIK-03

**Status:** proposed contract for Eddie and independent review; no service or backend is selected.

## Purpose and boundary

Provide one authenticated, board-scoped task record and recoverable change feed for humans and agents. The authoritative task service/store remains a future decision. Mind retains memory and internal `work_items` / `stage_executions`, which are not the shared board. Relay may present the board after integration is separately authorized; its current todo/goal displays are not the service. Control owns oversight, findings, and bounded approvals; its records and chat do not create task authority or authorize execution. Onoes-Inference-Control is a separate dashboard with an open security gate.

MVP includes task create, edit, complete, reopen, removal request/approval, authorized reads, immutable history, and a replayable board feed. It does not include execution commands, automatic task creation from Mind or chat, multi-board moves, dependencies, comments, attachments, notifications, or Relay/Control UI changes. It does not choose an API protocol, database, vendor, identity provider, or storage technology.

## Task and actor contract

Each task has: `id` (stable opaque identifier), `board_id`, `project_scope` (board-local project/scope label), `title`, optional `body`, `status`, `priority` (`low|normal|high`), optional `source_ref` (opaque reference, never authority), `creator_actor_id`, optional `assignee_actor_id`, `created_at`, `updated_at`, optional `completed_at`, and monotonic `revision`. A removal request has its own requester, time, reason, and pending state; approval records decision actor/time and the task revision reviewed. Timestamps are service-assigned UTC values. Removed tasks retain a tombstone, history, and feed event; removal hides normal board listing but does not erase audit evidence.

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

The service derives a stable human or agent actor ID from a verified authentication context and binds it to every read, mutation, decision, and feed subscription. A shared service key, browser field, request-supplied actor name, display name, or source reference cannot establish actor identity. Service-to-service access must preserve a verified end actor or use a separately identified service actor with only its own grants. An actor must have a current grant on the specific board: read to view tasks/history/feed; write to create/edit/complete/reopen/request removal; approve-removal to decide a pending request. Grants are checked on every operation, including replay; denial reveals no other board's task data. Who issues grants, which actors may approve, and whether an approver may also be the requester require Eddie's policy decision.

## Lifecycle and writes

Normal task states are `open` and `done`. Create starts `open`; edit changes content/assignee/priority in either state without changing status; complete changes `open → done` and sets `completed_at`; reopen changes `done → open` and clears `completed_at`. Repeating a transition from its destination state is a safe no-op or returns the original result, never a second mutation. A removal request can be made against an active task and becomes `pending` without changing task status. A denial closes the request and keeps the task active. Approval of a pending request changes the task to `removed` and records a tombstone; removed tasks cannot be edited, completed, reopened, or removed again. A new request after denial is a new decision record. Stale revisions fail with a conflict and require refresh; the service must not silently overwrite another actor's edit.

Every accepted mutation, removal request, and decision is attributed to the authenticated actor, board, task, operation, service time, prior/new revision, and outcome in append-only audit history. History cannot be rewritten by normal task operations. A client supplies a unique idempotency key for each intended write within a board/actor scope. Repeating the same key and same payload returns the original outcome and identifiers without adding a history entry or feed event. Reusing the key with a different payload fails. A retry after a lost response is safe across restart. A rejected authorization or stale-revision attempt cannot become an accepted mutation through replay of its key.

## Event feed and recovery

Every committed task change and removal decision produces a durable event with board ID, task ID, resulting revision, event kind, and an opaque cursor. For one board, events have a stable total order consistent with committed changes; an event becomes visible only after its task/history change is durable. Clients obtain a consistent board snapshot plus a cursor, then request events after that cursor. Replay returns missed events in order, including removal tombstones, after disconnect or service restart. Reconnect may redeliver an event; clients deduplicate by stable event ID/cursor and task revision. Cursor contents are opaque and cannot be used as authorization. A cursor outside retention produces an explicit expired-cursor result and requires a fresh snapshot; silent gaps are forbidden. The retention period, maximum replay window, and snapshot/cursor handoff mechanics are decisions for Eddie before implementation. Recovery must preserve committed task state, audit entries, idempotency outcomes, and replayable events across restart.

## Threat notes and decisions

- Threats to test: forged actor labels, cross-board reads/writes, shared-key impersonation, concurrent/stale edits, duplicate retries, approval bypass, and lost or reordered events.
- **D-1:** Choose the authoritative shared task service/store and its operator; no existing Mind, Relay, or Control component is qualified by this packet.
- **D-2:** Choose the identity trust source and lifecycle for stable human/agent IDs, and who grants/revokes board roles.
- **D-3:** Define removal approver eligibility, whether self-approval is permitted, and any required reason/revision rule. Until decided, no removal-approval path is authorized for implementation.
- **D-4:** Set audit/event retention and replay window, including policy for expired cursors and access to removed-task history.
- **D-5:** Decide the snapshot/feed interface and whether the proposed priority and source-reference conventions fit the first board. These choices do not expand this design task into implementation.

The [acceptance matrix](../reports/KANBAN-ACCEPTANCE-MATRIX.md) maps each guarantee to a pass/fail case. Echo and an independent reviewer must review this proposal before implementation is scoped.
