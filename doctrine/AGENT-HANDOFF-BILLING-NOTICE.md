# Agent Handoff: GitHub Billing-Safe Mode

Paste this notice into every handoff to Codex, MiMo, DeepSeek, or another coding agent:

```text
GitHub billing-safe mode is active.

You may use repository content operations only:
- read files
- create or update files
- commit changes
- create branches
- pull and push repository content

Do not use or enable:
- GitHub Actions
- workflow dispatch
- hosted runners
- self-hosted runner registration
- Codespaces
- Packages or registries
- deployments or hosted services
- Pages
- releases with large assets
- Git LFS
- Dependabot jobs
- billing, organization, enterprise, or runner administration

If an operation might consume a paid allowance or trigger a billing email, stop and ask Eddie. Do not test it experimentally.
``` 
