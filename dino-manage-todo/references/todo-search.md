<!-- BEGIN GENERATED_COMMANDS -->
## Command Surface

Use this generated command surface as the canonical interface for todo search.

```text
dino todo search [query]           # Search todo tasks extracted from note content
  --status <status>              # Task status: all | completed | uncompleted
  --tags <string|@file>          # Tags every task must carry, via its own #tag or its note's tags (JSON array or comma/newline-separated)
  --priority <list>              # Priorities to keep, comma-separated: high,medium,low,none
  --from <date>                  # Task date range start (YYYY-MM-DD or ISO datetime); matches tasks whose schedule overlaps the range
  --to <date>                    # Task date range end (YYYY-MM-DD or ISO datetime)
  --days <n>                     # Recent N days; mutually exclusive with --from/--to
  --limit <n>                    # Maximum returned tasks
  --scan-limit <n>               # Maximum scanned note rows
  --cursor <token>               # Continue from meta.next_cursor of the previous page (same query)
  --include-deleted              # Include soft-deleted notes
```

- Execute online searches with `--sync-timeout 20000 --format json` and keep the host timeout 5-10 seconds higher.
<!-- END GENERATED_COMMANDS -->

## Output Shape

- `data.stale`: whether cache freshness could be proven
- `data.meta`: query, filters, returned/total counts, scan bounds, and truncation info
- `data.tasks`: each item includes `task_key`, `task_id`, `note_id`, `note_title`, `status`, `priority` (`high`/`medium`/`low`/`null`), `depth`, `parent_id`, `start_time`/`due_time`, `tags`, `note_tags`, and `note_box_ids`

## Completeness Rules

- `data.meta.truncated: true` means the returned task array is incomplete.
- `data.meta.scan_truncated: true` means not all eligible note rows were scanned.
- `data.meta.total_count_is_lower_bound: true` means `total_count` is not exact.
- Increase `--limit` for task-result truncation and `--scan-limit` for note-scan truncation, keeping both bounded.
- Do not infer task absence, uniqueness, or an exact count until freshness is current and all three flags above are false.

## Notes

- `--tags` matches a task's own `#tag` or its note's tags (a leading `#` and letter case are ignored); every listed tag must match
- A query matches the task text or its note title
- `--priority high,medium` keeps those priorities; `none` keeps tasks without one
- Date filtering keeps tasks whose schedule overlaps the range: start→due when both exist, a date-only due covers its whole local day, and an undated task sits on the local day its note was created
