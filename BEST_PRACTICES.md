# Dinox Skill Best Practices

This document defines the preferred structure for bundled Dinox CLI skills.
It is intentionally modeled after the stronger parts of the `lark-cli` skill
system, adapted to Dinox's smaller command surface.

## Goals

- Keep skill behavior aligned with the actual `dino` CLI
- Make bundled skills safe for agent execution
- Reduce drift between command schemas, generated references, and hand-written guidance
- Give future skills one obvious structure to follow

## Required Frontmatter

Every skill must start with YAML frontmatter.

Required fields:

- `name`
- `description`
- `metadata`
- `metadata.requires.bins`

Recommended fields for user-invocable skills:

- `argument-hint`
- `allowed-tools`
- `metadata.cliHelp`
- `metadata.category`
- `metadata.risk`

Required fields for background/shared skills:

- `user-invocable: false`
- `metadata.cliHelp`

## Frontmatter Example

```yaml
---
name: dino-note
description: >
  Search, read, create, update, star, and delete Dinox notes.
argument-hint: "[request or note id]"
allowed-tools:
  - Bash
  - Write
metadata:
  requires:
    bins: ["dino"]
  cliHelp: "dino note --help"
  category: "notes"
  risk: "read"
---
```

## Shared Guidance

All user-invocable `dino-*` skills must instruct the agent to read
`../dino-shared/SKILL.md` before executing workflow logic.

Use a strong, early line such as:

```md
**CRITICAL — Before doing anything else, first read [`../dino-shared/SKILL.md`](../dino-shared/SKILL.md).**
```

## Recommended Body Structure

For user-invocable skills, prefer this order:

1. Title
2. Shared-guidance reference
3. Short purpose statement
4. `## Safety & Boundaries`
5. `## Instructions` or `## Intent Mapping`
6. `## Commands`
7. `## Workflow`
8. `## Error Handling`

For background skills:

1. Title
2. Scope statement
3. Global rules
4. Shared patterns or references

## Command Guidance Rules

- Prefer `dino ... --format json` for commands that return structured data.
- Prefer `dino schema <path>` when the skill needs to explain or inspect a command shape.
- Use `--dry-run` before confirmed writes whenever the command supports it.
- Do not recommend the legacy `--json` output flag in user-facing skills.
- Prefer public CLI names over storage-oriented names:
  - `boxes`, not `zettel_boxes`
  - `prompt`, not `cmd`

## References Pattern

For complex skills, keep the main `SKILL.md` short and move detailed scenarios
into `references/*.md`.

Prefer domain skills over CRUD-specific micro-skills. If multiple commands act
on the same business object and share the same safety model, keep them in one
user-facing skill and route internally with references.

Recommended triggers for introducing references:

- More than one workflow branch
- Multi-step decision trees
- Many domain-specific examples
- Tool-specific caveats that make the main skill noisy

Avoid creating a separate reference file when the content is only a short step
list or a tiny command snippet. If a section is roughly 20-40 lines and is
needed almost every time the skill runs, keep it in `SKILL.md`.

## Quality Gates

Bundled skills should pass `pnpm skills:check`.

The checker currently enforces:

- Valid frontmatter
- Matching `name`
- Presence of `description`
- Presence of `metadata`
- Presence of `metadata.requires.bins`
- Shared-guidance reference for user-facing skills
- No legacy `--json` recommendations in user-facing skills
- `--format json` guidance where `dino` commands appear

The checker should remain conservative: only enforce rules that are stable and
worth keeping across the whole skill set.
