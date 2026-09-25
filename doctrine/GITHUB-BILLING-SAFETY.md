# GitHub Billing Safety

**Status:** HARD-LOCK
**Version:** 1.0

## Absolute rule

No agent may use GitHub functionality that can consume paid minutes, paid storage, paid bandwidth, paid compute, or paid account allowances.

## Prohibited without explicit Eddie approval

- GitHub Actions workflow runs.
- Enabling, editing, or dispatching GitHub Actions workflows.
- GitHub-hosted runners.
- Self-hosted runner provisioning through GitHub.
- Codespaces.
- Packages, registries, or artifact publication.
- Large-file storage or Git LFS changes.
- Releases or release assets that create storage or bandwidth usage.
- Deployments, environments, Pages, or hosted services.
- Dependabot or other automated jobs that can generate workflow activity.
- Any GitHub feature that may generate a usage, billing, or allowance email.

## Allowed by default

- Git repository reads and writes.
- Commits, branches, pulls, and pushes.
- Markdown, JSON, schemas, plans, reports, and small evidence indexes.
- Read-only repository metadata.
- Local validation and secret scanning.

## Agent requirement

Before any GitHub operation, the agent must classify it as:

```text
REPOSITORY_CONTENT_OPERATION
or
POTENTIAL_BILLING_OPERATION
```

Only the first category is allowed autonomously.

If uncertain, stop and ask Eddie. Do not test the feature experimentally.

## Token rule

GitHub credentials must not include billing, Actions, Packages, Codespaces, runner, enterprise, or organization-management permissions unless Eddie explicitly authorizes them for a named task.

## Reporting

Every project handoff to Codex, MiMo, DeepSeek, or another agent must include this rule and state:

```text
GitHub billing-safe mode is active. Repository content operations only. No Actions, Codespaces, Packages, deployments, runners, or paid features.
```
