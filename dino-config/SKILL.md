---
name: dino-config
description: >
  Read or set Dinox CLI configuration values (e.g. sync.timeoutMs).
argument-hint: "[get|set] [key] [value]"
allowed-tools:
  - Bash
metadata:
  requires:
    bins: ["dino"]
  cliHelp: "dino config --help"
  category: "config"
  risk: "mixed"
---

# Configure Dinox CLI

**CRITICAL — Before doing anything else, first read [`../dino-shared/SKILL.md`](../dino-shared/SKILL.md). It contains shared auth, output, and write-safety rules.**

Use this skill when the user asks to read or change CLI config (especially sync timeout).

## Safety & Boundaries (Must Follow)

- `config set` is a write operation. Show the exact command and get explicit confirmation before writing.
- Only run `dino ...` commands needed for this workflow. Do not run unrelated shell commands unless the user explicitly asks.
- Do not store secrets in config via this skill.

## Supported Keys

- `sync.timeoutMs` (positive integer milliseconds)

## Commands

For agent / script integrations, prefer structured JSON output.

View all config (sanitized; powersync section is hidden):
```bash
dino config get --format json
```

Read one key:
```bash
dino config get sync.timeoutMs --format json
```

Set timeout (example: 20 seconds):
```bash
dino config set sync.timeoutMs 20000
```

## Workflow

1. If the user asks to inspect config, run `dino config get --format json`.
2. If the user asks to change `sync.timeoutMs`, confirm the target value is a positive integer and ask for confirmation.
3. Run `dino config set sync.timeoutMs <ms>`, then verify with `dino config get sync.timeoutMs --format json`.

## Error Handling

- If CLI reports `Unknown config key`, explain that currently only `sync.timeoutMs` is configurable.
- If value is invalid, ask for a positive integer milliseconds value (e.g. `20000`).
