# Project Overrides

Only additive restrictions are valid. These `*` rules do not weaken the locked doctrine.

- `*` Public project files must use repository-relative references or logical placeholders; never include local absolute paths, private URLs, IPs, hostnames, logs, screenshots, or credentials.
- `*` Codex may write only within the task-specific checkout and paths named by the active handoff. AIK-06 authorized only Control Chat navigation and its direct regression test; AIK-07 authorizes only the packet decision brief, HTML, receipt, and tracking entries—no code or owner decision.
- `*` Codex milestones are pushed only to `codex/<TASK-ID>` in the task-named repo; Echo verifies and promotes accepted content, never force-pushing or pushing directly to `main`.
- `*` The only application runtime authorized so far is the loopback-only Inference Control mock test; no real model/upstream action or remote exposure is authorized.
- `*` No GPU, Proxmox, VM/LXC, Tailscale, BIOS/OS, or firmware change is authorized until separate approval after hardware inspection.
- `*` Do not rotate the existing shared agent key under this project; do not read, copy, log, or publish it.
- `*` If a checker fails, record and repair the root cause within scope; never skip, weaken, or suppress the gate.
