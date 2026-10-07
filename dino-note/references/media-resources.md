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

Prefer the following forms because they are compatible with the current
markdown-to-json pipeline.

### Image

Use standard markdown image syntax plus Dinox metadata comment:

```md
![Cover](https://bucket.example.com/assets/user/images/cover.jpg)<!-- {"kind":"image","resourceId":"<resourceId>","widthPercent":66} -->
```

Notes:

- Use the uploaded `storageUrl` as the image `src`.
- `thumbnailUrl` is for preview/metadata; do not replace the main image with the
  thumbnail unless the user explicitly wants a thumbnail-sized image.
- The current image metadata comment preserves `resourceId`. Keep `storageKey`
  in the working context/output even though it is not embedded in this image form.

### Audio

Prefer HTML audio blocks so both `resourceId` and `storageKey` survive parsing:

```md
<audio
  src="https://bucket.example.com/assets/user/audios/demo.m4a"
  preload="metadata"
  data-resource-id="<resourceId>"
  data-storage-key="<storageKey>"
></audio>
```

### Video

Prefer HTML video blocks so both `resourceId` and `storageKey` survive parsing:

```md
<video
  src="https://bucket.example.com/assets/user/videos/demo.mp4"
  poster="https://bucket.example.com/assets/user/videos/thumb/demo_thumbnail.webp"
  data-resource-id="<resourceId>"
  data-storage-key="<storageKey>"
  controls
></video>
```

If an uploaded image thumbnail exists, it is reasonable to use `thumbnailUrl` as
the `poster`.

### File

Use a normal markdown link plus Dinox metadata comment:

```md
[Quarterly Report.pdf](https://bucket.example.com/assets/user/files/report.pdf)<!-- {"kind":"file","resourceId":"<resourceId>"} -->
```

Notes:

- Use the uploaded `storageUrl` as the link target.
- Keep `storageKey` in the working context/output even though the current file
  metadata comment only embeds `resourceId`.

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
