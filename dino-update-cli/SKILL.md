---
name: dino-update-cli
description: >
  Update Dinox CLI itself. Use when the user wants to upgrade
  `@dinoxx/dinox-cli`, refresh to the latest command features, or fix behavior
  caused by an outdated CLI version.
argument-hint: ""
allowed-tools:
  - Bash
metadata:
  requires:
    bins: ["dino"]
  cliHelp: "dino update --help"
  category: "maintenance"
  risk: "write"
---

# Update Dinox CLI

**CRITICAL — Before doing anything else, first read [`../dino-shared/SKILL.md`](../dino-shared/SKILL.md). It contains shared auth, output, and write-safety rules.**

Use this skill to upgrade the installed Dinox CLI package.

## Safety & Boundaries (Must Follow)

- Updating the CLI changes globally installed tooling. Show the exact command(s) you will run and get explicit confirmation before updating.
- Only run `dino update` (and `dino info` for verification). Do not run unrelated shell commands unless the user explicitly asks.
- Do not run package-manager-specific global update commands directly unless the user explicitly requests it; `dino update` already performs auto-detection.

## Primary Command

```bash
dino update
```

`dino update` auto-detects the package manager used for the current installation
and runs the matching global update command:

- npm: `npm update -g @dinoxx/dinox-cli`
- pnpm: `pnpm update --global @dinoxx/dinox-cli`
- yarn: `yarn global upgrade @dinoxx/dinox-cli`
- bun: `bun add --global @dinoxx/dinox-cli`

## Verification

After update succeeds, confirm the installed version:

```bash
dino info --format json
```

## Post-Update AI Reminder

After `dino update` succeeds, the CLI prints:

- `skills repo: https://github.com/ryzencool/dinox-cli/tree/main/skills`
- an AI reminder to review and update local Dinox skills

If the user explicitly asks to update this skills repo as well, treat that as a separate request (git operations) and do not do it implicitly as part of updating the CLI.

## Error Handling

- If update fails due to permissions, ask the user to rerun in an environment
  with global package install permissions.
- If `dino` is not found, install first:
  `npm install -g @dinoxx/dinox-cli`
