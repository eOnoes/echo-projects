# Public Publication Safety

**Status:** HARD-LOCK DRAFT
**Version:** 1.0-draft

This doctrine repository may be public.

## Allowed

- Goals, plans, doctrines, task status, sanitized reports, public source links, logical artifact names, and redacted evidence indexes.

## Prohibited

Never publish:

- API keys, tokens, passwords, cookies, SSH keys, private keys, or connection strings.
- Private IPs, hostnames, ports, service topology, or account identifiers.
- Exact local paths containing usernames or sensitive directory names.
- Private logs, screenshots, model files, credentials, or personal information.

Use placeholders such as:

```text
<LOCAL_PROJECT_ROOT>
<PRIVATE_REPOSITORY>
<REDACTED_HOST>
<PRIVATE_EVIDENCE_LOCATION>
```

## Required pre-push gate

Before every push:

1. Review the diff.
2. Run secret scanning.
3. Check for private paths, URLs, logs, and screenshots.
4. Confirm that only public-safe content is present.

A successful scanner does not replace human review for logs and screenshots.
