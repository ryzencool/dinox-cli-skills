---
name: dino-sync
description: >
  Sync Dinox local cache (PowerSync) with the cloud and report status.
argument-hint: "[--sync-timeout <ms>]"
allowed-tools:
  - Bash
---

# Sync Dinox Data

Use this skill when the user asks to run a sync, refresh local cache, or debug sync staleness.

## Safety & Boundaries (Must Follow)

- Sync connects to the cloud and may upload local changes. Show the exact command you will run and get explicit confirmation before syncing.
- Only run `dino ...` commands needed for this workflow. Do not run unrelated shell commands unless the user explicitly asks.
- Do not ask the user to paste auth tokens into chat. If login is required, instruct them to run `dino auth login "<token>"` in their own terminal.

## Commands

Recommended (machine-readable YAML output):
```bash
dino sync --json
```

With a higher timeout:
```bash
dino sync --sync-timeout 600000 --json
```

## Workflow

1. Run `dino auth status --json` first. If `loggedIn: no`, stop and ask the user to login in their own terminal.
2. Show the exact `dino sync ...` command you will run and ask for confirmation.
3. Run `dino sync --json`, then summarize:
   - `dbPath`
   - `stale` and `idle` warnings (if present)
   - `uploadEnabled`
   - `status.connected` and `status.lastSyncedAt`
   - `tokenIndex` counters (scanned/reindexed/skipped/removed)

## Error Handling

- If sync times out (`stale: true`), explain the cache may be stale and suggest retrying with a higher `--sync-timeout`.
- If auth errors occur, instruct the user to run `dino auth login "<token>"` in their own terminal (do not paste tokens into chat), then retry.

