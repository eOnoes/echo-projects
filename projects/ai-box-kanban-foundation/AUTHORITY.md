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

### Later, separately approved work

Only a new task can authorize edits to the exact listed Markdown files in Control. Any Inference Control security implementation requires a separate scope decision and task. This packet never authorizes live model operations.

## Allowed error handling

For a failure inside an assigned task, Codex must:

1. Preserve the exact failing command, result, affected file, and task ID in the task receipt.
2. Diagnose and fix the root cause only within its allowed paths.
3. Re-run the focused check, then the relevant complete check, and record exact results.
4. Keep unrelated or pre-existing failures distinct; do not claim they were fixed.
5. Stop as `BLOCKED` if repair needs wider scope, private credentials, permission changes, a live service, a provider call, or a decision from Eddie.

Codex must not skip/xfail a failing test, loosen an acceptance threshold, delete evidence, rewrite a failure as success, or continue under a different task without approval.

## Milestone recording and GitHub

- Codex records each completed milestone in the project packet: update `TASKS.md`, append evidence to `EVIDENCE.md`, refresh `HANDOFF.md`, write a task receipt, and provide Markdown plus HTML milestone handoff.
- After task assignment, Codex may create and push a scoped branch named `codex/<TASK-ID>` containing changes only under `projects/ai-box-kanban-foundation/`. No force push and no direct push to `main`.
- Echo reads back the remote branch, inspects the exact diff, independently runs/reads the stated checks, and only then promotes accepted project-packet changes to `main`.
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
