---
name: dino-auth
description: >
  Check Dinox authentication status, and safely guide login/logout workflows.
version: 1.1.0
argument-hint: "[status|login|logout]"
allowed-tools:
  - Bash
metadata:
  requires:
    bins: ["dino"]
  cliHelp: "dino auth --help"
  category: "auth"
  risk: "mixed"
---

# Dinox Auth (Login / Logout / Status)

**CRITICAL — Before doing anything else, first read [`../dino-shared/SKILL.md`](../dino-shared/SKILL.md). It contains shared auth, output, and write-safety rules.**

Use this skill when the user asks about logging in, logging out, or checking auth status.

## Safety & Boundaries (Must Follow)

- Never ask the user to paste auth tokens into chat. For temporary auth, ask them to set `DINOX_TOKEN` in their own shell. For persistent auth, ask them to run `dino auth login "<token>"` in their own terminal session.
- `DINOX_TOKEN` takes precedence over saved config, may be a raw token or `Bearer ...`, and must never be echoed in chat or logs.
- Only run `dino ...` commands needed for this workflow. Do not run unrelated shell commands unless the user explicitly asks.
- `logout` is a write operation (it modifies local config). `logout --clear-local-db` is destructive. Show the exact command and get explicit confirmation before executing.

## Intent Mapping

- `status` / empty args: show current auth and sync connectivity info
- `login`: guide the user to use `DINOX_TOKEN` or run login locally, then verify
- `logout`: clear saved token (and optionally the local DB)

## Commands

Check status (recommended default):
```bash
dino auth status --format json
```

Logout:
```bash
dino auth logout
```

Logout and delete local SQLite cache (destructive):
```bash
dino auth logout --clear-local-db
```

Persistent login (user must run locally; do not request token in chat):
```bash
dino auth login "<token>"
```

Process-only login (user must set it locally; do not request token in chat):
```bash
export DINOX_TOKEN="<token-or-Bearer-token>"
dino auth status --format json
```

## Workflow

1. If the user asks to check login, run `dino auth status --format json` and summarize `loggedIn`, `userId`, and `dbPath`.
2. If the user asks to login, prefer process-only auth for automation: ask them to set `DINOX_TOKEN` in their own shell, then rerun `dino auth status --format json`. For persistent login, instruct them to run `dino auth login "<token>"` in their own terminal.
3. If the user asks to logout, show the exact logout command and ask for confirmation, then run it and verify with `dino auth status --format json`.

## Error Handling

- If status shows `loggedIn: no`, explain that the user must set `DINOX_TOKEN` or run `dino auth login` in their own terminal, never paste tokens into chat logs.
- If `dino auth login` fails because `DINOX_TOKEN` differs from the login token, ask the user to unset `DINOX_TOKEN` or use the same token.
- If a command reports `Missing resolved userId for the current authorization`, run an online `dino auth status --format json` after the user has set credentials so the CLI can resolve the token identity.
- If PowerSync endpoints are unset, suggest checking CLI install and then running `/dino-sync` after login.
