# Saved View JSON Contract

Use this reference when constructing or reviewing `--filter` and `--config` payloads.

## Field References

- System fields: `sys.title`, `sys.created_at`, `sys.updated_at`, `sys.tags`, `sys.zettel_boxes`, `sys.source`, `sys.type`, `sys.starred_at`.
- Property fields: `prop.<key>`, where `<key>` comes from `dino view fields --format json`.
- Property keys are stable storage keys. A renamed display name does not change the `prop.<key>` reference.
- Use only field entries with `available: true`. Malformed or migration-pending property definitions remain visible with an `error` so they do not hide healthy fields.

## Filter Envelope

```json
{
  "version": 1,
  "root": {
    "kind": "group",
    "operator": "and",
    "children": [
      {
        "kind": "condition",
        "field": "prop.status",
        "operator": "equals",
        "value": "option-id"
      },
      {
        "kind": "condition",
        "field": "prop.score",
        "operator": "greaterThanOrEqual",
        "value": 10
      }
    ]
  }
}
```

Set `root` to `null` for no filter. A node is one of:

- Condition: `{ "kind": "condition", "field": "...", "operator": "...", "value": ... }`
- Group: `{ "kind": "group", "operator": "and|or", "children": [...] }`
- Negation: `{ "kind": "not", "child": ... }`

Limits: maximum 8 levels, 100 nodes, and 2,000 characters per string value.

## Operators by Semantic Type

| Type | Operators | Value |
|------|-----------|-------|
| text/url | `equals`, `notEquals`, `contains`, `notContains`, `isEmpty`, `isNotEmpty` | string except empty checks |
| select | `equals`, `notEquals`, `isEmpty`, `isNotEmpty` | stable option ID |
| multi-select/relation/list | `contains`, `notContains`, `isEmpty`, `isNotEmpty` | stable option ID or relation ID |
| number | equality, ordered comparisons, empty checks | number |
| date/datetime | equality, ordered comparisons, empty checks | valid calendar date or ISO datetime string |
| checkbox | `equals`, `notEquals`, `isEmpty`, `isNotEmpty` | boolean |

`equals` or `notEquals` with a missing/null value means empty/not-empty. `isEmpty` and `isNotEmpty` do not accept a non-null value.
Date validation matches Electron before JavaScript date normalization, so impossible calendar dates are rejected for ISO strings using either `T` or `t` separators.

## Config Envelope

```json
{
  "version": 1,
  "layout": "table",
  "columns": [
    { "field": "sys.title" },
    { "field": "prop.status" },
    { "field": "prop.score" }
  ],
  "sort": [
    { "field": "prop.status", "direction": "asc" },
    { "field": "prop.score", "direction": "desc" }
  ]
}
```

- Only `table` is supported.
- `sys.title` must be the first column.
- Columns must be unique; maximum 100.
- Sort fields must be unique; maximum 20.
- Do not sort list fields (`multi_select`, `relation`, `sys.tags`, `sys.zettel_boxes`).
- Select sorting follows option `sortRank`, then option ID. Empty values sort last. Every query adds note ID descending as a deterministic tie-breaker.
- The default config is title + updated time, sorted by updated time descending.

## Query Output

- `data.columns` reports semantic type, property type, date mode, and option catalog for configured columns.
- `data.rows[].values` contains stored IDs and scalar/list values.
- `data.rows[].displayValues` resolves select IDs to label/color/archive metadata when applicable.
- `data.totalCount` counts all matches. Continue with `data.nextOffset` until `data.hasMore` is false.
