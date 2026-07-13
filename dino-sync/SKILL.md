---
name: dino-sync
description: >
  Sync Dinox local cache (PowerSync) with the cloud and report status.
version: 1.1.1
argument-hint: "[--strict] [--sync-timeout <ms>]"
allowed-tools:
  - Bash
metadata:
  requires:
    bins: ["dino"]
  cliHelp: "dino sync --help"
  category: "sync"
  risk: "write"
---

# Sync Dinox Data

**CRITICAL — Before doing anything else, first read [`../dino-shared/SKILL.md`](../dino-shared/SKILL.md). It contains shared auth, output, and write-safety rules.**

Use this skill when the user asks to run a sync, refresh local cache, or debug sync staleness.

## Safety & Boundaries (Must Follow)

- Sync connects to the cloud and may upload local changes. Show the exact command you will run and get explicit confirmation before syncing.
- Only run `dino ...` commands needed for this workflow. Do not run unrelated shell commands unless the user explicitly asks.
- Do not ask the user to paste auth tokens into chat. If login is required, instruct them to set `DINOX_TOKEN` or pipe a token into `dino auth login --token-stdin` in their own terminal.

## Commands

Recommended (structured JSON output):
```bash
dino sync --sync-timeout 20000 --format json
```

Strict freshness gate for analysis:
```bash
dino sync --strict --sync-timeout 20000 --format json
```

Set the host tool timeout at least several seconds above the CLI timeout (for example, 25-30 seconds for the commands above).

## Workflow

1. Run `dino auth status --format json` first. If `loggedIn: no`, stop and ask the user to login in their own terminal.
2. Show the exact `dino sync ...` command you will run and ask for confirmation.
3. Use `dino sync --strict --sync-timeout 20000 --format json` before date-range analysis, latest-note claims, duplicates checks, exports, or other completeness-sensitive work. Strict mode requires an active connection, a checkpoint in the command's start second or later (PowerSync timestamps have whole-second precision), settled downloads with no download error, and a complete local token index.
4. Run the selected `dino sync ...` command, then summarize:
   - `dbPath`
   - `stale`, `downloadIdle`, `uploadIdle`, and `elapsedMs`
   - `gate.ok` and `gate.reasons`
   - `uploadEnabled`
   - `status.connected` and `status.lastSyncedAt`
   - `tokenIndex.complete` and counters (scanned/reindexed/skipped/removed)
5. Treat `gate.ok: true` and `stale: false` as fresh even when compatibility field `idle` is false because uploads are still active. Download freshness no longer waits for the upload queue.
6. Structured success is emitted only after the foreground database closes, its owner lease is released, and daemon restoration finishes.

## Error Handling

- If sync times out (`stale: true`), inspect `gate.reasons`. `index-timeout` means local indexing was interrupted and is resumable. Do not blindly retry with progressively larger timeouts inside an agent; first ensure the host timeout exceeds the CLI timeout, then retry the 20-second command once or run `dino doctor --format json`.
- If strict sync fails with `SYNC_REQUIRED`, do not make data completeness claims until the user retries successfully or explicitly accepts stale/offline results.
- If auth errors occur, instruct the user to set `DINOX_TOKEN` or pipe a token into `dino auth login --token-stdin` in their own terminal (do not paste tokens into chat), then retry.
