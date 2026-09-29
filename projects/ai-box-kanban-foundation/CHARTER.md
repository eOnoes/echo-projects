# AI Box and Shared Task Board Charter

## Mission

Turn the audits into an ordered, reviewable foundation for a safe shared task list and the AI-box dashboard. Make ownership, authority, and approval boundaries explicit before implementation.

## Problem

The current applications have different roles and incomplete contracts. Mind records internal execution work, Relay displays agent todo/goal data, and Control is an oversight surface. None is yet verified as the authoritative, authenticated cross-agent Kanban service the user wants. Separately, the located Inference Control prototype has security blockers and must not be treated as deployable.

## In scope — items 1–10, staged by dependency

1. **Control boundary reconciliation:** compare the relevant Control docs against the supplied Mind audit; produce a contradiction matrix and proposed text; wait for Eddie’s approval before editing source docs.
2. **Inference Control discovery/import:** record the completed discovery, audit, sanitized standalone repository, and current security gate. Do not repeat discovery or touch legacy credential-bearing material.
3. **Kanban MVP requirements:** specify data, identity, authorization, task lifecycle, removal approvals, mutation history, idempotency, durable event delivery/replay, and acceptance tests. Keep this design-only.
4. **Authoritative Kanban store/contract:** D-1–D-5 policy directions are recorded; integrate them into the contract and acceptance matrix, complete three distinct audit/refinement rounds, and obtain Eddie's approval before selecting technical details or implementing a service.
5. **Relay adapter/UI:** only after item 4; use the single reviewed service contract, not a duplicate task store.
6. **Control oversight views:** only after the contract exists; show task history/approvals without granting execution authority.
7. **Control Chat navigation:** repair the known crash in a bounded task; do not guess a Relay URL or route.
8. **Mind G0–G7:** tracked as a separate active effort; do not duplicate Codex work or inspect the dirty Mind workspace.
9. **Hardware/topology:** inspect actual delivered parts and PCIe/IOMMU/reset behavior; no purchase or system changes.
10. **Initial Proxmox boundary:** decide from item 9 evidence; an isolated Linux VM with PCI passthrough remains a candidate, not a deployment authorization.

## Success criteria

- Every ownership and execution-authority claim is consistent across the reviewed docs.
- A shared task can be attributed to a stable authenticated human or agent actor; request-supplied labels and a shared service key are not treated as identity.
- Board permissions, lifecycle transitions, approval-controlled removal, and audit retention are explicit.
- Clients can recover missed updates through a documented event cursor/replay contract.
- Mind, Relay, Control, and Inference Control have non-overlapping responsibilities documented.
- Each result has verifiable evidence and a recorded status.

## Out of scope

- Deploying a production Kanban service or choosing implementation technology before the owner-approved contract passes three distinct audit/refinement rounds; any private prototype requires its own bounded implementation task after design approval.
- Building Relay task features or Control task views before the single service contract is approved.
- Modifying Mind or connecting it to a board; Mind G0–G7 remains a separate task.
- Live-mode Inference Control remediation, model operations, upstream calls, or remote/public exposure under the local-mock task.
- Installing Proxmox, creating VMs/LXCs, changing BIOS/OS/GPU firmware, or testing GPUs before hardware delivery and separate authorization.
- Secret rotation, public deployment, or paid GitHub features.

## Adjacent safety gate

Onoes-Inference-Control’s live-mode security remediation is separate follow-on work. It must close authentication, request-boundary, validation, and destructive-action findings with tests and independent review before any real model or network test. Eddie has separately authorized a loopback-only mock prototype for local UI/workflow trials; that mock does not grant live-mode readiness. This charter does not authorize real model operations.
