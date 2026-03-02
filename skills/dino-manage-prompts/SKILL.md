---
name: dino-manage-prompts
description: >
  List or create Dinox prompts. Use when the user wants to see reusable prompts,
  add a new prompt template, or manage prompt commands in `c_cmd`.
argument-hint: "[name] [cmd]"
allowed-tools:
  - Bash
---

# Manage Dinox Prompts

Help the user manage prompts via `dino prompt` commands.

## Instructions

### List Prompts
If the user wants to browse prompts (or no argument is provided):

```bash
dino prompt list --json
```

When the user explicitly wants local-only cached data:

```bash
dino prompt list --offline --json
```

Present each item with:
- Prompt name (`name`)
- Prompt text (`cmd`)

### Create a Prompt
To add a prompt, collect both fields:
- **Name** (required)
- **Cmd / Prompt text** (required)
- Prefer explicit flags (`--name`, `--cmd`) when values include spaces.

Then run:

```bash
dino prompt add --name "周报助手" --cmd "请基于本周笔记输出一份简洁周报" --json
```

Optional runtime controls:

```bash
dino prompt add \
  --name "周报助手" \
  --cmd "请基于本周笔记输出一份简洁周报" \
  --sync-timeout 20000 \
  --json
```

## Workflow

1. If no create intent or missing arguments, run `dino prompt list --json`.
2. If creating, validate `name` and `cmd` are non-empty before executing.
3. Execute `dino prompt add ... --json` so response includes `id`, `name`, `cmd`, and `restored`.
4. If response has `restored: true`, tell the user an existing soft-deleted prompt was restored.
5. If add fails due to duplicate (`Prompt already exists`), inform the user and suggest reusing existing prompt.

## Error Handling

- **Not logged in** (`Missing persisted userId` / `Run dino auth login first`):
  ask the user to run `dino auth login "Bearer <token>"`, then retry.
- **Sync timeout warning**:
  mention results may be stale and suggest retry with higher `--sync-timeout`.
- **Missing required input**:
  ask for both prompt name and prompt text before running `prompt add`.
