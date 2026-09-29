# Project Authority

Inherits `doctrine/AUTHORITY-BASELINE.md`, `doctrine/GITHUB-BILLING-SAFETY.md`, and `doctrine/PUBLICATION-SAFETY.md`. This file adds project restrictions only.

## Roles

- **Owner:** Eddie — scope, go/no-go decisions, final acceptance.
- **Manager:** Echo — task assignment, evidence verification, branch review, status and handoff updates.
- **Executor:** Codex — only when Echo assigns a task ID and Eddie has approved the applicable gate.
- **Reviewer/witness:** Echo; a separate reviewer may be assigned for the Kanban contract or security follow-up.

## Codex permissions by stage

### Before a task is assigned

Codex may read this packet only. `READY_FOR_REVIEW` is not authorization to execute. No source audit, code edit, test, service start, provider call, GitHub write, or credential access is permitted until `TASKS.md` names an `IN_PROGRESS` task and Echo sends the task-specific handoff.

### AIK-01 — Control boundary audit

After explicit start approval, Codex may:

- Read the named Markdown documents in the `Onoes-Control` repository and the supplied audit conclusion.
- Create a contradiction matrix and proposed wording under this project packet’s `reports/` directory.
- Run local, read-only Markdown/link checks on its deliverable.

Codex may **not** edit the Control repository during AIK-01. Source-document edits require Eddie to approve the proposed wording and a new bounded task naming the exact files.

### AIK-03 — Kanban MVP specification

After AIK-01’s output is reviewed, Codex may create/update the Kanban specification and acceptance matrix inside this project packet only. This is design-only: no backend, database, API, Relay, Control, or Mind source changes.

## AIK-06 — Control Chat navigation

After Eddie's 2026-09-29 scope expansion, Codex may modify only the private Onoes-Control Chat navigation path and a directly related regression test in the task-named branch. The task may identify exact files by inspecting the clean Control checkout, but any edit beyond the Chat path/test or any Relay URL requires a stop and decision. No Mind/Relay source, credential, deployment, or live runtime access.

## AIK-07B — integrate owner decisions into the design contract

Eddie has recorded D-1–D-5 in `receipts/AIK-07B-OWNER-DECISIONS.md`. Codex may modify only the exact paths named in `tasks/AIK-07B-KANBAN-CONTRACT-UPDATE.md` to revise the contract, acceptance matrix, decision brief, and packet tracking. This is design-only. Do not run the three audit rounds inside the update task; Echo launches each fresh round separately. No source code, service, database, runtime, deployment, or public exposure is authorized. Preserve all open technical choices and keep AIK-08 blocked until three audit/refinement rounds and Eddie's final design approval pass.


### Later, separately approved work

Only a new task can authorize live-mode Inference Control security changes. Any live model operations require a separate scope decision and independent review. This packet never authorizes live model operations.

## Allowed error handling

For a failure inside an assigned task, Codex must:

1. Preserve the exact failing command, result, affected file, and task ID in the task receipt.
2. Diagnose and fix the root cause only within its allowed paths.
3. Re-run the focused check, then the relevant complete check, and record exact results.
4. Keep unrelated or pre-existing failures distinct; do not claim they were fixed.
5. Stop as `BLOCKED` if repair needs wider scope, private credentials, permission changes, a live service, a provider call, or a decision from Eddie.

Codex must not skip/xfail a failing test, loosen an acceptance threshold, delete evidence, rewrite a failure as success, or continue under a different task without approval.

## Milestone recording and GitHub

- Codex records receipts and evidence for each task. For packet-only tasks, it may push only the project packet to `codex/<TASK-ID>`; for source-repo tasks, it may edit only the task-named repository/branch and paths, never push `main`, and must return the exact commit for Echo to verify.
- Echo verifies the source diff, checks, and remote readback, then updates/promotes the project packet separately.
- A pushed milestone is a reviewable claim, not completion by itself. Echo records `COMPLETE` only after acceptance evidence passes.

## Absolute project restrictions

Codex and Echo must not:

- Read, request, copy, print, rotate, or publish credentials or local secret configuration.
- Inspect or modify the dirty Mind workspace.
- Modify unrelated projects, the legacy Inference Control source, or any parent workspace.
- Start services, contact model runtimes, load/unload models, expose ports, or make provider calls.
- Change OS/BIOS/GPU settings, install Proxmox, create infrastructure, purchase resources, or delete files.
- Use GitHub Actions, Codespaces, Packages, deployments, runners, releases, LFS, or paid features.
- Publish private paths, private URLs, IPs, hostnames, logs, screenshots, or personal data.

## GitHub billing-safe declaration

```text
GitHub billing-safe mode is active. Repository content operations only. No Actions, Codespaces, Packages, deployments, runners, or paid features.
```
