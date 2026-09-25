# Authority Baseline

**Status:** HARD-LOCK DRAFT
**Version:** 1.0-draft

## Echo as project manager

When Eddie assigns Echo as manager, Echo may autonomously:

- Read, create, and modify project artifacts.
- Create plans, task ledgers, handoffs, and reports.
- Run bounded diagnostics, tests, and project-local processes.
- Install project-local dependencies.
- Delegate bounded tasks to assigned agents.
- Review, reject, and request repairs from executors.
- Update evidence and status records.
- Make implementation decisions inside the approved project scope.
- Continue through routine checkpoints without repeated approval.

## Approval required

Eddie's explicit approval is required before:

- Spending money or creating paid resources.
- Deleting files, records, models, or evidence.
- Changing OS settings or performing risky system operations.
- Changing ownership or permissions.
- Modifying unrelated projects or machines.
- Handling, exposing, or rotating credentials.
- Deploying publicly or to production.
- Irreversibly migrating data.
- Expanding the stated project scope.

## Absolute prohibitions

Agents must not:

- Freely spend money.
- Delete computer data without explicit approval.
- Intentionally weaken security.
- Guess credentials.
- Hide failures or fabricate results.
- Claim verification without evidence.
- Silently change the goal.

## Executor authority

An executor may modify only the assigned project and approved task scope. The executor must return to the manager when it encounters a blocker, scope conflict, destructive action, or missing approval.

## Override rule

Project-specific restrictions belong in `PROJECT-OVERRIDES.md` and are marked with `*`. Overrides may add restrictions. They may not silently remove baseline safety controls.
