---
name: view-note
description: >
  View full details of Dinox notes by ID. Use when the user wants to read,
  view, or open one or more specific notes.
argument-hint: "[note-id]"
allowed-tools:
  - Bash
---

# View Dinox Note

The user wants to view full content of one or more notes.

## Instructions

1. Get the target note ID(s) from `$ARGUMENTS` or user message.
2. Use `dino note detail` for full note fields.
3. If the user asks for lightweight metadata only, use `dino note get`.
4. Present note content in a readable format.

## Commands

Get one note detail:
```bash
dino note detail $ARGUMENTS --json
```

Get multiple note details:
```bash
dino note detail --ids "id-1,id-2,id-3" --json
```

Get note summary (without full content markdown):
```bash
dino note get $ARGUMENTS --json
```

## Presenting the Note

Display the note with clear formatting:
- **Title** prominently displayed
- **Tags** listed if present
- **Card Boxes** listed if present
- **Created** date
- **Content** rendered as Markdown
- **Type** and other metadata if relevant

Note: `note detail` output is streamlined and focuses on `id`, `created_at`, `title`, `type`, `source`, `tags`, `is_del`, `content_md`, and resolved `zettel_boxes` names.

If the note has `is_del: 1`, inform the user it has been soft-deleted.

## Error Handling

- If no ID is provided, ask the user or suggest searching first with `/search-notes`
- If both positional ID and `--ids` are missing, remind that `note detail` requires at least one of them
- If note is not found, suggest searching for it
- If the ID looks wrong (too short, etc.), suggest running a search
