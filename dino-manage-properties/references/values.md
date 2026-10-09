# Property Value Shapes

Use this reference when composing `--set`, `--patch`, `--values`, or `note create --properties` values.
`--set key=value` parses `value` as JSON when it is valid JSON (`4`, `true`, `["a","b"]`) and otherwise
sends it as a plain string, so `--set 状态=进行中` and `--set code=007` stay strings.

| Type | Stored value | Accepted input |
|------|--------------|----------------|
| `text`, `email`, `phone` | trimmed string (max 2000) | string, number, boolean |
| `url` | string | http(s) URL; `config.schemes` may restrict schemes |
| `number` | finite number | number or numeric string; `config.min`/`max` apply |
| `date` | `YYYY-MM-DD` (date mode) or ISO datetime (datetime mode) | parsable date string or epoch ms |
| `select`, `status` | option `id` | option id or label; an `open` select (the default) adds an unknown label as a new option, `status` only accepts existing options |
| `multi_select` | array of option ids | array of ids or labels (max 50, `config.maxSelections`) |
| `checkbox` | boolean | boolean, `"true"/"false"`, `1/0` |
| `relation` | array of note ids | array of note ids |
| `files` | `[{"resourceId":"<uuid>","path"?}]` | same shape |
| `unique_id` | positive integer, shown as `PREFIX-n` | omit to auto-issue, or `"PREFIX-12"` / `12` |
| `place` | `{"lat":..,"lon":..,"name"?}` | same shape |

- `null` in `--patch` removes the key; `prop slots --clear` keeps the key with a `null` value.
- Keys without a definition are stored as given and read back as untyped (`defined: false`).

## Config Envelopes

Pass with `prop create --config` or `prop update --config`; `--clear-config` restores the type default.

```json
{"version":1,"type":"date","mode":"datetime"}
{"version":1,"type":"number","format":"currency","currency":"CNY","precision":2}
{"version":1,"type":"select","mode":"closed"}
{"version":1,"type":"multi_select","mode":"open","maxSelections":3}
{"version":1,"type":"url","schemes":["https"]}
```

A `closed` select or multi-select only accepts existing options; `open` (the default for both) adds unknown labels as new options.

## Options

- `--option <label[:color[:group]]>` (create) and `--add-option` (update); colors: gray, red, orange, yellow, green, blue, purple, pink; status groups: todo, in_progress, complete.
- `--rename-option <id|label>=<new>`, `--option-color <id|label>=<color>`, `--archive-option`, `--restore-option` edit one option by id or label.
- `--options @file` replaces the full list: existing options must keep their `id` (archive instead of removing); new items omit `id`.
