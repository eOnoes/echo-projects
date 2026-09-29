# Task Ledger

## Current task

**Task:** `AIK-01-DOCS` — apply delegated Control boundary wording
**Status:** `IN_PROGRESS`
**Executor:** Echo
**Approval:** Eddie delegated wording selection and management in the project chat.
**Allowed files:** only `Onoes-Control/docs/MIND-INTEGRATION-READINESS.md` and `Onoes-Control/docs/SECURITY-FOUNDATION.md`, plus this packet's evidence/handoff records.
**Checks:** exact two-file diff, `git diff --check`, Markdown link check, private-content scan, remote readback; no source-code changes.

AIK-01 audit is complete and verified. AIK-01-DOCS is the sole `IN_PROGRESS` task.

## Completed

### AIK-01 — Control boundary audit

**Status:** `COMPLETE_WITH_LIMITATIONS` — Codex delivered the cited matrix; Echo verified citations and scope. Eddie delegated the wording decision to Echo for the bounded follow-on AIK-01-DOCS task.

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
| `AIK-01-DOCS` | Apply delegated boundary wording to the two exact Control Markdown docs | `IN_PROGRESS` | Complete two-file checks and verify remote readback | Echo |
| `AIK-03` | Draft shared Kanban MVP contract and acceptance matrix | `READY` | AIK-01-DOCS verified | Codex |
| `AIK-04` | Inference Control security remediation | `BLOCKED` | Separate scope, approval, and review | Unassigned |

## Task rules

- One task may be `IN_PROGRESS` at a time.
- `AIK-01` is complete with limitations; its deliverable is the verified boundary matrix in this packet.
- `AIK-01-DOCS` is the active bounded documentation-only task; it may edit exactly the two named Control Markdown files.
- `AIK-03` is design-only; no backend or UI implementation.
- `AIK-04` is not authorized by this packet and must precede any live model/runtime use.
- Never mark a task complete from an executor summary alone; Echo verifies evidence and remote files.
