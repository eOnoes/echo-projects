# Task Ledger

## Current task

**Task:** `AIK-03` — define shared Kanban MVP requirements
**Status:** `IN_PROGRESS`
**Executor:** Codex (`gpt-6-sol`, medium reasoning)
**Owner direction:** The original project scope covers items 1–3; Eddie delegated project management to Echo.
**Allowed output:** spec, acceptance matrix, receipt, and handoff under this packet only; no backend or source-code implementation.

AIK-01 and AIK-01-DOCS are complete and verified. AIK-03 is the sole `IN_PROGRESS` task.

## Completed

### AIK-01 — Control boundary audit

**Status:** `COMPLETE_WITH_LIMITATIONS` — Codex delivered the cited matrix; Echo verified citations and scope. Eddie delegated the wording decision to Echo for the bounded follow-on AIK-01-DOCS task.

### AIK-01-DOCS — Apply delegated Control boundary wording

**Status:** `COMPLETE`

- [x] Changed only `docs/MIND-INTEGRATION-READINESS.md` and `docs/SECURITY-FOUNDATION.md` in the private Control repo.
- [x] Verified exact two-file scope, Markdown links, `git diff --check`, and private-content scan.
- [x] Fast-forwarded commit `12633d4c2ccc06a7dc9b25af80b56f3e249a05ab` to Control `master`; remote blob readback matched and source worktree is clean.
- [x] Preserved the operations freeze; no code/runtime work.
- **Receipt:** `receipts/AIK-01-DOCS.md`; **HTML:** `reports/AIK-01-DOCS-HANDOFF.html`.

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
| `AIK-03` | Draft shared Kanban MVP contract and acceptance matrix | `IN_PROGRESS` | AIK-01-DOCS verified | Codex (`gpt-6-sol`, medium) |
| `AIK-04` | Inference Control security remediation | `BLOCKED` | Separate scope, approval, and review | Unassigned |

## Task rules

- One task may be `IN_PROGRESS` at a time.
- `AIK-01` is complete with limitations; its deliverable is the verified boundary matrix in this packet.
- `AIK-01-DOCS` is complete; only the two named Control Markdown files changed, with remote readback verification.
- `AIK-03` is design-only; no backend or UI implementation.
- `AIK-04` is not authorized by this packet and must precede any live model/runtime use.
- Never mark a task complete from an executor summary alone; Echo verifies evidence and remote files.
