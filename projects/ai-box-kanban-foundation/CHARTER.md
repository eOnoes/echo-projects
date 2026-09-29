# AI Box and Shared Task Board Charter

## Mission

Turn the audits into an ordered, reviewable foundation for a safe shared task list and the AI-box dashboard. Make ownership, authority, and approval boundaries explicit before implementation.

## Problem

The current applications have different roles and incomplete contracts. Mind records internal execution work, Relay displays agent todo/goal data, and Control is an oversight surface. None is yet verified as the authoritative, authenticated cross-agent Kanban service the user wants. Separately, the located Inference Control prototype has security blockers and must not be treated as deployable.

## In scope — first three items

1. **Control boundary reconciliation:** compare the relevant Control docs against the supplied Mind audit; produce a contradiction matrix and proposed text; wait for Eddie’s approval before editing source docs.
2. **Inference Control discovery/import:** record the completed discovery, audit, sanitized standalone repository, and current security gate. Do not repeat discovery or touch legacy credential-bearing material.
3. **Kanban MVP requirements:** specify data, identity, authorization, task lifecycle, removal approvals, mutation history, idempotency, durable event delivery/replay, and acceptance tests. Keep this design-only.

## Success criteria

- Every ownership and execution-authority claim is consistent across the reviewed docs.
- A shared task can be attributed to a stable authenticated human or agent actor; request-supplied labels and a shared service key are not treated as identity.
- Board permissions, lifecycle transitions, approval-controlled removal, and audit retention are explicit.
- Clients can recover missed updates through a documented event cursor/replay contract.
- Mind, Relay, Control, and Inference Control have non-overlapping responsibilities documented.
- Each result has verifiable evidence and a recorded status.

## Out of scope

- Selecting or implementing the authoritative task database/API.
- Implementing Relay UI/feed or Control task views.
- Modifying Mind or connecting Mind to a board.
- Fixing the Inference Control code or authorizing its runtime use.
- Installing Proxmox, creating LXC/VMs, testing GPUs, or evaluating model performance.
- Secret rotation, deployments, public service exposure, or paid GitHub features.

## Adjacent safety gate

Onoes-Inference-Control’s security remediation is separate follow-on work. It must close authentication, request-boundary, validation, and destructive-action findings with tests and independent review before any live model or network test. This charter does not authorize those code changes.
