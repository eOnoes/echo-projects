# Task Ledger

## Current task

**Task:** `AIK-06-CONTROL-CHAT-NAV` — repair Onoes-Control Chat navigation
**Status:** `IN_PROGRESS`
**Executor:** Codex (`gpt-6-sol`, medium reasoning)
**Owner direction:** Eddie expanded work through items 4–10 and asked to move toward a functioning product; item 7 is the next independent software slice.
**Allowed output:** Chat navigation fix and directly related regression test in the named private Control repo branch. No Relay URL guessing, unrelated app changes, credentials, deployments, or live runtime.

AIK-01, AIK-01-DOCS, and AIK-03 are complete with limitations. AIK-05 local mock is complete with limitations. AIK-04-PLAN is paused after independent review `NEEDS_CHANGES`. D-1–D-5 remain open; item 8 is assigned separately on Mind; items 9–10 wait for physical hardware evidence.

## Completed

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

**Status:** `COMPLETE_WITH_LIMITATIONS` — Codex draft verified by Echo and independently reviewed `PASS — ZERO GAPS` by Mimo v2.5. D-1–D-5 remain owner decisions; D-4 should explicitly include idempotency-outcome retention/expiry before implementation.

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
| `AIK-03` | Draft shared Kanban MVP contract and acceptance matrix | `COMPLETE_WITH_LIMITATIONS` | Echo + independent review passed; D-1–D-5 pending | Codex, Echo verified |
| `AIK-04-PLAN` | Draft security remediation plan | `PAUSED` | Independent review `NEEDS_CHANGES`; product-first priority | Echo |
| `AIK-05` | Local-mock Inference Control slice | `COMPLETE_WITH_LIMITATIONS` | Remote branch and 3 tests verified; no live mode | Codex, Echo verified |
| `AIK-06-CONTROL-CHAT-NAV` | Repair Control Chat navigation | `IN_PROGRESS` | Independent software task; verified route target required | Codex (`gpt-6-sol`, medium) |
| `AIK-07-KANBAN-DECISIONS` | Resolve D-1–D-5 / authoritative store | `BLOCKED` | Eddie's choices required before backend selection | Eddie / Echo |
| `AIK-08-BOARD-UI` | Relay task UI + Control oversight views | `BLOCKED` | AIK-07 contract/store decision and review | Unassigned |
| `AIK-09-MIND-GATES` | G0–G7 review | `BLOCKED` | Separate active Mind task; consume its verified report only | Separate Codex effort |
| `AIK-10-HARDWARE-BOUNDARY` | Confirm topology and Proxmox boundary | `BLOCKED` | Physical parts/assembly and authorization | Eddie / Echo |

## Task rules

- One task may be `IN_PROGRESS` at a time.
- `AIK-01` is complete with limitations; its deliverable is the verified boundary matrix in this packet.
- `AIK-01-DOCS` is complete; only the two named Control Markdown files changed, with remote readback verification.
- `AIK-03` is design-only and complete with limitations; D-1–D-5 remain open and no backend or UI implementation is authorized.
- `AIK-05` is local mock only; live security, remote access, and real model/upstream operations remain blocked.
- `AIK-06` is limited to Chat navigation and a regression test; do not guess Relay routing.
- Items 4–6 wait for D-1–D-5; item 8 stays with the separate Mind effort; items 9–10 wait for physical evidence.
- Never mark a task complete from an executor summary alone; Echo verifies evidence and remote files.
