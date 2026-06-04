<!-- BEGIN GENERATED_COMMANDS -->
## Command Surface

Use these generated commands as the canonical interfaces for note mutation workflows.

```text
dino note update [id]              # Full-replace note tags, boxes, and/or starred state
  --ids <string|@file>           # Batch note ids (JSON array or comma/newline-separated)
  --tags <string|@file>          # Full-replace tag list; use [] to clear
  --boxes <string|@file>         # Full-replace box paths or unique names; use [] to clear
  --starred <true|false>         # Set starred status
  --dry-run                      # Preview the write without executing it

dino note tag [id]                 # Incrementally add, remove, or replace note tags
  --ids <string|@file>           # Batch note ids (JSON array or comma/newline-separated)
  --add <string|@file>           # Tags to add
  --remove <string|@file>        # Tags to remove
  --replace <string|@file>       # Full replacement tag list; use [] to clear
  --dry-run                      # Preview the write without executing it

dino note move [id]                # Incrementally add, remove, or replace note zettel boxes
  --ids <string|@file>           # Batch note ids (JSON array or comma/newline-separated)
  --add <string|@file>           # Box paths or unique names to add
  --remove <string|@file>        # Box paths or unique names to remove
  --replace <string|@file>       # Full replacement box paths or unique names; use [] to clear
  --dry-run                      # Preview the write without executing it

dino note bulk [query]             # Bulk add, remove, or replace note tags or boxes using safe search filters
  --tags <expr>                  # Target filter: tag expression with AND/OR/NOT, or [] for empty tags
  --from <date>                  # Target filter: created_at start (YYYY-MM-DD or ISO datetime)
  --to <date>                    # Target filter: created_at end (YYYY-MM-DD or ISO datetime)
  --days <n>                     # Target filter: recent N days by created_at; mutually exclusive with --from/--to
  --starred <true|false>         # Target filter: starred status
  --boxes <string|@file>         # Target filter: box paths or unique names, or [] for empty boxes
  --all                          # Target all active notes; required when no other target filter is provided
  --tag-add <string|@file>       # Tags to add to matched notes
  --tag-remove <string|@file>    # Tags to remove from matched notes
  --tag-replace <string|@file>   # Full replacement tag list for matched notes; use [] to clear
  --box-add <string|@file>       # Box paths or unique names to add to matched notes
  --box-remove <string|@file>    # Box paths or unique names to remove from matched notes
  --box-replace <string|@file>   # Full replacement box list for matched notes; use [] to clear
  --expected-count <n>           # Required for real writes; must equal the current matched note count
  --confirm                      # Required for real writes after reviewing a dry run
  --dry-run                      # Preview matched targets and changes without executing

dino note star [id]                # Mark one or more notes as starred
  --ids <string|@file>           # Batch note ids (JSON array or comma/newline-separated)
  --dry-run                      # Preview the write without executing it

dino note unstar [id]              # Mark one or more notes as not starred
  --ids <string|@file>           # Batch note ids (JSON array or comma/newline-separated)
  --dry-run                      # Preview the write without executing it

dino note delete <id>              # Soft-delete a note by setting is_del=1
  --dry-run                      # Preview the write without executing it
```

- `--tags` and `--boxes` are full-replacement inputs.
- Prefer `note tag` and `note move` for incremental add/remove/replace metadata changes.
- Use `note bulk` for filter-based batch metadata changes; it never accepts `--sql`, and real writes require `--confirm --expected-count <n>`.
- Prefer `note star` / `note unstar` for pure starring changes, and `note update` when multiple fields change together.
- Run the same command with `--dry-run --format json` first.
<!-- END GENERATED_COMMANDS -->

## Target Resolution

1. If the user does not give an exact note ID, search first.
2. Before a destructive write, fetch lightweight context with `dino note get <id> --context-only --format json`.
3. For batch writes, confirm every target ID before executing.

## Update Workflow

1. Confirm the target note IDs and the final desired tags, boxes, or starred state.
2. If the user intent is incremental add/remove, fetch current state first and convert it into an explicit replacement set.
3. If the update also touches markdown content and that content includes local media/file paths, first read [media-resources](media-resources.md) and rewrite them into uploaded remote forms before updating the note content elsewhere in the workflow.
4. Validate candidate tag names with `dino tag list --format json`.
5. Validate candidate box names with `dino box list --format json`.
6. If any names are missing, ask whether to create them first.
7. Run the exact mutation command with `--dry-run --format json`.
8. Ask for confirmation.
9. Rerun without `--dry-run`, then summarize `updatedCount/total` or the deleted note ID.

## Important Rules

- Never silently convert an incremental request into a destructive full replacement.
- Never auto-create missing tags or boxes without permission.
- Never keep local filesystem media paths inside persisted note markdown; upload them first.
- For delete, always tell the user this is a soft-delete (`is_del=1`).
- Keep temp files for batch IDs under `/tmp/`.
