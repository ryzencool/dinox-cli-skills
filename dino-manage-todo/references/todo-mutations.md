<!-- BEGIN GENERATED_COMMANDS -->
## Command Surface

Use these generated commands as the canonical mutation interfaces for todo workflows.

```text
dino todo append [task]            # Append one or more tasks to an existing note
  --task <text>                  # Repeatable task text
  --tasks <string|@file>         # Task list (JSON array or comma/newline-separated)
  --note-id <id>                 # Target note id; omitted means latest eligible note
  --dry-run                      # Preview the write without executing it

dino todo create [task]            # Create a new note containing one or more todo items
  --task <text>                  # Repeatable task text
  --tasks <string|@file>         # Task list (JSON array or comma/newline-separated)
  --title <string>               # Optional note title
  --dry-run                      # Preview the write without executing it

dino todo update <taskId>          # Update a todo task checked status by task id
  --status <status>              # Target status: completed|uncompleted|done|undone|true|false|1|0
  --dry-run                      # Preview the write without executing it
```

- Run the same command with `--dry-run` first.
- For append, prefer explicit `--note-id` unless the user confirms the default target behavior.
<!-- END GENERATED_COMMANDS -->
