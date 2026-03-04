---
name: dino-auth
description: >
  Check Dinox authentication status, and safely guide login/logout workflows.
argument-hint: "[status|login|logout]"
allowed-tools:
  - Bash
---

# Dinox Auth (Login / Logout / Status)

Use this skill when the user asks about logging in, logging out, or checking auth status.

## Safety & Boundaries (Must Follow)

- Never ask the user to paste auth tokens into chat. If login is required, ask them to run `dino auth login "<token>"` in their own terminal session.
- Only run `dino ...` commands needed for this workflow. Do not run unrelated shell commands unless the user explicitly asks.
- `logout` is a write operation (it modifies local config). `logout --clear-local-db` is destructive. Show the exact command and get explicit confirmation before executing.

## Intent Mapping

- `status` / empty args: show current auth and sync connectivity info
- `login`: guide the user to run login locally, then verify
- `logout`: clear saved token (and optionally the local DB)

## Commands

Check status (recommended default):
```bash
dino auth status --json
```

Logout:
```bash
dino auth logout
```

Logout and delete local SQLite cache (destructive):
```bash
dino auth logout --clear-local-db
```

Login (user must run locally; do not request token in chat):
```bash
dino auth login "<token>"
```

## Workflow

1. If the user asks to check login, run `dino auth status --json` and summarize `loggedIn`, `userId`, and `dbPath`.
2. If the user asks to login, instruct them to run `dino auth login "<token>"` in their own terminal, then rerun `dino auth status --json` to verify.
3. If the user asks to logout, show the exact logout command and ask for confirmation, then run it and verify with `dino auth status --json`.

## Error Handling

- If status shows `loggedIn: no`, explain that `dino auth login` must be run in the user's terminal and never pasted into chat logs.
- If PowerSync endpoints are unset, suggest checking CLI install and then running `/dino-sync` after login.

