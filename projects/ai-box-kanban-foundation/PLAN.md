# Execution Plan

**Plan state:** `RUNNING`
**Current executor:** Echo — prepare the Round 3 frozen audit packet. Round 1 fixes are verified; Round 2 DSH/Gemini returned `PASS` with process limitations recorded. AIK-05 local mock is verified complete; AIK-04-PLAN is paused.
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

**Status:** `COMPLETE_WITH_LIMITATIONS` — Codex draft verified by Echo and independently reviewed PASS. Eddie decided D-1 at the logical/operator level; D-2–D-5 were later owner-decided and integrated under AIK-07B. D-4 private-trial no-expiry and the production retention gate are explicit.
**Purpose:** specify a separate shared task service before implementation.

**Deliverable:** `deliverables/KANBAN-MVP.md` plus `reports/KANBAN-ACCEPTANCE-MATRIX.md`.

The spec must cover:

- Task identifiers, title/body, status, priority, project/scope, source reference, creator/assignee, and timestamps.
- State transitions and explicit create/edit/complete/reopen/remove-request/remove-approval behavior.
- Stable authenticated human/agent identity and board-scoped authorization; a shared service key or request-supplied actor name is not identity.
- Mutation attribution, append-only audit history, idempotency, and safe duplicate handling.
- Durable event delivery, ordering, replay cursor, retention, and recovery after restart/disconnection.
- Separation of responsibilities: dedicated logical task service/store is the chosen authority under D-1; Relay remains a client, Control oversight/approval, and Mind memory/internal execution records. Technology remains unselected.
- Acceptance cases for unauthorized writes, attribution, repeated requests, removal approval, event replay, and feed recovery.

**Non-goal:** no backend choice or code implementation in this phase.

**Exit gate:** compact spec, threat notes, and acceptance matrix reviewed by Echo and independently reviewed; unresolved choices are explicitly listed for Eddie.

## Phase 4 — Prepare Inference Control security remediation plan (AIK-04-PLAN)

**Status:** `PAUSED` — independent review returned `NEEDS_CHANGES`; Eddie redirected priority to a functioning local product slice. No source edits or live-mode fixes were made under this plan task.
**Allowed scope:** project-packet plan/acceptance/receipt artifacts only; no dashboard code, secrets, runtime, or services.
**Exit gate:** Echo verifies the proposed fixes and acceptance cases; any code remediation requires a separate exact-file task and review.

## Phase 5 — Deliver the local-only Inference Control mock slice (AIK-05)

**Status:** `COMPLETE_WITH_LIMITATIONS` — sanitized repo branch `codex/IC-01-LOCAL-MOCK`, commit `e480d8a6f92cccc20d3fdba7397145e97b537943`; Echo reran syntax checks and 3 tests and verified remote readback.

**Acceptance:** loopback-only UI, visible mock label, fixture catalogue, simulated in-memory load/unload, no real CLI/upstream side effects. This does not close F1–F7 for live mode.

## Phase 6 — Repair Control Chat navigation (AIK-06)

**Status:** `COMPLETE_WITH_LIMITATIONS` — branch `codex/AIK-06-CONTROL-CHAT-NAV`, commit `6c2a86551a49412ad7c3c4c9a65b1ff9fab2c66c`; receipt `receipts/AIK-06-CONTROL-CHAT-NAV.md`.
**Evidence:** focused regression 1/1 pass; production build passes; six full-suite failures were reproduced at the unchanged base commit. Branch is not merged/deployed; Relay destination remains unverified and unchanged.

## Phase 7 — Prepare D-1–D-5 owner decisions (items 4; AIK-07)

**Status:** `COMPLETE_WITH_LIMITATIONS` — Eddie's D-1–D-5 policy directions are recorded in `receipts/AIK-07B-OWNER-DECISIONS.md`; technical implementation choices remain open.

## Phase 7B — Integrate owner decisions and audit the contract (AIK-07B)

**Status:** `COMPLETE_WITH_LIMITATIONS` — Round 1 returned `NEEDS_CHANGES`; corrections are verified. Round 2 independent DSH audit returned `PASS` with process/traceability limitations recorded at [`AIK-07B-ROUND-2.md`](receipts/AIK-07B-ROUND-2.md). Round 3 and owner approval remain.
**Gate:** Freeze/hash the corrected packet and complete Round 3 as a distinct review; document Round 2's `PASS` and process limitations. No source code, backend, runtime, or service work. Final owner review precedes implementation.

## Phase 8 — Build Relay task UI and Control oversight views (items 5–6; AIK-08)

**Status:** `BLOCKED` until the contract update passes three distinct audit/refinement rounds and Eddie approves the final design. One task authority; Relay consumes it; Control remains oversight/approval only.

## Phase 9 — Mind G0–G7 review (item 8; external task)

**Status:** `BLOCKED` on the separate Codex/Mind effort's reviewed evidence. Do not duplicate or inspect the dirty Mind workspace.

## Phase 10 — Verify hardware and choose the initial Proxmox boundary (items 9–10; AIK-10)

**Status:** `BLOCKED` until physical delivery and topology inspection. Parts/assembly/boot dates are estimates. Candidate remains an isolated Linux VM with PCI passthrough; no installation, BIOS/OS change, firmware change, or live inference is authorized here.

## Product-first execution order

Use dependency-based chunks: Phase 6 Chat fix is branch-verified; D-1–D-5 are recorded; Phase 7B updates the contract, then Echo launches three separate audits; Phase 8 implementation follows only after three audit rounds and owner approval; Phase 9 stays with the separate Mind effort; hardware follows physical inspection. A functioning private prototype is the target; production readiness remains a separate gate.

## Milestone/error protocol

After each assigned task, Codex updates the task status, evidence register, handoff, task receipt, and Markdown/HTML milestone report in its scoped branch. If a check fails, preserve the failure, fix only within assignment, rerun focused and complete checks, and report exact outputs. Any secret, out-of-scope change, live-runtime requirement, or approval conflict stops the task as `BLOCKED`; do not widen scope or weaken a gate to claim progress.
