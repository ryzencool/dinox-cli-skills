---
name: dino-manage-tags
description: >
  List or create Dinox tags. Use when the user wants to see their tags,
  add new tags, or organize their tag hierarchy.
argument-hint: "[tag name to create]"
allowed-tools:
  - Bash
---

# Manage Dinox Tags

Help the user manage their Dinox tags.

## Instructions

### List Tags
If the user wants to see existing tags (or no argument is provided):
```bash
dino tag list --json
```
Present tags in a clean list. If there are many tags, group or categorize them.

### Create a Tag
If the user provides a tag name in `$ARGUMENTS` or asks to create one:
```bash
dino tag add "$ARGUMENTS" --json
```

With emoji:
```bash
dino tag add "tag-name" --emoji "🏷️" --json
```

## Tag Hierarchy

Tags support slash-separated hierarchy:
- `work` — top-level tag
- `work/projects` — nested tag under "work"
- `work/projects/frontend` — deeper nesting

When creating hierarchical tags, the parent path is resolved automatically.

## Workflow

1. If no arguments → list all tags
2. If argument provided → create the tag
3. After creating, confirm success and show the tag ID
4. If the user wants to see what notes use a tag, suggest `/dino-search-notes --tags "tagname"`
