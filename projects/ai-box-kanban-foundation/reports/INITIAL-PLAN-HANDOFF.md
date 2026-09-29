# AI Box and Shared Task Board — Initial Project Handoff

**Project ID:** `ai-box-kanban-foundation`
**Status:** `READY_FOR_REVIEW`
**Owner:** Eddie
**Manager:** Echo
**Executor:** Codex, conditional on a task-specific start approval

## TL;DR

- The three requested planning items are captured: reconcile Control authority docs; record the completed Inference Control discovery/import; define the shared Kanban MVP.
- Item 2 is complete, but the dashboard is **not safe to run or expose** until its separate security gate passes.
- Codex is allowed to produce bounded project artifacts and, after approval, a read-only Control-doc matrix. Source edits and backend implementation are not authorized yet.
- Milestone updates will be committed to a task-scoped Codex branch, verified by Echo, then promoted to `main` only after acceptance.
- **Next:** Eddie reviews this packet and authorizes `AIK-01` if the scope is right.

## Proposed sequence

1. **AIK-01 — Control boundary audit:** read-only document comparison; write a cited contradiction matrix and proposed wording. Eddie approves before any source-doc edit.
2. **AIK-02 — Inference Control discovery/import:** complete. Dashboard identified, sanitized private repo created, key excluded; no runtime readiness claimed.
3. **AIK-03 — Kanban MVP specification:** design-only contract for identity, authorization, lifecycle, audit, idempotency, event replay, and recovery. No backend choice or code.
4. **Separate follow-up:** dashboard security remediation; mandatory before model/runtime use, but not part of this low-hanging-fruit packet.

## Codex guardrails

Codex receives one task at a time. It may write only inside this project packet unless Eddie explicitly approves a later source-document task naming exact files. It may not inspect credential files, the dirty Mind workspace, or unrelated projects; start services; run live model operations; rotate keys; use paid GitHub features; or push directly to `main`.

For errors, preserve exact output, diagnose within scope, fix root cause, rerun focused and complete checks, and record evidence. Do not suppress tests, loosen acceptance criteria, or rewrite failures as successes. Stop and report `BLOCKED` if a fix requires broader scope or approval.

## Milestone record

Codex should update `TASKS.md`, `EVIDENCE.md`, and `HANDOFF.md`, add a receipt, and produce Markdown plus HTML after each milestone. It commits/pushes only the project folder to `codex/<TASK-ID>`. Echo verifies the remote diff and evidence before accepting and promoting the milestone.

## Review decision

Reply **“Start AIK-01”** to authorize the read-only Control-document audit, or give edits to the packet first. No Codex task has been dispatched.
