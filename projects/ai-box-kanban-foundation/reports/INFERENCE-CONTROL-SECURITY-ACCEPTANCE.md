# Inference Control security acceptance matrix

**Task:** AIK-04-PLAN

**Status:** Proposed tests only. No code, tests, service, CLI, upstream, or model was run.

**Fixture rule:** Use an in-process request harness with fake authorization, body stream, catalogue, CLI, and upstream sinks. Assert both response and zero forbidden side effects. No real credentials or destinations are needed. F1–F7 refer only to the [sanitized task findings](../tasks/AIK-04-PLAN-CODEX-HANDOFF.md).

| Case | Finding | Mock/fixture input and negative control | Pass / safe expected result | Fail condition |
|---|---|---|---|---|
| S-01 | F1 | Exercise load and unload hub mutations in each supported mode with missing, invalid, and insufficient authorization; control: permitted actor with required operation grant. | All denied cases fail before mutation or CLI invocation; permitted control reaches only its allowed handler. | Any mode permits an unauthorized mutation or denies the permitted control because of an unintended blanket block. |
| S-02 | F2 | Send `/v1/*` and native Ollama proxy requests with missing/invalid authorization; control: an authorized request to each route family. | Denied cases yield no upstream request and no credential attachment; authorized controls proceed only to the fake upstream. | A denied request reaches dispatch/upstream, or an authorized control bypasses its expected policy. |
| S-03 | F3 | Attempt each destructive action with absent, malformed, and untrusted Origin/Referer or CSRF proof; control: authorized actor with valid policy-approved proof. | Invalid-proof cases cause no mutation; valid proof proceeds to the fake mutation only when F1 authorization also passes. | Proof alone authorizes a call, or missing/untrusted proof mutates state. |
| S-04 | F4 | Feed a body above the finite configured limit and a stream that aborts/closes mid-body; control: complete body within limit. | Oversize and incomplete streams terminate without mutation, further buffering, or lingering work; the complete control reaches parsing/validation. | Unbounded accumulation, side effect after abort/close, or an in-limit complete request rejected by body handling. |
| S-05 | F5 | Supply disallowed scheme, unapproved host, and ambiguous URL fixtures; control: an explicitly approved scheme/host. | Disallowed fixtures produce no outbound call and no credential attachment; approved control forwards only to the fake destination under policy. | Credential attachment or forwarding precedes validation, or an unapproved destination passes. |
| S-06 | F6 | Supply a model name absent from the fixture runtime catalogue and one present; inspect fake CLI arguments. | Unknown name fails before CLI invocation; known name resolves to the catalogue entry and reaches only the fake CLI. | Arbitrary input reaches CLI or valid catalogue entry cannot be selected. |
| S-07 | F6 | Supply context lengths below/above approved finite bounds and at valid boundaries; inspect fake CLI. | Out-of-range or nonfinite values fail before CLI; valid boundaries are accepted. | Unbounded/invalid context reaches CLI or valid boundary is refused. |
| S-08 | F6 | Request unload-all without confirmation, with wrong/stale confirmation, and with explicit operation-specific valid confirmation; use a fake unload sink. | Missing/wrong/stale confirmation causes zero unloads; valid confirmation permits only the authorized fake unload-all operation. | Unload-all runs without adequate confirmation or confirmation bypasses F1 authorization. |
| S-09 | F7 | Configure agent key as unset and empty with missing/empty supplied key; control: configured nonempty fixture key and matching supplied value, plus mismatch. | Unset/empty configuration and mismatch deny; matching fixture value passes only the agent auth check and still obeys operation policy. | Empty equals empty and grants access, or agent auth bypasses F1/F2. |

## Test prerequisites and unresolved design inputs

- **UNRESOLVED:** Exact source files, route inventory, mode list, and order of checks. A separately approved exact-file implementation task must map each entry and prove complete coverage.
- **UNRESOLVED:** Permitted actors and grants, browser origin/CSRF policy, request-size and context bounds, approved remote scheme/host list, catalogue source, and unload-all confirmation semantics. Set these before implementation; absence must deny.
- No acceptance row establishes live runtime readiness. After implementation, local mock/fixture results and independent review must be recorded before any separately authorized live testing.
