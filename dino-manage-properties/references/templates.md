# Note Templates

A template stores content (a Tiptap document) and an ordered list of property keys. Creating a note
from it hydrates `{{date}}`, `{{time}}`, `{{datetime}}`, and `{{location}}` tokens and declares each key
as an empty slot on the new note; templates hold no default values.

<!-- BEGIN GENERATED_COMMANDS -->
## Template Command Reference

Note templates hold content plus the ordered property keys a new note declares; create notes from them with `dino note create --template <id>`.

```text
dino template list                 # List note templates
  --limit <n>                    # Maximum returned templates (1-2000)

dino template get <id>             # Get one note template with its Tiptap document

dino template create               # Create a note template
  --name <string>                # Template name
  --content <string|@file>       # Template content as Markdown; {{date}} {{time}} {{datetime}} {{location}} tokens are filled when a note is created
  --content-json <json|@file>    # Template content as a Tiptap doc JSON
  --property-key <key>           # Property key declared on notes created from the template (repeatable, ordered)
  --property-keys <string|@file> # Property keys as a JSON array or comma/newline-separated list
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Run the write in a rolled-back transaction and preview the result

dino template update <id>          # Update a note template; fields you do not pass are left as stored
  --name <string>                # Rename the template
  --content <string|@file>       # Template content as Markdown; {{date}} {{time}} {{datetime}} {{location}} tokens are filled when a note is created
  --content-json <json|@file>    # Template content as a Tiptap doc JSON
  --clear-content                # Make the template property-only
  --property-key <key>           # Property key declared on notes created from the template (repeatable, ordered)
  --property-keys <string|@file> # Property keys as a JSON array or comma/newline-separated list
  --clear-property-keys          # Remove all property keys
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Run the write in a rolled-back transaction and preview the result

dino template delete <id>          # Soft-delete a note template
  --durability <local|uploaded>  # Required write durability before success: local saves to the local DB; uploaded waits for the PowerSync upload queue to drain
  --dry-run                      # Run the write in a rolled-back transaction and preview the result
```

- Run template writes with `--dry-run` first.
- When executing an online command from this surface, add `--sync-timeout 20000` and keep the host timeout 5-10 seconds higher.
<!-- END GENERATED_COMMANDS -->

## Workflow

1. List templates with `dino template list --sync-timeout 20000 --format json` (`data.templates[].propertyKeys`, `hasContent`).
2. Inspect one with `dino template get <id> --sync-timeout 20000 --format json`; `data.template.document` is the Tiptap doc (legacy Markdown content is converted).
3. Create or edit with `--content @file.md` (Markdown) or `--content-json @doc.json`, and `--property-key <key>` (repeatable, ordered) or `--property-keys "a,b"`. Run `--dry-run` first.
4. Make sure every template key has a definition (`dino prop list`); create missing ones with `dino prop create` so values are typed.
5. Create a note from it with `dino note create --title <title> --template <id> --type note --dry-run --format json`. Add `--properties @values.json` to fill values in the same transaction; a bad value aborts the whole create. Explicit `--content` overrides the template content.
