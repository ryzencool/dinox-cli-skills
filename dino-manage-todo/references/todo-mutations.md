<!-- BEGIN GENERATED_COMMANDS -->
## Command Surface

Use these generated commands as the canonical mutation interfaces for todo workflows.

```text
dino todo append [task]            # Add tasks to today's daily todo note, a given note, or under a task as subtasks
  --task <text>                  # Repeatable task text (inline Markdown: #tag, links, [#A] priority cookie)
  --tasks <string|@file>         # Task list (JSON array or comma/newline-separated)
  --items <json|@file>           # Structured tasks: JSON array of {text, checked?, priority?, schedule?: {date, time?}, subtasks?: [...]}
  --priority <priority>          # Priority for --task/--tasks: high|medium|low
  --due <date>                   # Due date for --task/--tasks (local YYYY-MM-DD)
  --time <HH:mm>                 # Due clock time with --due (local HH:mm); omit for an all-day due date
  --note-id <id>                 # Target note id; omitted means the daily todo note (created if missing)
  --parent <taskId>              # Add the tasks as subtasks of this task (combine with --note-id to skip the lookup)
  --date <date>                  # Daily target day (local YYYY-MM-DD); also the due date of tasks without one
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Preview the write without executing it

dino todo create [task]            # Create a new note containing one or more todo items
  --task <text>                  # Repeatable task text (inline Markdown: #tag, links, [#A] priority cookie)
  --tasks <string|@file>         # Task list (JSON array or comma/newline-separated)
  --items <json|@file>           # Structured tasks: JSON array of {text, checked?, priority?, schedule?: {date, time?}, subtasks?: [...]}
  --priority <priority>          # Priority for --task/--tasks: high|medium|low
  --due <date>                   # Due date for --task/--tasks (local YYYY-MM-DD)
  --time <HH:mm>                 # Due clock time with --due (local HH:mm); omit for an all-day due date
  --title <string>               # Optional note title
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Preview the write without executing it

dino todo update [taskId]          # Update a task's status, priority, or due time by task id (checking cascades to subtasks)
  --status <status>              # Target status: completed|uncompleted|done|undone|true|false|1|0
  --priority <priority>          # Set priority: high|medium|low, or none to clear
  --due <date>                   # Set the due date (local YYYY-MM-DD)
  --time <HH:mm>                 # Due clock time with --due (local HH:mm); omit for an all-day due date
  --clear-due                    # Remove the task's due time
  --updates <json|@file>         # Batch: JSON array of {task_id, note_id?, status?, priority?, schedule?: {date, time?}|null}
  --note-id <id>                 # Restrict task lookup to one exact note id
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Preview the write without executing it
```

- Run the same command with `--sync-timeout 20000 --dry-run --format json` first and keep the host timeout 5-10 seconds higher.
- For append without `--note-id`/`--parent`, tasks go to the daily todo note for today (or `--date`), which is created when that day has none; prefer an explicit target when the user names a note.
- Use `--items` for structured tasks (priority, schedule, subtasks); `--priority`/`--due`/`--time` apply to plain `--task`/`--tasks` text.
- Checking a task also checks its subtasks; unchecking one also unchecks its checked parents. Report `cascaded_task_ids` to the user.
- For update, pass the `note_id` returned by todo search as `--note-id` when available; broad task lookup is bounded and refuses truncated results. Legacy ids bind to the searched task snapshot, so search again after the note changes.
<!-- END GENERATED_COMMANDS -->

## Write Receipts

- Inspect returned write receipts under `data`: `durability`, `upload_queue_remaining`, `version`, `content_hash`, `changed`, and `stale`.
- Use `--durability uploaded` only when upload completion is required before success.

## Structured Items

`--items` takes a JSON array (or `@file`). Each entry is a string or an object:

```json
[
  { "text": "Book flights", "priority": "high", "schedule": { "date": "2026-11-10", "time": "09:30" } },
  { "text": "Pack", "subtasks": [{ "text": "Chargers" }, { "text": "Passport" }] }
]
```

- `priority`: `high` | `medium` | `low`; `schedule.time` is optional (omit for an all-day due date).
- Task text is inline Markdown, so `#tag` becomes a real tag and `[#A]` sets the priority.
- The result's `data.tasks` lists every created task with its new `task_id` and `parent_task_id`.

## Batch Updates

`dino todo update --updates <json|@file>` takes `[{ "task_id", "note_id"?, "status"?, "priority"?, "schedule"? }]`. `priority: "none"` or `null` clears it; `schedule: null` clears the due date. Each note is written once; read per-task results from `data.tasks` and per-note receipts from `data.notes`.

## Cascade

Checking a task checks all of its subtasks; unchecking a task also unchecks its checked parents. Parents never complete automatically. The tasks flipped this way are in `cascaded_task_ids`.
