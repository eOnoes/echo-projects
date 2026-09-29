# Task Ledger

## Current task

**Task:** `AIK-01` — Control boundary document audit
**Status:** `NEEDS_APPROVAL`
**Executor:** Codex, conditional
**Approval required:** Eddie must authorize start.
**Allowed output:** one cited contradiction matrix and proposed wording under this project’s `reports/`; no source edits.
**Evidence required:** exact source document/section references, scope check, local validation result, and a scoped pushed branch for Echo review.

No task is currently `IN_PROGRESS`.

## Completed

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
| `AIK-01` | Reconcile Control authority/boundary documents; matrix + proposed text | `NEEDS_APPROVAL` | Eddie starts task | Codex |
| `AIK-03` | Draft shared Kanban MVP contract and acceptance matrix | `READY` | AIK-01 reviewed | Codex |
| `AIK-04` | Inference Control security remediation | `BLOCKED` | Separate scope, approval, and review | Unassigned |

## Task rules

- One task may be `IN_PROGRESS` at a time.
- `AIK-01` is read-only against source repos; its deliverable is confined to this packet.
- `AIK-03` is design-only; no backend or UI implementation.
- `AIK-04` is not authorized by this packet and must precede any live model/runtime use.
- Never mark a task complete from an executor summary alone; Echo verifies evidence and remote files.
