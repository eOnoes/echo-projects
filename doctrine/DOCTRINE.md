# Agent Project Doctrine

**Status:** HARD-LOCK DRAFT
**Version:** 1.0-draft
**Owner:** Eddie
**Manager default:** Echo

## Purpose

Provide one agent-agnostic operating doctrine for planning, delegating, executing, reviewing, handing off, and closing projects.

## Core rule

The project documents are authoritative. The executing agent is replaceable.

## Goal command

Projects are selected by a stable command:

```text
Goal: <project-id>
```

The command resolves to the project's canonical `GOAL.md`.

## Required behavior

1. Read `GOAL.md` first.
2. Follow its linked documents.
3. Confirm project, phase, task, authority, and executor before acting.
4. Perform only the current approved task.
5. Record evidence for meaningful work.
6. Report using the standard response format.
7. Stop when approval, missing evidence, scope conflict, or safety uncertainty is encountered.
8. Apply `GITHUB-BILLING-SAFETY.md` to every GitHub operation.

## Scope protection

A project may not silently become a different project. New work requires a documented scope decision and an update to the goal packet.

## Evidence protection

Claims must be labeled `PROVEN`, `INFERRED`, `UNRESOLVED`, `DISPROVEN`, or `BLOCKED`.

## Agent neutrality

The doctrine applies equally to Echo, Codex, MiMo, DeepSeek, Claude, Kimi, Relay agents, and Eddie-assisted execution. Roles are assigned per project.

## GitHub billing safety

The default GitHub mode is repository-content-only. Agents may read and write repository files, commits, branches, and documentation. Agents may not use Actions, Codespaces, Packages, runners, deployments, hosted services, or other potentially billable GitHub functions without explicit Eddie approval.

## Hard-lock rule

This doctrine is not changed for convenience during a build. A project-specific exception must be recorded in `PROJECT-OVERRIDES.md` using the asterisk convention and must not weaken safety, evidence, scope, or billing controls.

## Reusable lessons

`PITFALLS-AND-LESSONS.md` is a living, append-only record of pitfalls found across projects and
the practices that resolved them. It applies to all projects, not one.

- **Read it before starting any model-compression or quantization campaign.**
- **Append to it whenever a new pitfall is discovered.** Never overwrite an entry.
- A lesson learned twice is a process failure, not bad luck.
