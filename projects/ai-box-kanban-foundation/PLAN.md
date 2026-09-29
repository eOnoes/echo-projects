# Execution Plan

**Plan state:** `RUNNING`
**Current executor:** Codex — AIK-04-PLAN, `gpt-6-sol`, medium reasoning; project code fixes remain blocked.
**Rule:** one `IN_PROGRESS` task at a time.

## Owner-estimated AI-box schedule (planning only)

- **October 6, 2026:** expected to have all parts.
- **October 11, 2026:** target to have the unit put together.
- **October 12, 2026:** target boot/load testing.

Eddie described these dates as estimates, not commitments. They do not authorize purchasing, OS/BIOS changes, service exposure, or live model testing before the relevant gates pass.

## Phase 0 — Project packet and safety boundaries

**Status:** `COMPLETE` — initial packet commit `c975022afe3080f7e6bf1c6b612ed242acffd271` was pushed to public `main` and read back from GitHub.

- [x] Create canonical goal, charter, authority, plan, tasks, evidence, handoff, closure, overrides, and Codex entrypoint.
- [x] Record dashboard discovery as complete and its runtime security gate as open.
- [x] Define Codex permissions, milestone receipts, error handling, and GitHub billing limits.
- [x] Validate public-safe content and commit only this project directory; internal links resolve, diff check passes, and credential/private-path scan is clean.

**Exit gate:** `PROVEN` — remote head and project file tree were read back; the published commit contains only the 12 files in this project packet.

## Phase 1 — Reconcile Control authority boundaries (AIK-01)

**Status:** `COMPLETE_WITH_LIMITATIONS` — Codex's cited matrix is verified; its recommendations now flow into AIK-01-DOCS under Eddie's delegated wording authority.
**Model:** `gpt-6-sol` · **reasoning:** medium

**Purpose:** establish a single consistent statement of task ownership, verification, approval, and execution authority.

**Inputs:** `Onoes-Control` documents `MIND-INTEGRATION-READINESS.md`, `SECURITY-FOUNDATION.md`, `GATE-2-INVARIANTS.md`, `CHAT-PHASE3-LIVE-MESSAGING.md`; supplied read-only Mind audit conclusion.

**Steps:**
1. Codex reads only those docs and extracts statements about task records, approvals, persistence, and execution authority.
2. Codex writes `reports/CONTROL-BOUNDARY-MATRIX.md` with document/section references, agreement/conflict, and proposed wording.
3. Echo verifies every citation and checks that the proposal preserves: Mind internal execution records only; Relay candidate UI, not a task service; Control oversight/approval, not execution authority.
4. Eddie reviews the matrix or delegates wording selection to Echo.
5. After explicit approval or delegation, a separate task may authorize exact documentation-only edits to named Control files.

**Exit gate:** cited matrix and replacement text are owner-approved or owner-delegated; any authorized edits have a scoped diff and doc checks; no code changes.

**Failure route:** missing or contradictory source facts become `UNRESOLVED`; do not infer a contract or edit around the conflict. Return the decision to Eddie.

## Phase 1B — Apply delegated boundary wording (AIK-01-DOCS)

**Status:** `COMPLETE` — commit `12633d4c2ccc06a7dc9b25af80b56f3e249a05ab` was fast-forwarded to private Control `master`; remote readback and clean checkout verified.

## Phase 2 — Record Inference Control discovery/import (AIK-02)

**Status:** `COMPLETE_WITH_LIMITATIONS`.

The dashboard was found as `Onoes-Inference-Control`, imported into a fresh-history private repository, and scanned before publication. The existing key was not rotated and is not part of the new repo. The prototype is not safe to run or expose; its security gate is a separate follow-on project. No live runtime test is established by the import.

**Exit gate:** completion and limitations remain documented; no claim of deployment or runtime readiness.

## Phase 3 — Define Kanban MVP requirements (AIK-03)

**Status:** `COMPLETE_WITH_LIMITATIONS` — Codex draft verified by Echo and independently reviewed PASS; D-1–D-5 remain owner decisions, with idempotency-result retention to be included in D-4 before implementation.
**Purpose:** specify a separate shared task service before implementation.

**Deliverable:** `deliverables/KANBAN-MVP.md` plus `reports/KANBAN-ACCEPTANCE-MATRIX.md`.

The spec must cover:

- Task identifiers, title/body, status, priority, project/scope, source reference, creator/assignee, and timestamps.
- State transitions and explicit create/edit/complete/reopen/remove-request/remove-approval behavior.
- Stable authenticated human/agent identity and board-scoped authorization; a shared service key or request-supplied actor name is not identity.
- Mutation attribution, append-only audit history, idempotency, and safe duplicate handling.
- Durable event delivery, ordering, replay cursor, retention, and recovery after restart/disconnection.
- Separation of responsibilities: task store/contract not chosen yet; Relay candidate UI; Control verification/approval; Mind memory and internal execution records.
- Acceptance cases for unauthorized writes, attribution, repeated requests, removal approval, event replay, and feed recovery.

**Non-goal:** no backend choice or code implementation in this phase.

**Exit gate:** compact spec, threat notes, and acceptance matrix reviewed by Echo and independently reviewed; unresolved choices are explicitly listed for Eddie.

## Phase 4 — Prepare Inference Control security remediation plan (AIK-04-PLAN)

**Status:** `RUNNING` — Codex receives only the sanitized, verified findings summarized in the task handoff.
**Allowed scope:** project-packet plan/acceptance/receipt artifacts only; no dashboard code, secrets, runtime, or services.
**Exit gate:** Echo verifies the proposed fixes and acceptance cases; any code remediation requires a separate exact-file task and review.

## Phase 5 — Close or split follow-on work

Eddie decides whether to authorize documentation edits, a separate Kanban implementation packet, and/or a separate Inference Control security-remediation project. Do not silently extend this plan into implementation.

## Milestone/error protocol

After each assigned task, Codex updates the task status, evidence register, handoff, task receipt, and Markdown/HTML milestone report in its scoped branch. If a check fails, preserve the failure, fix only within assignment, rerun focused and complete checks, and report exact outputs. Any secret, out-of-scope change, live-runtime requirement, or approval conflict stops the task as `BLOCKED`; do not widen scope or weaken a gate to claim progress.
