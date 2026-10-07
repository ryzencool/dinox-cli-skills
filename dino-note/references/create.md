<!-- BEGIN GENERATED_COMMANDS -->
## Command Surface

Use this generated command surface as the canonical interface for note creation.

```text
dino note create                   # Create a new note from markdown content
  --title <string>               # Note title
  --content <string|@file>       # Markdown content
  --type <note|crawl>            # Note type: note or crawl
  --tags <string|@file>          # Tag list (JSON array or comma/newline-separated)
  --boxes <string|@file>         # Box paths or unique names (JSON array or comma/newline-separated)
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Preview the write without executing it
```

- Use `--boxes` for public box input.
- Prefer `--type note` unless the user explicitly wants a `crawl` note.
- Run the same command with `--sync-timeout 20000 --dry-run --format json` first and keep the host timeout 5-10 seconds higher.
<!-- END GENERATED_COMMANDS -->

## Required Inputs

- Title
- Markdown content

Markdown fenced code blocks with `mermaid` or `mindgraph` languages are preserved as structured atomic note nodes.

## Optional Inputs

- Note type
- Tags
- Boxes
- Media/file resources referenced from local paths

## Validation Flow

1. If tags are specified, verify with `dino tag list --sync-timeout 20000 --format json`.
2. If boxes are specified, verify with `dino box list --sync-timeout 20000 --format json`.
3. If markdown contains local image/audio/video/file references, first read [media-resources](media-resources.md) and rewrite them into uploaded remote forms.
4. If required inputs are missing, ask for them before constructing the command.
5. Write free-form content to a unique file in the host operating system's temporary directory and reference it with `@filepath`; never interpolate it into shell source.
6. Do not auto-create missing tags or boxes without explicit permission. Offer to create them separately first.

## Write Flow

1. Finalize the rewritten markdown so no local resource paths remain.
2. Build the final `dino note create ... --sync-timeout 20000 --format json --dry-run` command and keep the host timeout 5-10 seconds higher.
3. Inspect `data.inputs`, `data.changes`, `data.stale`, and `data.sync.gate`. If the preview exactly matches the note the user explicitly asked to create, execute without asking again; otherwise clarify the title, content, tags, boxes, or type.
4. Rerun the same command without `--dry-run`.
5. Require top-level `ok: true`, then report the new note ID plus `data.durability`, `data.upload_queue_remaining`, `data.version`, `data.content_hash`, `data.changed`, `data.stale`, and top-level `_notice`.
6. Use `--durability uploaded` only when the user explicitly needs upload completion before success.
