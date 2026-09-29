# Inference Control security readiness plan

**Task:** AIK-04-PLAN

**State:** Proposed for Echo review; no implementation or runtime readiness is claimed.

**Evidence boundary:** F1–F7 below are the verified, sanitized audit findings in [the task handoff](../tasks/AIK-04-PLAN-CODEX-HANDOFF.md). Every required behavior in this document is a proposed fix, not an observed property of the application.

## Gate and sequence

Until all applicable controls pass mock or fixture acceptance tests and independent review, deny model control and any live model or network testing. The order below minimizes the interval in which a route can bypass a boundary. Each stage should leave unsafe or unconfigured behavior denied.

| Order | Finding | Verified audit finding | Proposed required behavior | Dependency / gate |
|---|---|---|---|---|
| P0.1 | F1 | Hub API authorization is fail-open outside agent mode; mutating load/unload routes are served by the hub. | Make authorization fail closed for every hub mutation in every mode. Missing, invalid, or insufficient authorization denies the request before side effects. Define the permitted actor/operation policy in the later implementation task. | First boundary; no mutating route may remain reachable without it. |
| P0.2 | F2 | `/v1/*` and native Ollama proxy paths are dispatched before the API authorization check. | Apply authorization before any proxy dispatch, including those path families. A denied request must never reach an upstream or attach credentials. | Shares the F1 policy; review all route entry points after source mapping. |
| P0.3 | F7 | Agent authorization succeeds when `AGENT_KEY` is unset. | An unset or empty configured agent key must deny agent authorization; equality with an empty supplied value must not grant access. | Close alongside F1/F2; preserve the existing non-rotation decision. |
| P1.1 | F3 | Destructive operations lack an Origin/Referer or CSRF-token gate. | Require a valid request-origin/CSRF decision for browser-capable destructive calls; absent, invalid, or untrusted proof denies before mutation. Define the allowed-origin/token policy during implementation. | Must complement, not replace, F1 authorization. |
| P1.2 | F4 | `readBody()` accepts unbounded input and lacks abort/close handling. | Enforce a finite request-body limit before excessive buffering; stop reading and release work on abort/close; never invoke a mutation on oversized or incomplete input. | Apply at every relevant body-reading entry point found later. |
| P1.3 | F5 | Remote URLs are not restricted to approved schemes/hosts before credentials may be attached. | Validate the destination against an explicit approved scheme/host policy before forwarding or attaching any credential. Invalid, unapproved, or ambiguous destinations deny without outbound activity. | Depends on identifying all credential-attachment and forwarding paths in the exact-file task. |
| P2 | F6 | Model names are passed to CLI arguments without runtime-catalogue validation; context length is unbounded; unload-all lacks an adequate confirmation gate. | Resolve a requested model to an allowed runtime-catalogue entry before CLI invocation; enforce a finite, approved context-length range; require an explicit, operation-specific confirmation for unload-all. Reject failures before CLI or unload side effects. | Requires an authorized catalogue source, bounds, and confirmation policy; no live runtime is authorized by this plan. |

## Acceptance and stop rule

The [acceptance matrix](../reports/INFERENCE-CONTROL-SECURITY-ACCEPTANCE.md) specifies mock/fixture pass and fail observations for F1–F7, including negative controls. A later task must identify exact files, route coverage, upstream boundaries, policy values, and review ownership before editing code. Unknowns stay **UNRESOLVED**; they do not become permissive defaults. The exact code-path mapping cannot be derived from this packet and is **UNRESOLVED**.

This plan does not change the existing credential non-rotation decision. Credential access, migration, and disclosure are outside this task. The sanitized import remains unsuitable for live operation or exposure pending implementation, local tests, and independent security review under separate authorization.
