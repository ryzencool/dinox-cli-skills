<!-- BEGIN GENERATED_COMMANDS -->
## Command Surface

Use this generated command surface as the canonical interface for note creation.

```text
dino note create                   # Create a new note from markdown content
  --title <string>               # Note title
  --content <string|@file>       # Markdown content
  --type <note|crawl>            # Note type: note or crawl
  --tags <string|@file>          # Tag list (JSON array or comma/newline-separated)
  --boxes <string|@file>         # Box names (JSON array or comma/newline-separated)
  --dry-run                      # Preview the write without executing it
```

- Use `--boxes` for public box input.
- Prefer `--type note` unless the user explicitly wants a `crawl` note.
- Run the same command with `--dry-run --format json` first.
<!-- END GENERATED_COMMANDS -->

## Required Inputs

- Title
- Markdown content

## Optional Inputs

- Note type
- Tags
- Boxes
- Media/file resources referenced from local paths

## Validation Flow

1. If tags are specified, verify with `dino tag list --format json`.
2. If boxes are specified, verify with `dino box list --format json`.
3. If markdown contains local image/audio/video/file references, first read [media-resources](media-resources.md) and rewrite them into uploaded remote forms.
4. If required inputs are missing, ask for them before constructing the command.
5. If content is long, write it to `/tmp/` and reference it with `@filepath`.
6. Do not auto-create missing tags or boxes without explicit permission. Offer to create them separately first.

## Write Flow

1. Finalize the rewritten markdown so no local resource paths remain.
2. Build the final `dino note create ... --format json --dry-run` command.
3. Show the preview and ask for confirmation.
4. Rerun the same command without `--dry-run`.
5. Report the new note ID and any stale warning.
