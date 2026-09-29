# Project Overrides

Only additive restrictions are valid. These `*` rules do not weaken the locked doctrine.

- `*` Public project files must use repository-relative references or logical placeholders; never include local absolute paths, private URLs, IPs, hostnames, logs, screenshots, or credentials.
- `*` Codex may write only under this project packet during AIK-01 and AIK-03. Source-repository edits require a separate Eddie-approved task naming exact files.
- `*` Codex milestones are pushed only to `codex/<TASK-ID>`; Echo verifies and promotes accepted content to `main`.
- `*` No application, model, GPU, Proxmox, VM/LXC, or Tailscale runtime work is authorized.
- `*` Do not rotate the existing shared agent key under this project; do not read, copy, log, or publish it.
- `*` If a checker fails, record and repair the root cause within scope; never skip, weaken, or suppress the gate.
