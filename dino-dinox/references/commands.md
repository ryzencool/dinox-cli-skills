# Dinox Command Reference

Read this file only when the CLI is unavailable for `dino schema`, or when the
user asks for a broad inventory of Dinox commands.

<!-- BEGIN GENERATED_REFERENCE -->
## Contents

- [Global Options](#global-options)
- [Commands Quick Reference](#commands-quick-reference)
- [Auth](#auth)
- [Daemon](#daemon)
- [Doctor](#doctor)
- [Sync](#sync)
- [Schema](#schema)
- [Update CLI](#update-cli)
- [Agent Skills](#agent-skills)
- [Notes](#notes)
- [Todo](#todo)
- [Tags](#tags)
- [Card Boxes (Zettel Boxes)](#card-boxes-zettel-boxes)
- [Prompts](#prompts)
- [Saved Views](#saved-views)
- [Storage](#storage)
- [Config](#config)
- [Info](#info)
- [Metadata](#metadata)
- [Graph](#graph)

## Global Options

| Flag | Description |
|------|-------------|
| `--format <yaml|json>` | Structured output format. Prefer `json` for agent and script integrations. |
| `--offline` | Skip sync, use local cache only |
| `--require-sync` | Fail if the local PowerSync cache cannot be confirmed fresh before reading |
| `--sync-timeout <ms>` | Override default 300000 ms (5 min) sync/connect timeout |
| `--verbose` | Enable verbose logging |

## Commands Quick Reference

### Auth
```text
dino auth login                    # Save login token and verify PowerSync connectivity
  --token-stdin                  # Read the login token from piped stdin instead of argv

dino auth logout                   # Clear saved login token and optionally remove the local cache
  --clear-local-db               # Delete the local PowerSync SQLite database

dino auth status                   # Show current login, local cache, and sync status
```

Agent safety: the positional login-token form is legacy compatibility and is intentionally omitted from this agent-facing signature. Agent workflows must never place a Dinox authentication token in argv; use `--token-stdin` from the user's own shell or secret store.

### Daemon
```text
dino daemon start                  # Start daemon process
  --port <number>                # Legacy compatibility field; daemon listens on a private local socket
  --no-detach                    # Run in foreground for debugging

dino daemon status                 # Show daemon status

dino daemon restart                # Restart daemon process
  --port <number>                # Legacy compatibility field; daemon listens on a private local socket

dino daemon stop                   # Stop daemon process
```

### Doctor
```text
dino doctor                        # Check Dinox CLI health across auth, sync, local indexes, daemon, and database integrity
  --fix                          # Safely repair local indexes, drain upload queue, and restart stale daemon
```

### Sync
```text
dino sync                          # Connect and synchronize the local PowerSync database
  --strict                       # Fail unless connected, a current checkpoint completes, downloads settle, and the local index finishes
```

### Schema
```text
dino schema [path]                 # Inspect Dinox CLI command schemas for agent-friendly usage
```

### Update CLI
```text
dino update                        # Update @dinoxx/dinox-cli to the latest version
  --package-manager <manager>    # Override package manager detection
```

### Agent Skills
```text
dino skills doctor                 # Check installed Dinox skills against this CLI version and find safely removable retired skills

dino skills migrate                # Preview or confirm safe removal of retired Dinox skills
  --dry-run                      # Preview safe removals without changing installed skills (default)
  --confirm                      # Execute the reviewed migration using the pinned public skills installer
```

### Notes
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
  --fields <list>                # Comma-separated fields: id,title,summary,tags,created_at,starred_at,boxes,is_starred
  --include-deleted              # Include soft-deleted notes

dino note get <id>                 # Get lightweight note context by id without exposing full note content
  --context-only                 # Compatibility flag; note get is context-only by default

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
  --include-content              # Include full markdown plus detailed block text and attributes

dino note create                   # Create a new note from markdown content
  --title <string>               # Note title
  --content <string|@file>       # Markdown content
  --type <note|crawl>            # Note type: note or crawl
  --tags <string|@file>          # Tag list (JSON array or comma/newline-separated)
  --boxes <string|@file>         # Box paths or unique names (JSON array or comma/newline-separated)
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Preview the write without executing it

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

### Todo
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

dino todo append [task]            # Append one or more tasks to an existing note
  --task <text>                  # Repeatable task text
  --tasks <string|@file>         # Task list (JSON array or comma/newline-separated)
  --note-id <id>                 # Target note id; omitted means latest eligible note
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Preview the write without executing it

dino todo create [task]            # Create a new note containing one or more todo items
  --task <text>                  # Repeatable task text
  --tasks <string|@file>         # Task list (JSON array or comma/newline-separated)
  --title <string>               # Optional note title
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Preview the write without executing it

dino todo update <taskId>          # Update a todo task checked status by task id
  --status <status>              # Target status: completed|uncompleted|done|undone|true|false|1|0
  --note-id <id>                 # Restrict task lookup to one exact note id
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Preview the write without executing it
```

### Tags
```text
dino tag list                      # List all tags from c_tag_node

dino tag tree                      # Show tags as a hierarchy tree from c_tag_node

dino tag stats                     # Show tag usage counts and unused-tag status

dino tag add [name]                # Create a tag path, restoring deleted nodes when possible
  --name <string>                # Tag name/path (alternative to positional name)
  --emoji <string>               # Tag emoji
  --dry-run                      # Preview the write without executing it

dino tag rename <path> <new-name>  # Rename a tag and cascade descendant paths and note references
  --dry-run                      # Preview the write without executing it

dino tag move <path>               # Move a tag under another parent and cascade descendant paths and note references
  --to <parent-path>             # Target parent tag path
  --dry-run                      # Preview the write without executing it

dino tag merge <from> <to>         # Merge a tag subtree into another tag and soft-delete the source subtree
  --expected-count <n>           # Required for real writes; must equal source tags plus notes affected
  --confirm                      # Required for real writes after reviewing a dry run
  --dry-run                      # Preview the write without executing it

dino tag suggest                   # Suggest likely duplicate tags to merge

dino tag cleanup                   # Inspect tag cleanup candidates
  --dry-run                      # Preview cleanup candidates without writing
```

### Card Boxes (Zettel Boxes)
```text
dino box list                      # List all zettel boxes from c_zettel_box

dino box add [path]                # Create a zettel box path, restoring deleted path nodes when possible
  --name <string>                # Box path (alternative to positional path)
  --description <string>         # Box purpose/usage description
  --color <string>               # Box color
  --dry-run                      # Preview the write without executing it

dino box tree                      # Show zettel boxes as a hierarchy tree

dino box stats                     # Show zettel box note counts and empty-box status

dino box rename <path> <new-name>  # Rename a zettel box and cascade descendant paths
  --dry-run                      # Preview the write without executing it

dino box move <path>               # Move a zettel box under another parent and cascade descendant paths
  --to <parent-path>             # Target parent box path
  --dry-run                      # Preview the write without executing it

dino box merge <from> <to>         # Merge a zettel box subtree into another box and soft-delete the source subtree
  --expected-count <n>           # Required for real writes; must equal source boxes plus notes affected
  --confirm                      # Required for real writes after reviewing a dry run
  --dry-run                      # Preview the write without executing it

dino box cleanup                   # Inspect zettel box cleanup candidates
  --dry-run                      # Preview cleanup candidates without writing
```

### Prompts
```text
dino prompt list                   # List reusable prompts from c_cmd

dino prompt add                    # Create a prompt template, restoring a deleted prompt when possible
  --name <string>                # Prompt name
  --prompt <string|@file>        # Prompt text or @file containing prompt text
  --dry-run                      # Preview the write without executing it
```

### Saved Views
```text
dino view list                     # List active saved table views
  --limit <n>                    # Maximum returned views (1-5000)

dino view get <id>                 # Get one saved view definition

dino view fields                   # List system and property fields available to saved views

dino view query <id>               # Query notes through a saved view filter and sort definition
  --limit <n>                    # Maximum returned notes (1-500)
  --offset <n>                   # Result offset for pagination

dino view count <id>               # Count notes matching a saved view

dino view create                   # Create a canonical saved table view
  --name <string>                # Saved view name
  --emoji <string>               # Optional emoji or short icon text
  --filter <json|@file>          # Canonical version 1 saved view filter JSON; defaults to no filter
  --config <json|@file>          # Canonical version 1 saved view config JSON; defaults to title + updated_at
  --template-id <id>             # Optional note template id associated with the view
  --group-id <id>                # Optional saved view group id
  --sort-rank <n>                # Explicit catalog sort rank
  --pinned-at <timestamp>        # Pinned timestamp
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Validate and preview the view without writing

dino view update <id>              # Update a canonical saved table view
  --name <string>                # Replace the saved view name
  --emoji <string>               # Replace emoji or short icon text
  --clear-emoji                  # Clear the saved view emoji
  --filter <json|@file>          # Replace the canonical version 1 filter JSON
  --config <json|@file>          # Replace the canonical version 1 config JSON
  --template-id <id>             # Replace the associated note template id
  --clear-template               # Clear the associated note template id
  --group-id <id>                # Replace the saved view group id
  --clear-group                  # Clear the saved view group id
  --sort-rank <n>                # Replace the catalog sort rank
  --clear-sort-rank              # Clear the catalog sort rank
  --pinned-at <timestamp>        # Replace the pinned timestamp
  --unpin                        # Clear the pinned timestamp
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Validate and preview the update without writing

dino view delete <id>              # Soft-delete a saved view
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Preview the soft delete without writing
```

### Storage
```text
dino storage list                  # List custom storage configs from c_storage and mark the active one

dino storage test                  # Upload a tiny temporary object to a custom S3 storage target without persisting a c_resource row
  --storage-id <id>              # Explicit storage config id (otherwise use active custom config)
  --dry-run                      # Preview the test object target without uploading

dino storage upload <file>         # Upload one local file to a custom S3 storage target and persist a c_resource row
  --storage-id <id>              # Explicit storage config id (otherwise use active custom config)
  --category <kind>              # Upload category: images|audios|files|videos
  --key <string>                 # Explicit object key override
  --overwrite                    # Replace existing objects addressed by an explicit --key
  --dry-run                      # Preview the upload target and resource record without uploading

dino storage stats                 # Summarize uploaded storage usage grouped by provider and bucket
```

### Config
```text
dino config get [key]              # Read sanitized Dinox CLI configuration values

dino config set <key> <value>      # Write configurable Dinox CLI settings
```

### Info
```text
dino info                          # Show CLI version and its matching standalone skills release
```

### Metadata
```text
dino meta stats                    # Get metadata stats (notes/tags/boxes)

dino meta schema                   # Get structured Dinox data schema for AI integrations
```

### Graph
```text
dino graph backlinks <id>          # Get notes that link to a target note

dino graph outlinks <id>           # Get notes that this note links to

dino graph related <id>            # Get notes related within N degrees of separation
  --depth <n>                    # Degrees of separation (1-5)

dino graph stats                   # Get graph statistics for notes and links
```
<!-- END GENERATED_REFERENCE -->
