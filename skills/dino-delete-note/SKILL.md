---
name: dino-delete-note
description: >
  Soft-delete a Dinox note by ID (sets is_del=1). Use only when the user explicitly requests deletion.
argument-hint: "<note-id>"
allowed-tools:
  - Bash
---

# Delete (Soft-Delete) a Dinox Note

Use this skill when the user explicitly asks to delete a note by ID.

## Safety & Boundaries (Must Follow)

- Deleting a note is a write operation. Always show the exact command you will run and get explicit confirmation before deleting.
- Prefer to fetch context first so the user can confirm they are deleting the correct note.
- Do not ask the user to paste auth tokens into chat. If auth is required, instruct them to run `dino auth login "<token>"` in their own terminal.

## Commands

Inspect note context (recommended before delete):
```bash
dino note get "$ARGUMENTS" --context-only --json
```

Soft-delete note:
```bash
dino note delete "$ARGUMENTS" --json
```

## Workflow

1. Confirm the note ID from `$ARGUMENTS`.
2. Run `dino note get <id> --context-only --json` and show title/summary/tags/boxes to confirm the target.
3. Show the exact `dino note delete <id> --json` command you will run and ask for confirmation.
4. Run the delete and report success. Mention that this is a soft-delete (`is_del=1`).

## Error Handling

- If the note is not found, suggest using `/dino-search-notes` to locate the correct ID.
- If auth error occurs, instruct the user to run `dino auth login "<token>"` in their own terminal (do not paste tokens into chat), then retry.

