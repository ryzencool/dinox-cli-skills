# Media Resources

Use this reference whenever note creation or note updates involve **local**
image, audio, video, or file paths.

## Hard Rule

Do **not** send local file paths directly into `dino note create` or
`dino note update`.

Examples that must be rewritten first:

- `![cover](./cover.jpg)`
- `[report](file:///Users/me/report.pdf)`
- `<audio src="/Users/me/demo.m4a"></audio>`
- `<video src="./demo.mp4"></video>`

Local paths are not valid long-term note content. Upload them first, then
rewrite the markdown to use remote storage URLs.

## Required Flow

1. Detect every local media/file reference in the draft markdown.
2. Upload each local file first with `dino storage upload ... --format json`.
3. Record the returned:
   - `resourceId`
   - `storageKey`
   - `storageUrl`
   - `thumbnailUrl` for images when available
4. Rewrite the markdown into a parser-friendly remote form.
5. Only then run `dino note create` or `dino note update`.

## Upload Commands

Choose category by media type:

```bash
dino storage upload ./cover.jpg --category images --format json
dino storage upload ./demo.m4a --category audios --format json
dino storage upload ./demo.mp4 --category videos --format json
dino storage upload ./report.pdf --category files --format json
```

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

## Create / Update Workflow Integration

For note create/update:

1. Normalize the user's draft markdown.
2. Replace all local media references with uploaded remote forms.
3. Re-check the final markdown for any remaining local paths.
4. Use the rewritten markdown as `--content`.

## Safety

- Never leave raw local filesystem paths inside persisted markdown.
- Never invent `resourceId` or `storageKey`; only use values returned by `dino storage upload`.
- If upload fails for any referenced local media, stop and resolve that failure before creating or updating the note.
