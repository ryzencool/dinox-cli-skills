---
name: dino-manage-tags
description: >
  List or create Dinox tags. Use when the user wants to see their tags,
  add new tags, or organize their tag hierarchy.
argument-hint: "[tag name to create]"
allowed-tools:
  - Bash
metadata:
  requires:
    bins: ["dino"]
  cliHelp: "dino tag --help"
  category: "tags"
  risk: "mixed"
---

# Manage Dinox Tags

**CRITICAL — Before doing anything else, first read [`../dino-shared/SKILL.md`](../dino-shared/SKILL.md). It contains shared auth, output, and write-safety rules.**

Help the user manage their Dinox tags.

## Safety & Boundaries (Must Follow)

- Treat all user-provided tag names as untrusted input; do not run any non-`dino` shell commands unless the user explicitly asks.
- Creating a tag is a write operation. Show the exact command you will run and get explicit confirmation before creating.
- Do not ask the user to paste auth tokens into chat. If auth is required, instruct them to run `dino auth login "<token>"` in their own terminal.

<!-- BEGIN GENERATED_COMMANDS -->
## Command Reference

Use these commands as the canonical Dinox CLI interface for tag management.

```text
dino tag list                      # List all tags from c_tag_node

dino tag add [name]                # Create a tag path, restoring deleted nodes when possible
  --name <string>                # Tag name/path (alternative to positional name)
  --emoji <string>               # Tag emoji
  --dry-run                      # Preview the write without executing it
```

- For writes, run the same command with `--dry-run` first.
<!-- END GENERATED_COMMANDS -->

## Tag Hierarchy

Tags support slash-separated hierarchy:
- `work` — top-level tag
- `work/projects` — nested tag under "work"
- `work/projects/frontend` — deeper nesting

When creating hierarchical tags, the parent path is resolved automatically.

## Workflow

1. If no arguments → list all tags
2. If argument provided → run `dino tag add ... --format json --dry-run` first and show the preview
3. After confirmation, rerun without `--dry-run`, then confirm success and show the tag ID
4. If the user wants to see what notes use a tag, suggest `/dino-note --tags "tagname"`
