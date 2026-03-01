---
name: view-note
description: >
  View full details of a Dinox note by ID. Use when the user wants to read,
  view, or open a specific note.
argument-hint: "<note-id>"
allowed-tools:
  - Bash
---

# View Dinox Note

The user wants to view the full content of a specific note.

## Instructions

1. Get the note ID from `$ARGUMENTS` or ask the user
2. Fetch the full note details
3. Present the note content in a readable format

## Commands

Get full note details (with content):
```bash
dino note detail $ARGUMENTS --json
```

Get note summary (without full content):
```bash
dino note get $ARGUMENTS --json
```

## Presenting the Note

Display the note with clear formatting:
- **Title** prominently displayed
- **Tags** listed if present
- **Card Boxes** listed if present
- **Created / Updated** dates
- **Content** rendered as Markdown
- **Type** and other metadata if relevant

If the note has `is_del: 1`, inform the user it has been soft-deleted.

## Error Handling

- If no ID is provided, ask the user or suggest searching first with `/search-notes`
- If note is not found, suggest searching for it
- If the ID looks wrong (too short, etc.), suggest running a search
