<!-- BEGIN GENERATED_COMMANDS -->
## Command Surface

Use this generated command surface as the canonical interface for todo search.

```text
dino todo search [query]           # Search todo tasks extracted from note content
  --status <status>              # Task status: all | completed | uncompleted
  --tags <string|@file>          # Task tags (JSON array or comma/newline-separated)
  --from <date>                  # Task date range start (YYYY-MM-DD or ISO datetime)
  --to <date>                    # Task date range end (YYYY-MM-DD or ISO datetime)
  --days <n>                     # Recent N days; mutually exclusive with --from/--to
  --limit <n>                    # Maximum returned tasks
  --scan-limit <n>               # Maximum scanned note rows
  --include-deleted              # Include soft-deleted notes
```

- Prefer `--format json` for machine-readable search output.
<!-- END GENERATED_COMMANDS -->

## Output Shape

- `meta`: query, filters, returned/total counts, truncation info
- `tasks`: each item includes `task_key`, `task_id`, `note_id`, `note_title`, `status`, `depth`, `parent_id`, time fields, tags

## Notes

- `--tags` matches task inline hashtags, not note-level tags
- Date filtering checks `due_time` / `start_time`; when task time is missing, note `created_at` is used
