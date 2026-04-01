---
name: dino-storage
description: >
  List custom Dinox storage configs, test a custom S3 target, upload a local
  file to your own S3, and inspect usage stats. Use when the user wants to
  inspect configured object storage targets, verify a custom S3 config, push a
  file into a custom S3 bucket, or see storage usage totals.
argument-hint: "[list|test|upload <file>|stats]"
allowed-tools:
  - Bash
  - Write
metadata:
  requires:
    bins: ["dino"]
  cliHelp: "dino storage --help"
  category: "storage"
  risk: "mixed"
---

# Manage Dinox Storage

**CRITICAL — Before doing anything else, first read [`../dino-shared/SKILL.md`](../dino-shared/SKILL.md). It contains shared auth, output, and write-safety rules.**

Use this skill when the user wants to inspect custom storage configs, verify S3 connectivity, upload a file, or inspect storage usage.

## Safety & Boundaries (Must Follow)

- Treat storage credentials and endpoint details as sensitive data. Never echo `secret_access_key` back to the user.
- `dino storage test` writes a tiny temporary object to S3. `dino storage upload` writes to both S3 and local Dinox metadata. Always show the exact command and get explicit confirmation before running the real write.
- Prefer `dino storage list --format json` before uploading if the target storage config is ambiguous.
- Do not ask the user to paste auth tokens into chat. If auth is required, instruct them to run `dino auth login "<token>"` in their own terminal.
- If the user wants to upload to a specific config without changing the active storage in the app, prefer `--storage-id`.

<!-- BEGIN GENERATED_COMMANDS -->
## Command Reference

Use these commands as the canonical Dinox CLI interface for custom storage inspection, connectivity testing, uploads, and usage stats.

```text
dino storage list                  # List custom storage configs from c_storage and mark the active one

dino storage test                  # Upload a tiny temporary object to a custom S3 storage target without persisting a c_resource row
  --storage-id <id>              # Explicit storage config id (otherwise use active custom config)
  --dry-run                      # Preview the test object target without uploading

dino storage upload <file>         # Upload one local file to a custom S3 storage target and persist a c_resource row
  --storage-id <id>              # Explicit storage config id (otherwise use active custom config)
  --category <kind>              # Upload category: images|audios|files|videos
  --key <string>                 # Explicit object key override
  --dry-run                      # Preview the upload target and resource record without uploading

dino storage stats                 # Summarize uploaded storage usage grouped by provider and bucket
```

- Prefer `--storage-id` when you want to bypass the current active custom storage selection.
- For test and upload writes, run the same command with `--dry-run` first.
<!-- END GENERATED_COMMANDS -->

## Workflow

1. For browse requests, run `dino storage list --format json`.
2. For connectivity checks, use `dino storage test --format json` and prefer `--storage-id` when multiple custom configs exist.
3. For uploads, first identify the target config:
   - explicit `--storage-id`
   - otherwise the active custom storage config
   - otherwise the only available custom storage config when exactly one exists
4. Run `dino storage upload <file> ... --format json --dry-run` and show the planned bucket, key, and url.
5. After confirmation, rerun without `--dry-run`.
6. Summarize `resourceId`, `storageKey`, `storageUrl`, `thumbnailUrl`, `bucket`, and `provider`.
7. For usage questions, run `dino storage stats --format json`.
8. When the upload is for note markdown embedding, pass the returned `resourceId`, `storageKey`, `storageUrl`, and `thumbnailUrl` into the note-media rewrite flow described in [`../dino-note/references/media-resources.md`](../dino-note/references/media-resources.md).

## Important Notes

- `dino storage upload` only targets custom S3 configs. If no custom storage is configured, the user must create one in the app first.
- `dino storage test` also targets only custom S3 configs and does not create a `c_resource` row.
- Private buckets are allowed; `storageUrl` is still recorded, but direct read access may require signed URLs elsewhere.
- Upload also writes a `c_resource` row, so the file is visible to later Dinox workflows.
- When the uploaded file is an image, the CLI also generates a `400px` wide `webp` thumbnail and stores its URL in the resource `checksum` field.

## Error Handling

- If no custom storage config is available, tell the user to configure a custom storage target in the app first.
- If there is no active custom storage config and multiple configs exist, ask the user to pick one and use `--storage-id`.
- If S3 upload fails due to endpoint, bucket, region, or credentials, tell the user the custom storage config is incomplete or invalid and they should verify it in the app.
- If `stats` returns no entries, tell the user there are no uploaded storage resources recorded yet.
