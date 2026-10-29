# Semantic Search

Find notes by meaning instead of exact words. These commands query the Dinox semantic index online, then fill in titles, tags, and boxes from the local cache and drop notes deleted locally.

## Choose the command

| Request | Command |
| --- | --- |
| Notes about a topic or idea, phrased differently from the note | `dino note semantic "<query>" --format json` |
| Notes like a given note | `dino note similar <id> --format json` |
| Answer a question from the user's notes, with citations | `dino note context "<question>" --format json` |
| Exact words, filters, dates, recency, counts, completeness | `dino note search` (see [search-and-read](search-and-read.md)) |

## Semantic search

```bash
dino note semantic "how I planned the Q3 launch" --format json
dino note semantic "sleep and productivity" --tags health --days 90 --limit 20 --format json
dino note semantic "contract renewal terms" --include-resources --format json
```

- `--mode hybrid` (default) mixes meaning and keywords; `semantic` uses meaning only; `keyword` uses the index's full-text match.
- Filters: `--tags` (all listed tags, child tags included), `--boxes` (any listed box), `--from` / `--to` / `--days` on created_at.
- `--include-resources` also matches attachments; `notes[].matched_source: resource` marks a hit found in an attachment of that note.
- One entry per note, ranked by `score`, with the best `snippet`, `section`, and `heading_path`. `matches` counts the note's chunks among the hits.
- `in_local_cache: false` means the note is not synced locally yet; its title comes from the index.
- Results are ranked and capped (`--limit` up to 60). They are never a complete list: use `note search` for "all notes that ..." or counts.

Read a hit with `dino note detail <id> --content md --max-chars 4000 --format json` before quoting it.

## Similar notes

```bash
dino note similar <id> --limit 10 --format json
```

The note itself is excluded. `NOTE_NOT_FOUND` either means the note is deleted or not indexed yet (new notes are indexed shortly after upload); `dino graph related <id>` gives link- and tag-based neighbours that work immediately.

## Context for answers

```bash
dino note context "what did I decide about pricing?" --max-tokens 4000 --format json
```

`data.blocks[]` holds assembled `content` with `note_id`, `title`, `score`, and `citations` (`source_type`, `source_id`). Answer from the blocks and cite the note ids; do not present text that is not in a block as coming from the user's notes. `--max-notes` bounds how many notes contribute; `--notes-only` leaves attachments out.

## Freshness and failures

- The commands need the network and refuse `--offline`.
- The index is built asynchronously from uploaded notes. `_notice.index_may_lag` (with `upload_queue_remaining`) means local changes have not reached it yet; a missing note is then not evidence that it does not exist.
- `dropped_deleted_count` reports hits removed because the note is deleted locally.
- `SEMANTIC_SEARCH_UNAVAILABLE`: the service failed or timed out. Fall back to `dino note search` with key terms.
- `AUTH_INVALID`: the service rejected the login; have the user run `dino auth login` in their own terminal.
