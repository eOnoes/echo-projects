# Task Ledger

## Current gate / most recent task

**Task:** `AIK-07B-KANBAN-CONTRACT-UPDATE` — integrate recorded owner decisions into the design contract
**Status:** `READY` — owner directions D-1–D-5 are recorded; no implementation task is active.
**Executor:** Codex (`gpt-6-sol`, medium reasoning)
**Owner direction:** Eddie answered D-1–D-5 and asked Echo to see the product through. AIK-07B is a bounded packet-only contract revision; three distinct audit/refinement rounds and final owner review precede any build.
**Allowed output:** Only paths listed in [`tasks/AIK-07B-KANBAN-CONTRACT-UPDATE.md`](tasks/AIK-07B-KANBAN-CONTRACT-UPDATE.md).
**Review artifacts:** [`receipts/AIK-07B-OWNER-DECISIONS.md`](receipts/AIK-07B-OWNER-DECISIONS.md), current Kanban contract/matrix, and task receipt. Echo will verify each branch and audit round.

AIK-01, AIK-01-DOCS, AIK-03, AIK-05, AIK-06, and AIK-07 are complete with limitations. AIK-04-PLAN is paused after independent review `NEEDS_CHANGES`. D-1–D-5 owner directions are recorded; implementation details and the audited contract gate remain. Item 8 is assigned separately on Mind; items 9–10 wait for physical hardware evidence.

## Completed

### AIK-07 — Kanban D-1–D-5 owner decision brief

**Status:** `COMPLETE_WITH_LIMITATIONS` — branch `codex/AIK-07-KANBAN-DECISION-BRIEF`, commit `fa8fc4bedb1eb94006e230a1d85e75b7f253dc64`; six changed task-output blobs match remote and links/content checks pass.

- [x] Provides packet-cited facts, supported options, recommendations, tradeoffs, unknowns, and a first testable slice.
- [x] At brief issuance D-1–D-5 were pending; Eddie subsequently recorded all five directions in `receipts/AIK-07B-OWNER-DECISIONS.md`.
- [x] Echo verified the brief when issued; Eddie later recorded all five owner directions in `receipts/AIK-07B-OWNER-DECISIONS.md`.

### AIK-06 — Control Chat navigation fix

**Status:** `COMPLETE_WITH_LIMITATIONS` — branch `codex/AIK-06-CONTROL-CHAT-NAV`, commit `6c2a86551a49412ad7c3c4c9a65b1ff9fab2c66c`; not merged.

- [x] Chat render crash fixed; regression test passes; production build passes.
- [x] Six full-suite failures independently reproduced at the exact base commit; no new full-suite failures attributed to this change.
- [x] Relay destination is unverified and unchanged; demo composer remains disabled.
- [x] Exact remote blob readback, branch scope, clean worktree, and `git diff --check` verified.

### AIK-05 — Inference Control local-mock prototype

**Status:** `COMPLETE_WITH_LIMITATIONS` — commit `e480d8a6f92cccc20d3fdba7397145e97b537943` on private branch `codex/IC-01-LOCAL-MOCK`; Echo reran syntax checks, all 3 tests, diff hygiene, and remote readback.

- [x] Loopback-only server, persistent mock-mode UI label, fixture catalogue, in-memory simulated load/unload.
- [x] Tests prove non-loopback/agent startup denied and no real process spawn/upstream fetch in mock workflow.
- [x] Live-mode security and operator auth remain unresolved; no real model/upstream action enabled.

### AIK-01 — Control boundary audit

**Status:** `COMPLETE_WITH_LIMITATIONS` — Codex delivered the cited matrix; Echo verified citations and scope. Eddie delegated the wording decision to Echo for the bounded follow-on AIK-01-DOCS task.

### AIK-01-DOCS — Apply delegated Control boundary wording

**Status:** `COMPLETE`

- [x] Changed only `docs/MIND-INTEGRATION-READINESS.md` and `docs/SECURITY-FOUNDATION.md` in the private Control repo.
- [x] Verified exact two-file scope, Markdown links, `git diff --check`, and private-content scan.
- [x] Fast-forwarded commit `12633d4c2ccc06a7dc9b25af80b56f3e249a05ab` to Control `master`; remote blob readback matched and source worktree is clean.
- [x] Preserved the operations freeze; no code/runtime work.
- **Receipt:** `receipts/AIK-01-DOCS.md`; **HTML:** `reports/AIK-01-DOCS-HANDOFF.html`.

### AIK-03 — Define Kanban MVP requirements

**Status:** `COMPLETE_WITH_LIMITATIONS` — Codex draft verified by Echo and independently reviewed `PASS — ZERO GAPS`; all five owner directions are now recorded. AIK-07B integrates them into the contract and matrix.

