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

## Scope protection

A project may not silently become a different project. New work requires a documented scope decision and an update to the goal packet.

## Evidence protection

Claims must be labeled `PROVEN`, `INFERRED`, `UNRESOLVED`, `DISPROVEN`, or `BLOCKED`.

## Agent neutrality

The doctrine applies equally to Echo, Codex, MiMo, DeepSeek, Claude, Kimi, Relay agents, and Eddie-assisted execution. Roles are assigned per project.

## Hard-lock rule

This doctrine is not changed for convenience during a build. A project-specific exception must be recorded in `PROJECT-OVERRIDES.md` using the asterisk convention and must not weaken safety, evidence, or scope controls.
