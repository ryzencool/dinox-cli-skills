---
name: manage-boxes
description: >
  List or create Dinox card boxes (zettel boxes). Use when the user wants to
  see their card boxes, create new boxes, or organize notes into boxes.
argument-hint: "[box name to create]"
allowed-tools:
  - Bash
---

# Manage Dinox Card Boxes

Help the user manage their Dinox card boxes (zettel boxes).

## Instructions

### List Card Boxes
If the user wants to see existing boxes (or no argument is provided):
```bash
dino box list --json
```
Present boxes in a clean list.

### Create a Card Box
If the user provides a box name in `$ARGUMENTS` or asks to create one:
```bash
dino box add "$ARGUMENTS" --json
```

With description (helps AI route notes to the correct box):
```bash
dino box add "Inbox" --description "用于存放待整理的想法和资料" --json
```

With color:
```bash
dino box add "Inbox" --color "#FF5733" --json
```

## Workflow

1. If no arguments → list all card boxes
2. If argument provided → create the box
3. Ask the user if they want to add a description (recommended — helps AI route notes)
4. After creating, confirm success and show the box ID
5. If the user wants to add notes to a box, suggest using `/create-note` with the `--zettel_boxes` option
