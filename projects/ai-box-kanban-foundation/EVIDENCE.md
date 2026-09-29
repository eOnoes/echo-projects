# Evidence Register

Only verifiable claims belong here. Public packet entries contain no private paths, credentials, hostnames, or unredacted artifacts.

| ID | Claim | State | Source / pointer | Notes |
|---|---|---|---|---|
| `E-001` | Mind’s current `work_items` / `stage_executions` are internal execution tracking and are not a qualified general-purpose Kanban backend. | `PROVEN` | User-supplied read-only Mind audit conclusion, 2026-09-28 | G0–G7 remain open; no integration readiness implied. |
| `E-002` | Relay’s existing todo/goal displays are not a shared task service; a versioned authenticated task/feed contract is not established. | `PROVEN` | User-supplied read-only cross-app audit conclusion | Relay remains a candidate UI/chat surface. |
| `E-003` | Control is intended for oversight and approval; it must not authorize execution from Mind data. | `PROVEN` | User-supplied read-only Mind audit recommendation | Control persistence and boundary docs still require reconciliation. |
| `E-004` | The remembered model dashboard is Onoes-Inference-Control; a sanitized fresh-history private repository exists. | `PROVEN` | Verified local/remote import and secret scan; project name only | No private URL or credential copied into this public packet. |
| `E-005` | The dashboard is not ready for live runtime or network use until security fixes, tests, and independent review pass. | `PROVEN` | Sanitized dashboard security audit | AMD/ROCm capability is not established; no live model test is claimed. |
| `E-006` | The `eOnoes/echo-projects` control-plane repository is public and allows repository-content operations. | `PROVEN` | GitHub repository metadata readback | Initial project-packet publication is tracked separately by the commit and remote readback. |
| `E-007` | The exact Control-document contradictions and required wording are known. | `UNRESOLVED` | Cited matrix submitted and Echo-verified; Eddie’s wording decision remains pending. |
| `E-008` | The Kanban task/feed schema and authority model are fully specified. | `UNRESOLVED` | Pending AIK-03 | Must be independently reviewed before implementation. |
| `E-009` | The initial project packet is present on public `main` at commit `c975022afe3080f7e6bf1c6b612ed242acffd271`; remote readback matches. | `PROVEN` | GitHub commit and project-directory tree readback | Commit contains only the 12 project-packet files. |
| `E-010` | Eddie authorized AIK-01; Echo assigned Codex with `gpt-6-sol`, medium reasoning, and read-only source scope. | `PROVEN` | Owner instruction received in this project chat | No Control source edit, service, or runtime action is authorized. |
| `E-011` | The four named Control documents describe Control-owned oversight/approval, chat, audit, and frozen future command boundaries; their wording does not establish a canonical shared Kanban service. | `PROVEN` | [`CONTROL-BOUNDARY-MATRIX.md`](reports/CONTROL-BOUNDARY-MATRIX.md), C-01–C-12; [`AIK-01.md`](receipts/AIK-01.md) | Source lines and confidence labels are recorded per row; wording changes are proposals only. |
| `E-012` | AIK-01 proposed a common ownership/execution boundary paragraph and exact wording decisions for Eddie. | `PROVEN` | [`CONTROL-BOUNDARY-MATRIX.md`](reports/CONTROL-BOUNDARY-MATRIX.md), “Proposed common boundary paragraph” and “Exact next decision” | Echo verification passed; Eddie approval remains pending; no source edit. |
| `E-013` | The exact Control-document contradictions and required wording have been fully accepted. | `UNRESOLVED` | Supersedes the pending review in E-007; [`AIK-01-HANDOFF.md`](reports/AIK-01-HANDOFF.md) | Matrix is submitted, not accepted. |
| `E-014` | Codex pushed AIK-01 commit `91930e6b56b56c9de5a4877c87cf425488942f55` to `codex/AIK-01`; Echo verified the remote blobs, all seven paths are project-scoped, 22 citation ranges fit the source files and support the cited claims, and the Control checkout is unchanged. | `PROVEN` | Remote branch readback; independent source-document review; local/remote status checks | Proposed wording remains for Eddie; no Control source edit. |

## Evidence update rule

Append new IDs; never overwrite prior claims. Preserve the exact command/result or document section reference in a task receipt. If a claim changes, append a superseding entry with the reason and link the old ID.
