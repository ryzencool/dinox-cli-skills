<!-- BEGIN GENERATED_COMMANDS -->
## Command Surface

Use these generated commands as the canonical interfaces for note mutation workflows.

```text
dino note update [id]              # Full-replace note metadata for explicit note ids
  --ids <string|@file>           # Batch note ids (JSON array or comma/newline-separated)
  --tags <string|@file>          # Replace the entire tag list; use [] to clear all tags
  --boxes <string|@file>         # Replace the entire box list; use [] to clear all boxes
  --starred <true|false>         # Replace the starred state
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Preview the write without executing it

dino note tag [id]                 # Incrementally organize note tags for explicit note ids
  --ids <string|@file>           # Batch note ids (JSON array or comma/newline-separated)
  --add <string|@file>           # Tags to add without removing existing tags
  --remove <string|@file>        # Tags to remove while preserving the rest
  --replace <string|@file>       # Replace the entire tag list; use [] to clear all tags
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Preview the write without executing it

dino note move [id]                # Incrementally organize note zettel boxes for explicit note ids
  --ids <string|@file>           # Batch note ids (JSON array or comma/newline-separated)
  --add <string|@file>           # Box paths or unique names to add without removing existing boxes
  --remove <string|@file>        # Box paths or unique names to remove while preserving the rest
  --replace <string|@file>       # Replace the entire box list; use [] to clear all boxes
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Preview the write without executing it

dino note patch <id>               # Patch note content structure after a required content-read token
  --append-section <heading>     # Append a new level-2 section at the end of the note
  --append-to-heading <path>     # Append content to an existing heading path
  --replace-section <path>       # Replace the body of an existing heading path
  --replace-block                # Replace one exact markdown block matched by --match
  --match <string|@file>         # Exact markdown to replace when using --replace-block
  --content <string|@file>       # Markdown content to insert
  --read-token <token>           # Required for real writes; returned by note content-read
  --allow-protected-replace      # Allow replace operations to affect media, table, container, or unknown blocks after reviewing content-read output
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Preview the patch without writing

dino note bulk [query]             # Bulk organize note tags or boxes using safe search filters
  --tags <expr>                  # Target filter: tag expression with AND/OR/NOT, or [] for empty tags
  --from <date>                  # Target filter: created_at start (YYYY-MM-DD or ISO datetime)
  --to <date>                    # Target filter: created_at end (YYYY-MM-DD or ISO datetime)
  --days <n>                     # Target filter: recent N days by created_at; mutually exclusive with --from/--to
  --starred <true|false>         # Target filter: starred status
  --boxes <string|@file>         # Target filter: box paths or unique names, or [] for empty boxes
  --all                          # Target all active notes; required when no other target filter is provided
  --tag-add <string|@file>       # Tags to add to every matched note
  --tag-remove <string|@file>    # Tags to remove from every matched note
  --tag-replace <string|@file>   # Replace the entire tag list on every matched note; use [] to clear all tags
  --box-add <string|@file>       # Box paths or unique names to add to every matched note
  --box-remove <string|@file>    # Box paths or unique names to remove from every matched note
  --box-replace <string|@file>   # Replace the entire box list on every matched note; use [] to clear all boxes
  --expected-count <n>           # Required for real writes; must equal the current matched note count
  --confirm                      # Required for real writes after reviewing a dry run
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Preview matched targets and changes without executing

dino note star [id]                # Mark one or more notes as starred
  --ids <string|@file>           # Batch note ids (JSON array or comma/newline-separated)
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Preview the write without executing it

dino note unstar [id]              # Mark one or more notes as not starred
  --ids <string|@file>           # Batch note ids (JSON array or comma/newline-separated)
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Preview the write without executing it

dino note delete <id>              # Soft-delete a note by setting is_del=1
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Preview the write without executing it
```

- `--tags` and `--boxes` are full-replacement inputs.
- `note update` is for explicit note ids and full metadata replacement. It is not an append command.
- `note tag` and `note move` are explicit-id incremental organizers: add/remove preserves existing values, replace overwrites the whole list.
- `note move` changes zettel box membership only; it does not move files or note content.
- `note patch` is only for structured content edits. Run `note content-read` immediately before it, pass `--read-token` for real writes, and use `--allow-protected-replace` only after reviewing protected blocks.
- `note bulk` is for filter-based batch metadata organization; it never accepts `--sql`, and real writes require `--confirm --expected-count <n>`.
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
9. Rerun without `--dry-run`, then summarize `updatedCount/total` or the deleted note ID plus receipt fields (`durability`, `upload_queue_remaining`, `version`, `content_hash`, `changed`).

## Important Rules

- Never silently convert an incremental request into a destructive full replacement.
- Never auto-create missing tags or boxes without permission.
- Never keep local filesystem media paths inside persisted note markdown; upload them first.
- For delete, always tell the user this is a soft-delete (`is_del=1`).
- Use `--durability uploaded` only when the user needs upload completion before success; otherwise inspect the returned durability and queue count.
- Keep temp files for batch IDs under `/tmp/`.