- [x] Compact contract and A-01–A-12 acceptance matrix.
- [x] Exact eight-file project-packet scope, remote readback, links, JSON example, diff check, and content scan verified.
- [x] No backend or implementation selected.
- **Review:** [`reports/AIK-03-ECHO-REVIEW.md`](reports/AIK-03-ECHO-REVIEW.md); **HTML handoff:** [`reports/AIK-03-HANDOFF.html`](reports/AIK-03-HANDOFF.html).

### AIK-00 — Create and publish the project packet

**Status:** `COMPLETE`

- [x] Publish only `projects/ai-box-kanban-foundation/` to the public project-control repository.
- [x] Verify the remote `main` commit and packet file tree.
- [x] Confirm the staged commit contains only 12 packet files; link, diff, secret, and private-path checks pass.
- [x] Leave the canonical dirty clone and its unrelated changes untouched.
- **Receipt:** initial packet commit `c975022afe3080f7e6bf1c6b612ed242acffd271`.

### AIK-02 — Find and safely import Inference Control

**Status:** `COMPLETE_WITH_LIMITATIONS`

- [x] Identified the dashboard as `Onoes-Inference-Control`.
- [x] Created a fresh-history, sanitized private repository; only reviewed source and safe docs were included.
- [x] Excluded legacy live host configuration, credential-bearing launcher, usage data, screenshots, and raw audit.
- [x] Confirmed syntax check passed and remote commit matched local at completion.
- [x] Recorded that the app is not safe to run/expose until security remediation and independent review.
- [x] Did not rotate the existing key, per Eddie’s direction; no key material is in this packet.

The successful import is not runtime validation and does not close the dashboard security gate.

## Queue

| Task | Summary | Status | Dependency | Executor |
|---|---|---|---|---|
| `AIK-01-DOCS` | Apply delegated boundary wording to two Control Markdown docs | `COMPLETE` | Remote readback verified | Echo |
|| `AIK-03` | Draft shared Kanban MVP contract and acceptance matrix | `COMPLETE_WITH_LIMITATIONS` | Independent review passed; owner directions subsequently recorded | Codex, Echo verified |
| `AIK-04-PLAN` | Draft security remediation plan | `PAUSED` | Independent review `NEEDS_CHANGES`; product-first priority | Echo |
| `AIK-05` | Local-mock Inference Control slice | `COMPLETE_WITH_LIMITATIONS` | Remote branch and 3 tests verified; no live mode | Codex, Echo verified |
| `AIK-06-CONTROL-CHAT-NAV` | Repair Control Chat navigation | `COMPLETE_WITH_LIMITATIONS` | Branch verified, not merged; six baseline failures remain | Codex, Echo verified |
| `AIK-07-KANBAN-DECISION-BRIEF` | Recommend options for D-1–D-5 from packet evidence | `COMPLETE_WITH_LIMITATIONS` | All five owner directions subsequently recorded; AIK-07B integrates them into contract | Codex, Echo verified |
|| `AIK-07B-KANBAN-CONTRACT-UPDATE` | Integrate D-1–D-5 into contract, matrix, and brief | `READY` | Owner decisions recorded; three audit/refinement rounds remain | Codex, Echo review |
|| `AIK-08-KANBAN-VERTICAL-SLICE` | Implement first shared-board slice and Relay/Control views | `BLOCKED` | Contract revised, passes three audits, and receives owner approval; technical choices gated | Unassigned |
| `AIK-09-MIND-GATES` | G0–G7 review | `BLOCKED` | Separate active Mind task; consume its verified report only | Separate Codex effort |
| `AIK-10-HARDWARE-BOUNDARY` | Confirm topology and Proxmox boundary | `BLOCKED` | Physical parts/assembly and authorization | Eddie / Echo |

## Task rules

- One task may be `IN_PROGRESS` at a time.
- `AIK-01` is complete with limitations; its deliverable is the verified boundary matrix in this packet.
- `AIK-01-DOCS` is complete; only the two named Control Markdown files changed, with remote readback verification.
- `AIK-03` is design-only and complete with limitations; all D-1–D-5 owner directions are recorded; no backend or UI implementation is authorized before contract review and approval.
- `AIK-05` is local mock only; live security, remote access, and real model/upstream operations remain blocked.
- `AIK-07` delivered recommendations without deciding for Eddie; he later recorded all D-1–D-5 directions in the owner-decision receipt.
- AIK-06 remains on a branch, not merged or deployed; do not guess Relay routing.
- D-1–D-5 directions are recorded; database/runtime/hosting, concrete identity source, production retention durations, and event transport remain implementation-time decisions. Items 5–6 wait for the contract update, three audit rounds, and owner approval; item 8 stays with the separate Mind effort; items 9–10 wait for physical evidence.
- Never mark a task complete from an executor summary alone; Echo verifies evidence and remote files.
