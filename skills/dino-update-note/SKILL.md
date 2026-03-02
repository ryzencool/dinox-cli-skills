---
name: dino-update-note
description: >
  Update Dinox note tags and/or card boxes. Supports single-note and batch
  updates by ID.
argument-hint: "[note-id]"
allowed-tools:
  - Bash
  - Write
---

# Update Dinox Notes

Use this skill when the user wants to modify note tags or card boxes on existing notes.

## Required Inputs

1. Target note IDs:
   - Single note: positional `[note-id]`
   - Batch notes: `--ids <string|@file>`
   - At least one of `[note-id]` or `--ids` is required.
2. At least one update field:
   - `--tags <string|@file>`
   - `--boxes <string|@file>`

## Important Behavior

- `--tags` and `--boxes` are **full replacement** fields, not incremental add/remove.
- Passing `[]` clears the corresponding field.
- Tag names and box names must already exist; create missing ones first if needed.
- Batch updates deduplicate IDs automatically.

## Update Commands

Single note:
```bash
dino note update "note-id" --tags "work,ai" --boxes "Inbox,Project" --json
```

Batch update:
```bash
dino note update --ids "note-1,note-2,note-3" --tags "work" --json
```

Use files for long lists:
```bash
dino note update --ids @/tmp/note-ids.txt --boxes @/tmp/box-names.txt --json
```

Clear tags or card boxes:
```bash
dino note update "note-id" --tags "[]" --json
dino note update "note-id" --boxes "[]" --json
```

## Recommended Workflow

1. Confirm target note ID(s) and which fields to replace (`tags`, `boxes`, or both).
2. If needed, run `dino tag list --json` and `dino box list --json` to validate names.
3. Create missing tags/boxes first when user agrees:
   - `dino tag add "<tag-name>" --json`
   - `dino box add "<box-name>" --json`
4. Run `dino note update ... --json`.
5. Report `updatedCount/total`, and list any `updated: false` IDs as no-op updates.

## Error Handling

- If neither `--tags` nor `--boxes` is provided: tell user at least one is required.
- If no note IDs are provided: ask for `[note-id]` or `--ids`.
- If note IDs include deleted or missing notes: report IDs and ask user to fix targets.
- If tags/boxes are unknown: show missing names and offer to create them first.
