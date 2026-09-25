# GitHub Access Checklist

## Preferred method: browser login

Use GitHub CLI device/browser authentication from the computer:

```text
gh auth login --hostname github.com --git-protocol https --web
```

This avoids copying a token into Telegram. After login, verify:

```text
gh auth status
gh api user --jq .login
```

## Repository operations needed

For the public `echo-projects` repository, Echo needs to:

- Create the repository under Eddie's account.
- Push commits to the default branch.
- Create branches if needed.
- Read repository metadata and contents.
- Update files and commit documentation.
- Read commit history and diffs.
- Create and merge pull requests if that workflow is enabled.
- Create or update issues only if explicitly adopted for project tracking.
- Run repository secret scanning and inspect workflow results.

## Safest GitHub access model

Use the GitHub CLI browser/device flow rather than manually providing a token.

If a token is required, prefer a fine-grained personal access token limited to the `echo-projects` repository.

Recommended fine-grained permissions for documentation management:

- Metadata: Read-only.
- Contents: Read and write.
- Commit statuses: Read-only.
- Pull requests: Read and write, only if PR workflow is used.
- Issues: Read and write, only if issue tracking is used.
- Actions: Read-only initially.
- Administration: Avoid unless repository creation or settings management requires it.

## Account-level repository creation

A fine-grained token may not be able to create a new repository depending on GitHub account policy. The simplest safe path is:

1. Authenticate `gh` in the browser.
2. Let the CLI create `echo-projects`.
3. Verify the remote repository.
4. After creation, restrict ongoing access to the repository itself where possible.

## Never provide

Do not send any of these through Telegram or chat:

- Classic PATs.
- Fine-grained PAT values.
- SSH private keys.
- OAuth client secrets.
- Recovery codes.

## Verification after access

Echo must verify:

- Authenticated account name.
- Repository owner.
- Repository visibility.
- Default branch.
- Successful push.
- Read-back of the exact committed files.
- Secret scan result.
