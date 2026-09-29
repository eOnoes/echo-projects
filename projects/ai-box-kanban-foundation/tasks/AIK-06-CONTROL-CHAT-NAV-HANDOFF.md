# AIK-06 — Repair Onoes-Control Chat navigation

**Project:** `ai-box-kanban-foundation`
**Task:** `AIK-06-CONTROL-CHAT-NAV`
**Status:** `READY` (becomes `IN_PROGRESS` on dispatch)
**Repository:** private Onoes-Control source repository, branch `codex/AIK-06-CONTROL-CHAT-NAV`
**Known base:** `12633d4c2ccc06a7dc9b25af80b56f3e249a05ab`
**Executor:** Codex, `gpt-6-sol`, medium reasoning

## Goal

Reproduce and fix the known Chat/CHAT navigation crash with the smallest functional change and a regression check.

## In scope

- Inspect the clean Control checkout only; map the existing Chat route, handler, and actual configured target before editing.
- Reproduce the crash with a focused local test/smoke check, fix the smallest cause, and add/adjust a regression test.
- Preserve the existing Control oversight/approval boundary.
- Keep Relay routing unchanged unless the destination and behavior are verifiable from already-authorized project evidence. Do not guess or invent a Relay URL; if unavailable, fix the crash and report the routing decision as unresolved.

## Out of scope / stop conditions

- No Mind or Relay source access; no changes to auth, approvals, execution authority, task storage, security policy, or unrelated Control UI.
- No deployment, public exposure, live user data, credentials, paid GitHub features, Actions, or CI.
- Do not stop/restart the already-running local dev server; use isolated tests/build checks.
- If the crash cannot be reproduced or a required route/target decision is unavailable, stop and report exact evidence rather than broadening scope.

## Acceptance

1. Root cause and exact changed files are recorded.
2. The Chat navigation path no longer crashes in the focused regression test/smoke check.
3. Existing relevant tests and build pass; `git diff --check` passes.
4. Diff is confined to Chat navigation and directly related tests/docs.
5. Commit and push only `codex/AIK-06-CONTROL-CHAT-NAV`; no merge to `master` until Echo verifies remote readback.
6. Report any unverified Relay destination separately; do not claim the Relay integration is complete.
