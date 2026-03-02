---
name: dinox
description: >
  Background knowledge about the Dinox CLI tool (`dino`). Use this context
  whenever the user mentions dinox, dino, notes, tags, card boxes, zettel boxes,
  or wants to interact with their knowledge base. This skill provides the
  full command reference so you can correctly invoke `dino` subcommands.
user-invocable: false
---

# Dinox CLI Reference

Dinox CLI (`dino`) is a command-line tool for managing a personal knowledge base
(notes, tags, card boxes / zettel boxes). Data is stored locally in SQLite and
synced to the cloud via PowerSync.

## Global Options

| Flag | Description |
|------|-------------|
| `--json` | Output machine-readable YAML |
| `--offline` | Skip sync, use local cache only |
| `--sync-timeout <ms>` | Override default 15 s sync timeout |
| `--verbose` | Enable verbose logging |

## Commands Quick Reference

### Auth
```
dino auth login "Bearer <token>"   # Login with token
  --no-verify                      #   Skip credential exchange & initial sync
dino auth logout                   # Logout
  --clear-local-db                 #   Also delete local SQLite database
dino auth status                   # Show auth status (userId, sync state, etc.)
```

### Sync
```
dino sync                          # Sync local data with cloud
```

### Notes
```
dino note search [query]           # Search notes (supports FTS + Chinese tokenization)
  --tags <expr>                    #   Tag expression: '(work OR life) AND NOT archived'
  --from <date>                    #   Filter by created_at start (YYYY-MM-DD or ISO)
  --to <date>                      #   Filter by created_at end
  --days <n>                       #   Recent N days
  --box <string|@file>             #   Filter by card box names (comma-separated or JSON array)
  --sql <expr>                     #   SQL-like expression over id/content_md/summary/tags/zettel_boxes/created_at/type
  --include-deleted                #   Include soft-deleted notes

dino note get <id>                 # Get note summary by ID
dino note detail [id]              # Get full note detail (single or batch)
  --ids <string|@file>             #   Batch note IDs (JSON array or comma/newline-separated)

dino note create                   # Create a new note
  --title <string>                 #   (required) Note title
  --content <string|@file>         #   (required) Markdown content (or @filepath)
  --type <note|crawl>              #   Note type (default: crawl)
  --tags <string|@file>            #   Tag names (JSON array or comma-separated)
  --zettel_boxes <string|@file>    #   Card box names (JSON array or comma-separated)

dino note update [id]              # Update note tags and/or card boxes (single or batch)
  --ids <string|@file>             #   Batch note IDs (JSON array or comma/newline-separated)
  --tags <string|@file>            #   Full-replace tag names (use [] to clear)
  --boxes <string|@file>           #   Full-replace card box names (use [] to clear)

dino note delete <id>              # Soft-delete a note
```

### Tags
```
dino tag list                      # List all tags
dino tag add <name>                # Create a tag (supports slash hierarchy like "work/projects")
  --emoji <string>                 #   Optional emoji for the tag
```

### Card Boxes (Zettel Boxes)
```
dino box list                      # List all card boxes
dino box add <name>                # Create a new card box
  --description <string>           #   Box purpose/usage description (helps AI route notes)
  --color <string>                 #   Box color
```

### Prompts
```
dino prompt list                   # List all prompts
dino prompt add                    # Create a prompt template in c_cmd
  --name <string>                  #   Prompt display name (required)
  --cmd <string>                   #   Prompt command/text (required)
  --sync-timeout <ms>              #   Override connect/sync timeout
  --offline                        #   Skip connect/sync and use local cache only
  --json                           #   Output machine-readable YAML
```

### Config
```
dino config get                    # View current config
dino config set <key> <value>      # Set a config value
```

### Info
```
dino info                          # Show CLI version
```

## Important Notes

- The `--content` and `--tags` options accept `@filepath` syntax to read from a file
- Tags must exist before being used in `note create`; create them first with `tag add`
- Card box names must exist before being used; create them first with `box add`
- `prompt add` fails fast when `--name` or `--cmd` is empty
- `prompt add` rejects active duplicates by `(name, cmd)` and restores soft-deleted duplicates
- If `c_cmd` includes `user_id`, `prompt add` requires a logged-in user (`dino auth login`)
- Search uses FTS + tokenization (with `@node-rs/jieba`); falls back to LIKE when needed
- `note search` returns streamlined fields: `id`, `title`, `summary`, `tags`, `created_at`, `zettel_boxes`
- Tag expressions support `AND`, `OR`, `NOT`, and parentheses
- The `--sql` option supports SQL-like WHERE conditions (read-only; no INSERT/UPDATE/DELETE); `zettel_boxes` values are matched by name and auto-resolved to IDs
- `note detail` supports batch read via `[id]` + `--ids`; at least one is required
- `note update` supports batch update via `[id]` + `--ids`; at least one is required
- `note update` requires at least one of `--tags`/`--boxes`, and both fields are full-replace semantics
- Notes use soft-delete (`is_del=1`), not permanent deletion
- Output is YAML format when `--json` is used
- If `dino` is not found, try `npx dino` or check that dinox-cli is installed globally
