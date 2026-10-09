# Saved View JSON Contract

Use this reference when constructing or reviewing `--filter` and `--config` payloads. The CLI
validates them with the same schemas as Dinox desktop.

## Field References

- System fields: `sys.title`, `sys.created_at`, `sys.updated_at`, `sys.tags`, `sys.zettel_boxes`, `sys.source`, `sys.type`, `sys.primary_resource_type`, `sys.starred_at`, `sys.images`.
- Display-only system columns (no filter or sort): `sys.cover`, `sys.audio`, `sys.video`, `sys.file`.
- Property fields: `prop.<key>`, where `<key>` comes from `dino view fields --format json`. Keys are stable; a renamed display name does not change the reference.
- Use only field entries with `available: true`. Malformed or migration-pending property definitions remain visible with an `error` so they do not hide healthy fields.

## Filter Envelope

```json
{
  "version": 1,
  "root": {
    "kind": "group",
    "operator": "and",
    "children": [
      { "kind": "condition", "field": "prop.status", "operator": "equals", "value": "option-id" },
      { "kind": "condition", "field": "prop.score", "operator": "greaterThanOrEqual", "value": 10 }
    ]
  }
}
```

Set `root` to `null` for no filter. A node is one of:

- Condition: `{ "kind": "condition", "field": "...", "operator": "...", "value": ... }`
- Group: `{ "kind": "group", "operator": "and|or", "children": [...] }`
- Negation: `{ "kind": "not", "child": ... }`

Limits: maximum 8 levels, 100 nodes, and 2,000 characters per string value.

### Versions

- `version: 1` — plain conditions.
- `version: 2` — required when any condition uses a dynamic date operator: `withinLastDays` (integer 1-3650, includes today), `isToday`, `isThisWeek` (Monday-start), `isThisMonth`. The last three take no value. They are evaluated in `--time-zone` (default: the machine zone).
- `version: 3` — required when any condition uses `"valueRef": { "kind": "self", "field": "sys.id" | "sys.<field>" | "prop.<key>" }` instead of `value`. `$self` refs bind to the host note in `dino view note-detail`. A standalone `view query` of such a view filters by its `noteDetail` rule instead (listing the host notes); without a rule it matches nothing.

## Operators by Field Type

| Type | Operators | Value |
|------|-----------|-------|
| text/url/email/phone | `equals`, `notEquals`, `contains`, `notContains`, `isEmpty`, `isNotEmpty` | string |
| select/status | `equals`, `notEquals`, `isEmpty`, `isNotEmpty` | stable option ID |
| multi-select | `contains`, `notContains`, `isEmpty`, `isNotEmpty` | stable option ID |
| `sys.tags` | `isEmpty`, `isNotEmpty` (concrete tag values cannot be validated) | none |
| relation, `sys.zettel_boxes` | `equals`, `notEquals`, `contains`, `notContains`, empty checks | note or box ID |
| number/unique_id | equality, ordered comparisons, empty checks | number |
| date/datetime | dynamic date operators, equality, ordered comparisons, empty checks | valid calendar date or ISO datetime |
| checkbox | `equals`, `notEquals`, `isEmpty`, `isNotEmpty` | boolean |
| files/place, `sys.images`, `sys.starred_at` | `isEmpty`, `isNotEmpty` | none |
| `sys.primary_resource_type` | `equals`, `notEquals`, empty checks | `audio`, `video`, `file`, or `image` |

`equals` or `notEquals` with a missing/null value means empty/not-empty. Empty-check operators do not accept a value.
`view fields` returns the exact `filterOperators` for every field; prefer it over this table.

## Config Envelope

```json
{
  "version": 1,
  "layout": "table",
  "columns": [{ "field": "sys.title" }, { "field": "prop.amount" }, { "field": "prop.qty" }],
  "sort": [{ "field": "prop.amount", "direction": "desc" }],
  "computedColumns": [{ "id": "total", "title": "Total", "expr": { "op": "*", "left": "prop.amount", "right": "prop.qty" } }],
  "stats": [{ "id": "sum", "title": "Spend", "measure": { "field": "prop.amount", "aggregation": "sum" } }],
  "summary": [{ "column": "calc.total", "aggregation": "sum" }],
  "charts": [{
    "id": "per-month", "type": "bar",
    "dimensions": [{ "field": "sys.created_at", "timeBucket": "month" }],
    "measures": [{ "field": null, "aggregation": "count" }]
  }]
}
```

- `layout`: `table`, `list`, or `card`. `--layout` on create/update overrides it.
- `columns`: 1-100 unique fields; `sys.title` must be first.
- `sort`: up to 20 unique fields. Do not sort list fields (multi-select, relation, tags, boxes), files, place, `sys.images`, `sys.primary_resource_type`, `sys.starred_at`, or display-only fields. Empty values sort last; note ID descending breaks ties.
- `computedColumns` (max 10): per-row `*`, `/`, `+`, `-` over two numeric `prop.*` fields; returned as `calc.<id>`. Division by zero or a missing side yields `null`.
- `stats` (max 6), `summary` (max 20, one per column), `charts` (max 5; types `bar`, `line`, `pie`, `area`, `contribute`, `waterfall`, `ring`, `funnel`, `scatter`, `treemap`). Measures: `count` needs `"field": null`; `sum`/`avg` need exactly one numeric `prop.*` field or an `expr`. A date dimension needs a `timeBucket` (`hour`, `day`, `week`, `month`); `contribute` charts need `day`.
- `noteDetail`: `{ "field": "prop.kind", "value": "book" }` embeds the view on notes whose field equals the value, binding its `$self` refs to that note.
- The default config is title + updated time, sorted by updated time descending.

## Query Output

- `data.columns` reports type, property type, date mode, option catalog, and `computed` expressions.
- `data.rows[].values` contains stored IDs, scalar/list values, and `calc.<id>` results.
- `data.rows[].displayValues` resolves select/status IDs to label/color/group/archive metadata and box IDs to paths.
- `data.totalCount` counts all matches. Continue with `data.nextOffset` until `data.hasMore` is false.
- `dino view analytics` returns `data.stats`, `data.charts[].rows` (`bucket`, `value`, `label`), and `data.summary`; `unavailable: true` marks an item whose field no longer qualifies.
