---
name: dino-create-note
description: >
  Create a new Dinox note. Use when the user wants to save, write, record,
  or create a note in their knowledge base.
argument-hint: "[title]"
allowed-tools:
  - Bash
  - Write
---

# Create a Dinox Note

The user wants to create a new note in their Dinox knowledge base.

## Instructions

1. Gather the required information from the user or from `$ARGUMENTS`:
   - **Title** (required)
   - **Content** in Markdown (required)
   - **Tags** (optional) — must be existing tags
   - **Card boxes / Zettel boxes** (optional) — must be existing boxes
2. If the user hasn't specified tags or boxes, you can list available ones to suggest:
   - `dino tag list --json` to show available tags
   - `dino box list --json` to show available card boxes
3. For long content, write to a temporary file and use `@filepath` syntax
4. Run the create command and confirm success

## Create Command

For short content (inline):
```bash
dino note create --title "Note Title" --content "Markdown content here" --json
```

For long content (via temp file):
```bash
# Write content to temp file first, then:
dino note create --title "Note Title" --content @/tmp/note-content.md --json
```

With tags and boxes:
```bash
dino note create \
  --title "Note Title" \
  --content "Content" \
  --tags "tag1,tag2" \
  --zettel_boxes "box1,box2" \
  --json
```

## Important Rules

- **Tags must exist first**. If the user specifies a tag that doesn't exist, create it first with `dino tag add <name>` before creating the note.
- **Card boxes must exist first**. If needed, create with `dino box add <name>`.
- Content is Markdown format — use proper Markdown syntax.
- The `--type` option defaults to `crawl`. Use `--type note` for regular notes.
- After successful creation, the command outputs the new note's ID.

## Workflow

1. If the user provides all info → create directly
2. If missing title or content → ask the user
3. If tags are specified → verify they exist with `dino tag list --json`, create missing ones
4. If boxes are specified → verify they exist with `dino box list --json`, create missing ones
5. Create the note and report the new note ID
