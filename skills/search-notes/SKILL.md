---
name: search-notes
description: >
  Search and browse Dinox notes. Use when the user wants to find notes by
  keyword, tags, date range, or browse recent notes.
argument-hint: "[keyword or topic]"
allowed-tools:
  - Bash
---

# Search Dinox Notes

The user wants to search their Dinox notes. Use the `dino` CLI to find notes.

## Instructions

1. Run `dino note search` with the user's query using `--json` for structured output
2. Present the results in a clear, readable format (title, summary, date, tags)
3. If the user wants to see a specific note's full content, use `dino note detail <id>`

## Search Command

```bash
dino note search "$ARGUMENTS" --json
```

## Available Filters

Combine these based on what the user asks for:

- **Keyword**: `dino note search "keyword" --json`
- **By tags**: `dino note search --tags "(tag1 OR tag2) AND NOT archived" --json`
- **By date range**: `dino note search --from 2024-01-01 --to 2024-12-31 --json`
- **Recent N days**: `dino note search --days 7 --json`
- **By card box**: `dino note search --box "Inbox" --json`
- **By SQL expression**: `dino note search --sql 'type = "crawl" AND zettel_boxes IN ("Inbox","Project")' --json`
- **Include deleted**: `dino note search --include-deleted --json`
- **Combined**: `dino note search "keyword" --tags "work" --box "Inbox" --days 30 --json`

## Presenting Results

- Show results as a numbered list with title, summary (truncated), and date
- If no results found, suggest broadening the search or trying different keywords
- Offer to show full details of any note the user is interested in
- If the user selects a note, run `dino note detail <id> --json` to show the full content

## Tag Expression Syntax

Tag filters support boolean logic:
- `work` — notes tagged "work"
- `work AND life` — notes with both tags
- `work OR life` — notes with either tag
- `NOT archived` — notes without "archived" tag
- `(work OR life) AND NOT archived` — combined expression

## SQL Expression Syntax (`--sql`)

The `--sql` option accepts SQL-like WHERE conditions over these fields:
`id`, `content_md`, `summary`, `tags`, `zettel_boxes`, `created_at`, `type`

Supported operators: `=`, `!=`, `>`, `>=`, `<`, `<=`, `LIKE`, `NOT LIKE`, `IN`, `NOT IN`, `AND`, `OR`, `NOT`

Examples:
- `--sql 'type = "crawl"'`
- `--sql 'type = "crawl" AND zettel_boxes IN ("Inbox","Project")'`
- `--sql 'created_at >= "2026-01-01" AND summary LIKE "%AI%"'`

Note: `zettel_boxes` values are matched by box name and auto-resolved to IDs. Only read-only conditions are allowed (no INSERT/UPDATE/DELETE).

## Error Handling

- If `dino` is not found, tell the user to install dinox-cli: `npm install -g @dinoxx/dinox-cli`
- If auth error occurs, suggest running `dino auth login "Bearer <token>"`
- If sync times out, results may be stale — inform the user
