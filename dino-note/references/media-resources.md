# Media Resources

Use this reference whenever note creation or note updates involve **local**
image, audio, video, or file paths.

## Contents

- [Hard Rule](#hard-rule)
- [Required Flow](#required-flow)
- [Upload Commands](#upload-commands)
- [Parser-Friendly Markdown Forms](#parser-friendly-markdown-forms)
- [Create Or Patch Integration](#create-or-patch-integration)
- [Safety](#safety)

## Hard Rule

Do **not** send local file paths directly into `dino note create` or a
`dino note patch` content payload.

Examples that must be rewritten first:

- `![cover](./cover.jpg)`
- `[report](file:///Users/me/report.pdf)`
- `<audio src="/Users/me/demo.m4a"></audio>`
- `<video src="./demo.mp4"></video>`

Local paths are not valid long-term note content. Upload them first, then
rewrite the markdown to use remote storage URLs.

## Required Flow

1. Detect every local media/file reference in the draft markdown.
2. Run `dino storage upload ... --sync-timeout 20000 --format json --dry-run`
   for each file and inspect the planned storage config, bucket, main object key,
   URL, requested overwrite mode, freshness, and sync gate. This preview does
   not query S3 object existence, validate image decoding or thumbnail
   generation, or enumerate the derived image-thumbnail key; do not describe it
   as remote-state verification.
3. If the preview matches the user's authorized note operation, run the same
   upload without `--dry-run`. Ask again when the target is ambiguous or when
   `--overwrite` could replace the explicit main key or a derived image
   thumbnail. A host timeout leaves the remote outcome unknown; verify the
   reported keys before retrying.
4. Record the returned:
   - `resourceId`
   - `storageKey`
   - `storageUrl`
   - `thumbnailUrl` for images when available
5. Rewrite the markdown into a parser-friendly remote form.
6. Only then run `dino note create` or the structured `dino note patch` flow.

## Upload Commands

Choose category by media type:

```bash
dino storage upload ./cover.jpg --category images --sync-timeout 20000 --format json --dry-run
dino storage upload ./demo.m4a --category audios --sync-timeout 20000 --format json --dry-run
dino storage upload ./demo.mp4 --category videos --sync-timeout 20000 --format json --dry-run
dino storage upload ./report.pdf --category files --sync-timeout 20000 --format json --dry-run
```

After reviewing a preview, repeat only the approved upload without `--dry-run`.

## Parser-Friendly Markdown Forms

Write media the way every Dinox app stores it in `content_md`: a media block is
one line of HTML on its own (blank line before and after), carrying the
resource attributes as `data-*`. Older forms (a link plus a `<!-- {...} -->`
comment) still read, but do not write them.

### Image

```md
<img src="https://bucket.example.com/assets/user/images/cover.jpg" alt="Cover" data-type="image" data-resource-id="RESOURCE_ID" data-storage-key="STORAGE_KEY" data-width-percent="66">
```

Notes:

- Use the uploaded `storageUrl` as the image `src`.
- `thumbnailUrl` is for preview/metadata; do not replace the main image with the
  thumbnail unless the user explicitly wants a thumbnail-sized image.
- An image with no resource attributes is plain Markdown: `![Cover](https://…)`.

### Audio

```md
<audio controls src="https://bucket.example.com/assets/user/audios/demo.m4a" title="Demo" data-type="audio" data-resource-id="RESOURCE_ID" data-storage-key="STORAGE_KEY"></audio>
```

### Video

```md
<video src="https://bucket.example.com/assets/user/videos/demo.mp4" title="Demo" poster="https://bucket.example.com/assets/user/videos/thumb/demo_thumbnail.webp" data-resource-id="RESOURCE_ID" data-storage-key="STORAGE_KEY"></video>
```

If an uploaded image thumbnail exists, it is reasonable to use `thumbnailUrl` as
the `poster`.

### File

```md
<a href="https://bucket.example.com/assets/user/files/report.pdf" type="application/pdf" data-type="file" data-resource-id="RESOURCE_ID" data-storage-key="STORAGE_KEY">Quarterly Report.pdf</a>
```

Notes:

- Use the uploaded `storageUrl` as the link target; the link text is the file name.

## Create Or Patch Integration

For note create or content patch:

1. Normalize the user's draft markdown.
2. Replace all local media references with uploaded remote forms.
3. Re-check the final markdown for any remaining local paths.
4. Use the rewritten markdown as `--content`. For an existing note, follow
   [content-edit](content-edit.md) and obtain a fresh read token before the real
   patch.

## Safety

- Never leave raw local filesystem paths inside persisted markdown.
- Never invent `resourceId` or `storageKey`; only use values returned by `dino storage upload`.
- Treat dry-run collision status and image-thumbnail validity as unknown unless a future CLI response explicitly reports them.
- If upload fails for any referenced local media, stop and resolve that failure before creating or updating the note.
