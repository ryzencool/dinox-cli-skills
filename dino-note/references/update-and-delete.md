# Update, Organize, And Delete Notes

## Contents

- [Command Surface](#command-surface)
- [Target Resolution](#target-resolution)
- [Mutation Routing](#mutation-routing)
- [Write Workflow](#write-workflow)
- [Important Rules](#important-rules)

<!-- BEGIN GENERATED_COMMANDS -->
## Command Surface

Use these generated commands as the canonical interfaces for note mutation workflows.

```text
dino note update [id]              # Full-replace note metadata (title, tags, boxes, starred) for explicit note ids
  --ids <string|@file>           # Batch note ids (JSON array or comma/newline-separated)
  --title <title>                # Replace the note title (single note id only; content is unchanged)
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
  --read-token <token|@file>     # Single-use patch capability returned by note content-read; prefer @file to avoid exposing it in argv
  --allow-protected-replace      # Allow replacement of media, table, container, or unknown blocks only with a reviewed full-content read token
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
- `note patch` is only for structured content edits. Run `note content-read` immediately before it and put the short-lived patch capability in an exclusively created temporary file with owner-only permissions (0600 on POSIX; an owner-only ACL on Windows), then pass `--read-token @<file>` and delete the file immediately after the attempt. Default capabilities have `data.readTokenScope: structural-metadata`; `--allow-protected-replace` requires a reviewed `content-read --include-content` result with `data.readTokenScope: full-content`. Every real attempt consumes the capability, so read again before retrying.
- `note bulk` is for filter-based batch metadata organization; it never accepts `--sql`, and real writes require `--confirm --expected-count <n>`.
- Prefer `note star` / `note unstar` for pure starring changes, and `note update` when multiple fields change together.
- Run the same online command with `--sync-timeout 20000 --dry-run --format json` first and keep the host timeout 5-10 seconds higher.
<!-- END GENERATED_COMMANDS -->

## Target Resolution

1. If the user does not give an exact note ID, search first.
2. Before a destructive write, fetch lightweight context with `dino note get <id> --context-only --sync-timeout 20000 --format json`.
3. For batch writes, confirm every target ID before executing.

## Mutation Routing

- Add or remove tags while preserving the rest: `note tag --add` or `--remove`.
- Add or remove boxes while preserving the rest: `note move --add` or `--remove`.
- Replace the complete tag or box set only when explicitly requested: `note tag --replace`, `note move --replace`, or metadata-only `note update`.
- Change only starred state: `note star` or `note unstar`.
- Rename a note (change only its title): `note update <id> --title "New title"`. One note id per call; the body is untouched. Do not use `note content-read` plus `note patch` for title changes.
- Change Markdown content: read [content-edit](content-edit.md); never use a nonexistent `note update --content` option.
- Apply one metadata change to a filtered set: `note bulk`, with an explicit target filter, dry-run, confirmation, and expected count.
- Soft-delete one exact note: `note delete`.

## Write Workflow

1. Resolve and verify the target IDs. For incremental metadata changes, use `note tag` or `note move` directly instead of reconstructing a full replacement set.
2. Validate candidate tags with `dino tag list --sync-timeout 20000 --format json` and boxes with `dino box list --sync-timeout 20000 --format json`. Ask before creating anything missing.
3. Run the exact mutation with `--sync-timeout 20000 --dry-run --format json`; inspect `data.targets`, `data.changes`, `data.stale`, and `data.sync.gate`. Keep the host timeout 5-10 seconds higher.
4. If the user already requested that exact incremental edit or star change and the preview matches, execute without another prompt. Always ask again before delete, bulk mutation, or complete replacement.
5. For bulk writes, pass the preview's current matched count as `--expected-count <n>` together with `--confirm`.
6. Rerun without `--dry-run`, then summarize `data.updatedCount`/`data.total` or the deleted note ID plus `data.durability`, `data.upload_queue_remaining`, `data.version`, `data.content_hash`, `data.changed`, and `data.stale`.

## Important Rules

- Never silently convert an incremental request into a destructive full replacement.
- Never auto-create missing tags or boxes without permission.
- Never keep local filesystem media paths inside persisted note markdown; upload them first.
- For delete, always tell the user this is a soft-delete (`is_del=1`).
- Use `--durability uploaded` only when the user needs upload completion before success; otherwise inspect the returned durability and queue count.
- Create batch-ID files in the host operating system's temporary directory and never overwrite an existing path.
