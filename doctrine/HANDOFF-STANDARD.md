# Handoff Standard

**Status:** HARD-LOCK DRAFT
**Version:** 1.0-draft

## Purpose

Allow another agent or a later session to continue without reconstructing the project from chat history.

## Required handoff fields

```text
Project ID
Canonical GOAL.md location
Current phase
Current task
Last completed action
Verified evidence
Current blocker
Next safe action
Do-not-do list
Assigned executor
Required return package
```

## Executor return package

The executor must return:

1. Files changed.
2. Tests and exact results.
3. Commit or artifact identifiers.
4. Evidence locations.
5. Scope confirmation.
6. Remaining blockers.
7. Recommended next action.

## Handoff rule

A handoff is not complete until the manager can identify the next safe action and the evidence that supports the current status.
