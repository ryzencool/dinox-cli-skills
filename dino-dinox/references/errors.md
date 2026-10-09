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
| `INVALID_PROPERTY_DEF_CONFIG` | 1 | A property definition config could not be serialized. |
| `INVALID_PROPERTY_DEF_ID` | 1 | A property definition id is missing or blank. |
| `INVALID_PROPERTY_OPTIONS` | 1 | Property options could not be serialized. |
| `INVALID_STORAGE_KEY` | 2 | The storage object key is invalid. |
| `NOTE_INVALID_PROPERTIES` | 1 | Note properties are invalid. |
| `NOTE_NOT_FOUND` | 5 | The note does not exist or is deleted. |
| `NOTE_PROPERTIES_CORRUPT` | 1 | The note properties JSON is corrupt and cannot be mutated safely. |
| `NOTE_PROPERTIES_INVALID` | 1 | The note properties mutation is invalid. |
| `NOTE_PROPERTIES_MUTATION_FAILED` | 1 | The note properties mutation could not be applied. |
| `NOTE_PROPERTY_ORDER_STALE` | 5 | The property slot order no longer matches the note; re-read and retry. |
| `NOTE_TEMPLATE_INVALID_CONTENT` | 1 | The note template content is invalid. |
| `NOTE_TEMPLATE_INVALID_PROPERTY_DEF` | 1 | A note template property definition is invalid. |
| `NOTE_TEMPLATE_INVALID_TOKEN_CONTEXT` | 1 | A note template token is used in an invalid context. |
| `NOTE_TEMPLATE_NOT_FOUND` | 5 | The note template does not exist or is deleted. |
| `NOTE_TEMPLATE_UNSUPPORTED_PROPERTY_DEF_VERSION` | 1 | The note template property definition version is unsupported. |
| `NOT_FOUND` | 5 | The requested resource was not found. |
| `POWERSYNC_OWNER_BUSY` | 1 | Another process owns the local PowerSync database. |
| `PRECONDITION_FAILED` | 5 | A safety precondition for the operation did not hold. |
| `PROPERTY_DATE_INVALID` | 1 | A date property value is not a valid date. |
| `PROPERTY_DEF_CONFIG_INVALID` | 1 | A property definition config is invalid. |
| `PROPERTY_DEF_CONFIG_TYPE_MISMATCH` | 1 | A property definition config does not match the property type. |
| `PROPERTY_DEF_ENSURE_FAILED` | 1 | The property definition could not be created or reused. |
| `PROPERTY_DEF_ENSURE_INPUT_INVALID` | 1 | The property definition create input is invalid. |
| `PROPERTY_DEF_NOT_FOUND` | 5 | The property definition does not exist or is deleted. |
| `PROPERTY_DEF_TYPE_CONFLICT` | 5 | A property with this key already exists with a different type. |
| `PROPERTY_DEF_TYPE_INVALID` | 1 | The stored property definition type is invalid. |
| `PROPERTY_FILES_INVALID` | 1 | A files property value is invalid. |
| `PROPERTY_LEGACY_OPTION_LABEL_INVALID` | 1 | A legacy option label cannot be migrated. |
| `PROPERTY_LEGACY_OPTION_NOT_PLANNED` | 1 | A legacy option value was not covered by the migration plan. |
| `PROPERTY_LEGACY_VALUE_INVALID` | 1 | A legacy option value cannot be migrated. |
| `PROPERTY_MULTI_SELECT_EMPTY` | 1 | A multi-select property value is empty. |
| `PROPERTY_MULTI_SELECT_LIMIT_EXCEEDED` | 1 | A multi-select property value has too many selections. |
| `PROPERTY_NUMBER_INVALID` | 1 | A number property value is invalid or out of range. |
| `PROPERTY_OPTIONS_INVALID` | 1 | Stored property options are invalid. |
| `PROPERTY_OPTIONS_MIGRATION_REQUIRED` | 5 | Legacy property options must be migrated first (dino prop migrate-options). |
| `PROPERTY_OPTIONS_TYPE_MISMATCH` | 1 | Options were supplied for a property type that has none. |
| `PROPERTY_OPTION_ARCHIVED` | 5 | The select option is archived and cannot be assigned. |
| `PROPERTY_OPTION_ID_DUPLICATE` | 1 | Property options contain duplicate ids. |
| `PROPERTY_OPTION_ID_UNKNOWN` | 1 | A property option id does not exist on the definition. |
| `PROPERTY_OPTION_LABEL_INVALID` | 1 | A property option label is blank, too long, or duplicated. |
| `PROPERTY_OPTION_LIMIT_EXCEEDED` | 1 | The property has too many options. |
| `PROPERTY_OPTION_NEW_ID_FORBIDDEN` | 1 | New property options must not carry an id. |
| `PROPERTY_OPTION_NOT_FOUND` | 5 | The select option does not exist on a closed property. |
| `PROPERTY_OPTION_REMOVAL_REQUIRES_ARCHIVE` | 1 | Existing property options must be archived instead of removed. |
| `PROPERTY_OPTION_REPAIR_ID_FORBIDDEN` | 1 | Repair options must not carry ids. |
| `PROPERTY_OPTION_SORT_RANK_EXHAUSTED` | 1 | No sort rank is left for a new property option. |
| `PROPERTY_OPTION_VALUE_INVALID` | 1 | A select property value is invalid. |
| `PROPERTY_PLACE_INVALID` | 1 | A place property value is invalid. |
| `PROPERTY_RELATION_EMPTY` | 1 | A relation property value is empty. |
| `PROPERTY_RELATION_INVALID` | 1 | A relation property value references invalid notes. |
| `PROPERTY_RELATION_LIMIT` | 1 | A relation property value has too many notes. |
| `PROPERTY_TYPE_NOT_ACTIVE` | 1 | The property type is not active. |
| `PROPERTY_UNIQUE_ID_CONFIG_INVALID` | 1 | A unique-id property counter is invalid. |
| `PROPERTY_UNIQUE_ID_INVALID` | 1 | A unique-id property value is invalid. |
| `PROPERTY_UNIQUE_ID_TYPE_MISMATCH` | 1 | A unique-id setting was supplied for a different property type. |
| `PROPERTY_URL_INVALID` | 1 | A url property value is invalid. |
| `PROPERTY_VALUE_INVALID` | 1 | A property value does not match its definition. |
| `SAVED_VIEW_FILTER_CORRUPT` | 1 | A stored saved view filter is corrupt. |
| `SAVED_VIEW_FILTER_TOO_COMPLEX` | 1 | The saved view filter exceeds supported complexity. |
| `SAVED_VIEW_FILTER_VALUE_REQUIRED` | 5 | A saved view filter condition is missing its value. |
| `SAVED_VIEW_INVALID_CONFIG` | 1 | The saved view config is invalid. |
| `SAVED_VIEW_INVALID_CURRENT_TIME` | 1 | The reference time for dynamic date filters is invalid. |
| `SAVED_VIEW_INVALID_DATE_VALUE` | 1 | A saved view date value is not a real calendar date. |
| `SAVED_VIEW_INVALID_FIELD` | 1 | A saved view references an invalid field. |
| `SAVED_VIEW_INVALID_FILTER` | 1 | The saved view filter is invalid. |
| `SAVED_VIEW_INVALID_OPERATOR` | 1 | A saved view filter operator is invalid. |
| `SAVED_VIEW_INVALID_RELATIVE_DAYS` | 1 | withinLastDays requires an integer day count in range. |
| `SAVED_VIEW_INVALID_ROW` | 1 | A stored saved view row is invalid. |
| `SAVED_VIEW_INVALID_TIME_ZONE` | 2 | The time zone is not a valid IANA time zone. |
| `SAVED_VIEW_NOT_FOUND` | 5 | The saved view does not exist. |
| `SAVED_VIEW_OPERATOR_TYPE_MISMATCH` | 1 | A filter operator is not valid for the field type. |
| `SAVED_VIEW_OPTION_NOT_FOUND` | 5 | A select option id referenced by the view does not exist. |
| `SAVED_VIEW_PROPERTY_CONFIG_INVALID` | 1 | A property configuration used by the view is invalid. |
| `SAVED_VIEW_PROPERTY_INVALID` | 1 | A property used by the view is invalid. |
| `SAVED_VIEW_PROPERTY_NOT_FOUND` | 5 | A property referenced by the view does not exist. |
| `SAVED_VIEW_PROPERTY_OPTIONS_INVALID` | 1 | Property options used by the view are invalid. |
| `SAVED_VIEW_PROPERTY_OPTIONS_MIGRATION_REQUIRED` | 5 | Property options need migration before use. |
| `SAVED_VIEW_SORT_TYPE_UNSUPPORTED` | 1 | The field type cannot be sorted. |
| `SAVED_VIEW_TIME_ZONE_CONVERSION_FAILED` | 1 | A dynamic date boundary could not be converted to the time zone. |
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
