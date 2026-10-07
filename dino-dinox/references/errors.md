# Dinox CLI Error Codes

Generated from the CLI error code registry. Prefer the live catalog from
`dino schema errors --format json`; read this file only when `dino` is
unavailable. Branch on the structured `code` and `recoverable` fields and follow
`suggested_action` when present.

<!-- BEGIN GENERATED_ERRORS -->
## Exit Codes

| Exit | Meaning |
|------|---------|
| 1 | Command failed |
| 2 | Usage or invalid input |
| 3 | Authentication |
| 4 | Sync freshness or pending upload |
| 5 | Precondition failed or resource not found |

## Error Codes

| Code | Exit | Meaning |
|------|------|---------|
| `ALREADY_EXISTS` | 5 | The target resource already exists. |
| `AUTH_INVALID` | 3 | The configured credentials were rejected. |
| `AUTH_REQUIRED` | 3 | The command needs an authenticated Dinox account. |
| `AUTH_STATE_CHANGED` | 3 | Authentication changed while the command was running. |
| `CACHE_CLEANUP_FAILED` | 1 | Local cache cleanup after logout did not complete. |
| `COMMAND_FAILED` | 1 | Generic command failure without a more specific code. |
| `CONFIG_INVALID_JSON` | 1 | The CLI config file is not valid JSON. |
| `CONFIG_WRITE_BUSY` | 1 | Another process is writing the CLI config. |
| `DAEMON_ERROR` | 5 | The background daemon reported an error. |
| `DAEMON_INSTANCE_CHANGED` | 1 | The daemon instance changed between requests. |
| `DAEMON_INSTANCE_MISMATCH` | 1 | The running daemon belongs to a different CLI version or account. |
| `DAEMON_INSTANCE_UNKNOWN` | 1 | The daemon identity could not be verified. |
| `DAEMON_LEGACY_UNVERIFIED` | 1 | A daemon from an older CLI could not be verified safely. |
| `DAEMON_RELEASE_FAILED` | 1 | The daemon could not release database ownership. |
| `DAEMON_RELEASE_TIMEOUT` | 1 | The daemon did not release database ownership in time. |
| `DAEMON_STALE` | 5 | The daemon state is stale and needs a restart. |
| `DAEMON_START_FAILED` | 5 | The daemon process failed to start. |
| `DAEMON_START_TIMEOUT` | 1 | The daemon did not become ready in time. |
| `DAEMON_STOP_FAILED` | 1 | The daemon could not be stopped. |
| `DAEMON_UNAVAILABLE` | 1 | The daemon is not available for this request. |
| `DAEMON_UNREACHABLE` | 1 | The daemon socket could not be reached. |
| `INTERNAL_ERROR` | 1 | Unexpected internal error; not a DinoxError. |
| `INVALID_ARGUMENT` | 2 | An argument or option value is invalid. |
| `INVALID_STORAGE_KEY` | 2 | The storage object key is invalid. |
| `NOTE_INVALID_PROPERTIES` | 1 | Note properties are invalid. |
| `NOTE_NOT_FOUND` | 5 | The note does not exist or is deleted. |
| `NOTE_TEMPLATE_INVALID_CONTENT` | 1 | The note template content is invalid. |
| `NOTE_TEMPLATE_INVALID_PROPERTY_DEF` | 1 | A note template property definition is invalid. |
| `NOTE_TEMPLATE_INVALID_TOKEN_CONTEXT` | 1 | A note template token is used in an invalid context. |
| `NOTE_TEMPLATE_UNSUPPORTED_PROPERTY_DEF_VERSION` | 1 | The note template property definition version is unsupported. |
| `NOT_FOUND` | 5 | The requested resource was not found. |
| `POWERSYNC_OWNER_BUSY` | 1 | Another process owns the local PowerSync database. |
| `PRECONDITION_FAILED` | 5 | A safety precondition for the operation did not hold. |
| `SAVED_VIEW_FILTER_TOO_COMPLEX` | 1 | The saved view filter exceeds supported complexity. |
| `SAVED_VIEW_FILTER_VALUE_REQUIRED` | 5 | A saved view filter condition is missing its value. |
| `SAVED_VIEW_INVALID_CONFIG` | 1 | The saved view config is invalid. |
| `SAVED_VIEW_INVALID_DATE_VALUE` | 1 | A saved view date value is not a real calendar date. |
| `SAVED_VIEW_INVALID_FIELD` | 1 | A saved view references an invalid field. |
| `SAVED_VIEW_INVALID_FILTER` | 1 | The saved view filter is invalid. |
| `SAVED_VIEW_INVALID_ROW` | 1 | A stored saved view row is invalid. |
| `SAVED_VIEW_NOT_FOUND` | 5 | The saved view does not exist. |
| `SAVED_VIEW_OPERATOR_TYPE_MISMATCH` | 1 | A filter operator is not valid for the field type. |
| `SAVED_VIEW_OPTION_NOT_FOUND` | 5 | A select option id referenced by the view does not exist. |
| `SAVED_VIEW_PROPERTY_CONFIG_INVALID` | 1 | A property configuration used by the view is invalid. |
| `SAVED_VIEW_PROPERTY_INVALID` | 1 | A property used by the view is invalid. |
| `SAVED_VIEW_PROPERTY_NOT_FOUND` | 5 | A property referenced by the view does not exist. |
| `SAVED_VIEW_PROPERTY_OPTIONS_INVALID` | 1 | Property options used by the view are invalid. |
| `SAVED_VIEW_PROPERTY_OPTIONS_MIGRATION_REQUIRED` | 5 | Property options need migration before use. |
| `SAVED_VIEW_SORT_TYPE_UNSUPPORTED` | 1 | The field type cannot be sorted. |
| `SAVED_VIEW_UNSUPPORTED_CONFIG_VERSION` | 1 | The saved view config version is unsupported. |
| `SAVED_VIEW_UNSUPPORTED_FILTER_VERSION` | 1 | The saved view filter version is unsupported. |
| `SAVED_VIEW_UNSUPPORTED_LAYOUT` | 1 | The saved view layout is unsupported. |
| `SAVED_VIEW_VALUE_TYPE_MISMATCH` | 1 | A filter value does not match the field type. |
| `SCHEMA_PATH_NOT_FOUND` | 5 | No command schema exists for the requested path. |
| `SKILLS_LOCK_INVALID` | 1 | The skills installer lock file is malformed. |
| `SKILLS_LOCK_READ_FAILED` | 1 | The skills installer lock file could not be read. |
| `SKILLS_MIGRATION_FAILED` | 1 | The skills installer failed during migration. |
| `SKILLS_MIGRATION_PRECONDITION_FAILED` | 1 | The skills lock changed before migration ran. |
| `SKILLS_MIGRATION_VERIFICATION_FAILED` | 1 | Post-migration verification did not match the plan. |
| `STORAGE_IMAGE_PROCESSING_UNAVAILABLE` | 1 | The optional sharp image library is unavailable, so image uploads are refused. |
| `STORAGE_OBJECT_ALREADY_EXISTS` | 5 | The storage object already exists; overwrite was not confirmed. |
| `STORAGE_RESOURCE_INSERT_FAILED` | 1 | The uploaded object could not be recorded as a resource. |
| `STORAGE_TEST_CLEANUP_FAILED` | 1 | The storage connectivity test object could not be removed. |
| `STORAGE_THUMBNAIL_UPLOAD_FAILED` | 1 | The thumbnail upload failed. |
| `STORAGE_UPLOAD_OUTCOME_UNKNOWN` | 1 | The upload outcome is unknown; verify before retrying. |
| `SYNC_REQUIRED` | 4 | Fresh sync was required (--require-sync) but not confirmed. |
| `SYNC_STALE` | 4 | The local cache is stale. |
| `SYNC_TIMEOUT` | 4 | Sync did not complete within the timeout. |
| `TODO_TASK_LOOKUP_TRUNCATED` | 1 | Task lookup hit its scan limit; pass --note-id. |
| `TODO_TASK_NOT_FOUND` | 5 | The todo task does not exist. |
| `UNAUTHENTICATED` | 3 | The backend rejected the request as unauthenticated. |
| `UNSUPPORTED_CRUD_OPERATION` | 1 | A queued upload contains an unsupported operation. |
| `UPDATE_FAILED` | 1 | The CLI self-update failed. |
| `UPLOAD_PENDING` | 4 | Local writes are still waiting to upload. |
| `USAGE_ERROR` | 2 | Invalid command usage, arguments, or flags. |
<!-- END GENERATED_ERRORS -->
