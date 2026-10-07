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

- Execute online searches with `--sync-timeout 20000 --format json` and keep the host timeout 5-10 seconds higher.
<!-- END GENERATED_COMMANDS -->

## Output Shape

- `data.stale`: whether cache freshness could be proven
- `data.meta`: query, filters, returned/total counts, scan bounds, and truncation info
- `data.tasks`: each item includes `task_key`, `task_id`, `note_id`, `note_title`, `status`, `depth`, `parent_id`, time fields, and tags

## Completeness Rules

- `data.meta.truncated: true` means the returned task array is incomplete.
- `data.meta.scan_truncated: true` means not all eligible note rows were scanned.
- `data.meta.total_count_is_lower_bound: true` means `total_count` is not exact.
- Increase `--limit` for task-result truncation and `--scan-limit` for note-scan truncation, keeping both bounded.
- Do not infer task absence, uniqueness, or an exact count until freshness is current and all three flags above are false.

## Notes

- `--tags` matches task inline hashtags, not note-level tags
- Date filtering checks `due_time` / `start_time`; when task time is missing, note `created_at` is used
