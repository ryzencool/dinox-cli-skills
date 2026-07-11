<!-- BEGIN GENERATED_COMMANDS -->
## Command Surface

Use these generated commands as the canonical interfaces for note search and read workflows.

```text
dino note search [query]           # Search notes by keyword, tags, date range, boxes, or SQL-like filter
  --tags <expr>                  # Tag expression with AND/OR/NOT, or [] for empty tags
  --from <date>                  # created_at start (YYYY-MM-DD or ISO datetime)
  --to <date>                    # created_at end (YYYY-MM-DD or ISO datetime)
  --days <n>                     # Recent N days by created_at; mutually exclusive with --from/--to
  --starred <true|false>         # Filter by starred status
  --boxes <string|@file>         # Box paths or unique names (JSON array/comma list), or [] for empty boxes
  --sql <expr>                   # SQL-like expression over id/content_md/summary/tags/zettel_boxes/created_at/type/is_starred
  --limit <n>                    # Maximum returned notes
  --offset <n>                   # Result offset for pagination
  --fields <list>                # Comma-separated fields: id,title,summary,tags,created_at,boxes,is_starred
  --include-deleted              # Include soft-deleted notes

dino note get <id>                 # Get one note by id, optionally in lightweight context mode
  --context-only                 # Return lightweight note context (title/tags/summary/links)

dino note preview <id>             # Preview the first N lines of note markdown
  --lines <n>                    # Number of lines to return

dino note detail [id]              # Get full note details for one or more ids
  --ids <string|@file>           # Batch note ids (JSON array or comma/newline-separated)

dino note export [id]              # Export one or more notes as Markdown or JSON for backup and migration
  --ids <string|@file>           # Batch note ids (JSON array or comma/newline-separated)
  --type <markdown|json>         # Export format: markdown or json
  --output <path>                # Output file for one note, or output directory for multiple notes
  --overwrite                    # Replace existing export files when --output is used
  --include-deleted              # Allow exporting soft-deleted notes

dino note content-read <id>        # Read note content context and issue a short-lived token required before content patching
  --include-content              # Include full markdown content in the response
```

- Use `--boxes` for public box filters.
- Prefer `--fields id,title,summary,tags,created_at,boxes,is_starred` when the user only needs search metadata.
- Use `note export` for backup or migration; Markdown exports include frontmatter, JSON exports preserve note metadata and content JSON.
- Use `note content-read` immediately before `note patch`; the returned single-use readToken, content hash, block index, outline, and resolved hashtag paths are the required edit context. Run `content-read` again after every patch attempt.
- `--sql` remains storage-oriented and still uses the field name `zettel_boxes`.
<!-- END GENERATED_COMMANDS -->

## Search Filters

Tag filters support boolean logic:

- `work`
- `work AND life`
- `work OR life`
- `NOT archived`
- `(work OR life) AND NOT archived`

The `--sql` option accepts read-only SQL-like WHERE expressions over:
`id`, `content_md`, `summary`, `tags`, `zettel_boxes`, `created_at`, `type`, `is_starred`

Examples:

- `dino note search --sql 'type = "crawl"' --format json`
- `dino note search --sql 'type = "crawl" AND zettel_boxes IN ("Inbox","Project")' --format json`
- `dino note search --sql 'created_at >= "2026-01-01" AND summary LIKE "%AI%"' --format json`

## Read Path

1. For broad discovery, start with `dino note search ... --format json`.
2. For a known note, prefer `dino note get <id> --context-only --format json` first.
3. If the user needs a short excerpt, use `dino note preview <id> --lines 30 --format json`.
4. If the user explicitly wants full content, ask once for confirmation before `dino note detail <id> --format json`.

## Presentation

- For search results, show `id`, `title`, `summary`, `tags`, `created_at`, `boxes`, and starred state first.
- Mention that `summary` is already capped by the CLI.
- If no results are found, suggest broadening the query or relaxing filters.
- If a note has `is_del: 1`, tell the user it has already been soft-deleted.

## Search Behavior Notes

- Search uses FTS tokenization when available and falls back to `LIKE` search when FTS is unavailable.
- Search results return resolved `boxes` names, not box IDs.
- Use `note detail` only when the user needs full markdown or fields not included in search output.
