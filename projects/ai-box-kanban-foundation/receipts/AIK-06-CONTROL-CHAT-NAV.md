# AIK-06 receipt — Control Chat navigation fix

**Status:** `COMPLETE_WITH_LIMITATIONS` (branch verified; not merged)
**Branch/commit:** `codex/AIK-06-CONTROL-CHAT-NAV` / `6c2a86551a49412ad7c3c4c9a65b1ff9fab2c66c`
**Base:** `12633d4c2ccc06a7dc9b25af80b56f3e249a05ab`
**Model:** Codex `gpt-6-sol`, medium reasoning

## Change

- `src/App.jsx`: `AgentWorkspace` now reads the existing Control telemetry roster through `useTelemetry`, fixing the undefined `agents` render path.
- `tests/chat-navigation.test.mjs`: added a regression test rendering both overview and Chat workspace states under `TelemetryProvider`.
- Relay destination was not verified from Control configuration; routing was left unchanged. Demo composer remains disabled.

## Echo verification

- Exact diff scope: the two files above; normalized remote Git blob hashes match the local branch.
- `node --test tests/chat-navigation.test.mjs`: 1 passed, 0 failed. Vite emitted non-fatal dependency-scan shutdown diagnostics when this test was run alone; the test exited 0.
- `npm run build`: PASS.
- Full suite on the changed branch: 67 tests, 61 passed, 6 failed. The six failures are in fixture, Control-plane, and static-server tests.
- Baseline at the exact base commit: 66 tests, 59 passed, 7 failed; it reproduces the same six failing cases, plus a Sites-packaging test that fails when `dist` has not been built. Thus this change introduced no new full-suite failure.
- `git diff --check`: PASS; worktree clean; remote branch readback matched commit.

## Limitations

This is a branch-only UI fix. It is not merged or deployed. Full suite remains red on the same six pre-existing cases; the Chat regression and production build pass. No Relay integration, Mind access, credentials, or live runtime was used.
