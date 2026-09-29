# AIK-04-PLAN — Inference Control security readiness plan

**Project:** `ai-box-kanban-foundation`
**Task:** `AIK-04-PLAN`
**Status:** `IN_PROGRESS`
**Executor:** Codex, `gpt-6-sol`, medium reasoning
**Manager/reviewer:** Echo

## Owner direction and task boundary

Eddie asked Echo to keep Codex working and get the project ready ahead of the estimated AI-box schedule: parts by Oct 6, assembly by Oct 11, boot/load testing on Oct 12. Those dates are estimates only.

This is **read-only planning**, not remediation. Do not inspect or modify the dashboard repository, source files, Git history, local runtime configuration, or any credential. Use only the sanitized findings below and this project packet. Code changes require a later, exact-file task and review. Live model/runtime testing remains prohibited until the security gate passes.

## Verified findings supplied to this planning task

The sanitized audit recorded these blockers; preserve them without broadening their claims:

1. Hub API authorization is fail-open outside agent mode; mutating load/unload routes are served by the hub.
2. `/v1/*` and native Ollama proxy paths are dispatched before the API authorization check.
3. Destructive operations lack an Origin/Referer or CSRF-token gate.
4. `readBody()` accepts unbounded input and lacks abort/close handling.
5. Remote URLs are not restricted to approved schemes/hosts before credentials may be attached.
6. Model names are passed to CLI arguments without runtime-catalogue validation; context length is unbounded; unload-all lacks an adequate confirmation gate.
7. Agent authorization succeeds when `AGENT_KEY` is unset.

Credential handling is outside this task: a key existed in legacy tracked material, was not copied into the sanitized repository, and Eddie directed no rotation. Never retrieve, print, copy, rotate, or publish it; do not inspect the legacy tree/history.

## Required deliverables

1. `deliverables/INFERENCE-CONTROL-SECURITY-PLAN.md` — prioritized, minimal fail-closed remediation sequence. Map each supplied finding to required behavior and dependencies. Distinguish verified audit findings from proposed fixes. Do not invent source file paths; note that exact code-path mapping belongs to a separately approved implementation task.
2. `reports/INFERENCE-CONTROL-SECURITY-ACCEPTANCE.md` — pass/fail tests for all seven findings, using mocks/fixtures only; include negative controls and the safe expected result.
3. `receipts/AIK-04-PLAN.md` — exact packet files written, checks, unresolved source-path mapping, and scope confirmation.
4. Update `TASKS.md`, append evidence IDs, and refresh `HANDOFF.md`.
5. `reports/AIK-04-PLAN-HANDOFF.md` and `.html` — TL;DR first, then drill-down and the authorization boundary for any later code work.

## Explicit exclusions

- No application source, tests, configs, lockfiles, or Git history read or written.
- No credential access, secret scanning of private source, rotation, or key migration.
- No code or test execution, services, network calls, live model connections, or runtime operations.
- No deploy, LAN/Tailscale/public exposure, paid resources, GitHub Actions, or CI.
- No claims that the dashboard is ready for use; this deliverable is a remediation plan only.

## Verification and branch

- Work only in `projects/ai-box-kanban-foundation/**` on `codex/AIK-04-PLAN`.
- Run `git diff --check`, relative Markdown link checks, HTML parsing, scope verification, and secret/private-path scan.
- Commit/push only the project packet to `codex/AIK-04-PLAN`; never push main or force-push. Echo independently verifies remote readback.

## Stop conditions

If any desired security behavior cannot be derived from the supplied findings without inspecting code or guessing, mark it `UNRESOLVED` and explain what the later source-mapping task must verify. Do not expand this task into remediation.
