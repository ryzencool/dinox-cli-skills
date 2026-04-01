<!-- BEGIN GENERATED_COMMANDS -->
## Command Surface

Use these generated commands as the canonical interfaces for note mutation workflows.

```text
dino note update [id]              # Full-replace note tags, boxes, and/or starred state
  --ids <string|@file>           # Batch note ids (JSON array or comma/newline-separated)
  --tags <string|@file>          # Full-replace tag list; use [] to clear
  --boxes <string|@file>         # Full-replace box names; use [] to clear
  --starred <true|false>         # Set starred status
  --dry-run                      # Preview the write without executing it

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
