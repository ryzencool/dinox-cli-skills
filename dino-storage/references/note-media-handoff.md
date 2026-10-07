# Note Media Handoff

Read this reference when a storage upload is intended for Dinox note content.
The storage skill must remain usable without the separate note skill.

## Preserve Returned Fields

After a successful upload, keep these exact `data` fields in working context:

- `resourceId`
- `storageKey`
- `storageUrl`
- `thumbnailUrl` when present
- `category`

Never invent or reconstruct resource identifiers from a URL.

## Rewrite Local References

Do not persist local filesystem paths in note Markdown. Replace each local
reference with the returned remote URL before creating or patching a note:

- Image: use standard Markdown image syntax with `storageUrl`; retain
  `resourceId` in the Dinox metadata comment when the note workflow supports it.
- Audio or video: use a remote `src`; preserve `resourceId` and `storageKey` in
  the media metadata attributes used by Dinox.
- File: use a normal Markdown link to `storageUrl`; retain `resourceId` in the
  Dinox metadata comment when supported.
- Image preview: use `thumbnailUrl` only for preview or poster metadata unless
  the user explicitly wants the thumbnail as the primary image.

## Handoff Rule

Return the remote Markdown fragment plus the preserved resource fields to the
active note workflow. Re-scan the final Markdown and stop if any local path
remains or any required upload failed.
