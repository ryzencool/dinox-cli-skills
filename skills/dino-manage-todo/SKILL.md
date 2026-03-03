---
name: dino-manage-todo
description: >
  Search, create, append, and update Dinox todo tasks. Use when the user asks
  to manage checklist items, complete/uncomplete tasks, or query todos by tag/time/status.
argument-hint: "[query or task text]"
allowed-tools:
  - Bash
  - Write
---

# Manage Dinox Todos

Use this skill when the user wants to work with `dino todo` commands.

## Important Principles

1. Always prefer `--json` output so downstream parsing is stable.
2. Do not pass `--offline` unless the user explicitly wants local-only cached data.
3. All todo subcommands (`search`, `append`, `create`, `update`) sync before execution unless `--offline` is set.
4. `content_json` is the source of truth; CLI auto-syncs `image_detail`, `content_md`, and `content_text`.
5. Date filters match `due_time` / `start_time`; if task time is missing, note `created_at` is used.

## Intent Mapping

- Search / browse todos -> `dino todo search`
- Add tasks to an existing note -> `dino todo append`
- Create a new todo note -> `dino todo create`
- Mark task done / undone -> `dino todo update`

## Search Todos

Base command:

```bash
dino todo search "$ARGUMENTS" --json
```

Common filters:

```bash
dino todo search --status uncompleted --json
dino todo search --tags "work,finance" --json
dino todo search --from 2026-03-01 --to 2026-03-07 --json
dino todo search --days 7 --json
dino todo search "报销" --status uncompleted --tags "work" --limit 100 --json
```

Output shape is AI-friendly:

- `meta`: query, filters, returned/total counts, truncation info
- `tasks`: each item includes `task_key`, `task_id`, `note_id`, `note_title`, `status`, `depth`, `parent_id`, time fields, tags

Tag filtering details:
- `--tags` matches task inline hashtags (task-level tags), not note-level tags

Present to user as a concise numbered list with:
- task text + completion status
- note title + task ID
- due/start time when present

## Append Tasks to Existing Note

Append one or multiple tasks:

```bash
dino todo append "补交报销单" --json
dino todo append --note-id "note-id" --task "补交报销单" --task "整理发票" --json
dino todo append --tasks '["补交报销单","整理发票"]' --json
```

For long task lists, write to file first, then:

```bash
dino todo append --tasks @/tmp/todos.txt --json
```

Behavior notes:
- If `--note-id` is omitted, CLI appends to latest note where `image_detail` is non-empty (`created_at` desc).
- If note ends with `taskList`, tasks are merged into it; otherwise CLI creates a new trailing `taskList`.

## Create New Todo Note

Create note with one or multiple tasks:

```bash
dino todo create "补交报销单" --json
dino todo create --task "补交报销单" --task "整理发票" --json
dino todo create --tasks @/tmp/todos.txt --title "本周财务待办" --json
```

Behavior notes:
- If `--title` is not provided, CLI auto-generates a summary title.

## Update Task Status

Mark a task completed/uncompleted by task ID:

```bash
dino todo update "task-id" --status completed --json
dino todo update "task-id" --status uncompleted --json
```

Accepted `--status` values:
- completed aliases: `completed`, `done`, `true`, `1`
- uncompleted aliases: `uncompleted`, `undone`, `todo`, `false`, `0`

## Recommended Workflow

1. For ambiguous natural language, clarify action first: search / append / create / update.
2. For `append`/`create`, normalize task list and remove empty entries before running command.
3. For `update`, confirm exact `taskId` and target status.
4. Execute command with `--json`, then summarize key fields (IDs, counts, status changes).

## Error Handling

- `Task not found`: ask user to run `todo search` first and pick an exact `task_id`.
- `Task id is not unique`: show matched note IDs and ask user to disambiguate target.
- `No eligible note found for append`: suggest `dino todo create` or provide `--note-id`.
- Auth/sync issues: ask user to run `dino auth login "Bearer <token>"` then retry.
