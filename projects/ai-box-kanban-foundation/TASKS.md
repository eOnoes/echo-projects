# Task Ledger

## Current task

**Task:** `AIK-04-PLAN` — draft a read-only Inference Control security-remediation plan
**Status:** `READY_FOR_REVIEW` — Codex plan and acceptance matrix drafted; Echo verification and remote readback pending.
**Executor:** Codex (`gpt-6-sol`, medium reasoning)
**Owner direction:** Eddie asked Echo to keep Codex moving toward AI-box readiness; this task is planning-only.
**Allowed output:** security plan, acceptance matrix, receipt, and handoff under this project packet only; no dashboard source change, credential, or runtime.

AIK-01, AIK-01-DOCS, and AIK-03 are complete with limitations. AIK-03 decisions D-1–D-5 remain open; D-4 should specify idempotency-outcome retention before implementation. AIK-04-PLAN has proposed F1–F7 behavior and S-01–S-09 mock/fixture cases. No code fix or test is claimed.

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
| `AIK-04-PLAN` | Draft read-only security remediation plan from the sanitized audit findings | `READY_FOR_REVIEW` | Echo must verify branch, remote readback, and plan before completion | Codex (`gpt-6-sol`, medium) |
| `AIK-04` | Inference Control security remediation/code changes | `BLOCKED` | Separate exact-file implementation task, review, and authorization | Unassigned |

## Task rules

- One task may be `IN_PROGRESS` at a time; `READY_FOR_REVIEW` is not `COMPLETE`.
- `AIK-01` is complete with limitations; its deliverable is the verified boundary matrix in this packet.
- `AIK-01-DOCS` is complete; only the two named Control Markdown files changed, with remote readback verification.
- `AIK-03` is design-only and complete with limitations; D-1–D-5 remain open and no backend or UI implementation is authorized.
- `AIK-04-PLAN` is read-only planning from the sanitized audit findings; no dashboard source or credential access.
- `AIK-04` remediation/code changes remain blocked until a separate exact-file task is authorized and reviewed.
- Never mark a task complete from an executor summary alone; Echo verifies evidence and remote files.
