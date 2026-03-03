---
name: dino-update-cli
description: >
  Update Dinox CLI itself. Use when the user wants to upgrade
  `@dinoxx/dinox-cli`, refresh to the latest command features, or fix behavior
  caused by an outdated CLI version.
argument-hint: ""
allowed-tools:
  - Bash
---

# Update Dinox CLI

Use this skill to upgrade the installed Dinox CLI package.

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
dino info
```

## Post-Update AI Reminder

After `dino update` succeeds, the CLI prints:

- `skills repo: https://github.com/ryzencool/dinox-cli-skills`
- an AI reminder to review and update local Dinox skills

When the user asks to sync skills, update the skills repo and then verify the key entries are current (especially `dino-dinox`, `dino-update-cli`, and todo-related skills).

## Error Handling

- If update fails due to permissions, ask the user to rerun in an environment
  with global package install permissions.
- If `dino` is not found, install first:
  `npm install -g @dinoxx/dinox-cli`
