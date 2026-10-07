# Dinox CLI Skills

Portable [Agent Skills](https://agentskills.io) for [Dinox CLI](https://github.com/ryzencool/dinox-cli). Install them in any compatible agent, then use natural-language requests to operate your Dinox knowledge base through `dino`.

> For agent / script integrations, prefer `dino ... --format json`.
> If an agent is unsure how to call a command, inspect it first with `dino schema <path>`.
> For abnormal behavior, suspected stale data, missing search results, daemon failures, upload backlog, or local DB/index concerns, run `dino doctor --sync-timeout 20000 --format json` first.
> Structured success is `{ "ok": true, "data": ... }`; read command fields from `data` and inspect optional top-level `_notice` separately.
> Structured failures include top-level `code`, `recoverable`, `exit_code`, and `suggested_action`; agents should branch on those fields and inspect `error` for details.
> Default online reads use the daemon-owned DB runtime over a private local socket; retry with `--offline` only when the user accepts local-cache semantics.
> For online Agent calls whose schema exposes `--sync-timeout`, use a bounded value such as `20000` and set the host timeout 5-10 seconds higher. If the host times out before a structured envelope arrives, verify the result before retrying.

## Install Skills

Install globally for all four supported clients with the pinned installer:

```bash
npx --yes skills@1.5.16 add ryzencool/dinox-cli-skills \
  -g \
  -a codex -a claude-code -a hermes-agent -a openclaw \
  --skill '*' \
  -y
```

Do not omit the explicit Agent list. Auto-detection can reuse previous host
state, skip clients, and still exit successfully. Target one client explicitly
when that is the intended scope:

```bash
npx --yes skills@1.5.16 add ryzencool/dinox-cli-skills -g -a codex --skill '*' -y
npx --yes skills@1.5.16 add ryzencool/dinox-cli-skills -g -a claude-code --skill '*' -y
npx --yes skills@1.5.16 add ryzencool/dinox-cli-skills -g -a hermes-agent --skill '*' -y
npx --yes skills@1.5.16 add ryzencool/dinox-cli-skills -g -a openclaw --skill '*' -y
```

Global installation is the reliable multi-client default. Verify actual
`SKILL.md` files under `~/.agents/skills`, `~/.claude/skills`,
`~/.hermes/skills`, and `~/.openclaw/skills`; installer inventory output is not
proof that every adapter path exists. Do not assume the
installer always creates symlinks: single-client installs may copy files.

For project-local installation, omit `-g`, create `.hermes/` and `skills/`
before a multi-client install, and verify `.agents/skills`, `.claude/skills`,
`.hermes/skills`, and `skills` afterward. Hermes does not discover project
`.hermes/skills` by default; prefer its global install or add the project's
`.agents/skills` to `skills.external_dirs` in `~/.hermes/config.yaml`. Avoid
project installation inside the Dinox CLI source repository because OpenClaw's
project target is the canonical `skills/` directory itself.

For a CLI-matched, immutable installation, run `dino info --format json` and execute `data.skills_install_command`. It pins the standalone repository to the matching `v<CLI version>` tag instead of tracking the repository default branch.

Check installed skills at any time with `dino skills doctor --format json`. `data.current` reports each current skill as `current`, `outdated`, `missing`, `foreign`, or `unknown-version` against the CLI version and includes the matching pinned `installCommand`; `data.skills` lists retired Dinox skills. Remove retired skills that exactly match the official inventory with `dino skills migrate --dry-run --format json`, then `--confirm` after review.

The portable behavior lives in standard `SKILL.md` files. Agent-specific metadata under paths such as `agents/`, `.claude-plugin/`, `.codex-plugin/`, or `adapters/` is optional presentation or packaging support; a skill must remain usable when a client ignores those adapters.

## Install And Initialize CLI

Install Dinox CLI globally:

```bash
npm install -g @dinoxx/dinox-cli
```

Check the installation:

```bash
dino info --format json
```

Process-only auth for AI/CI:

```bash
export DINOX_TOKEN="<your-token-or-Bearer-token>"
dino auth status --offline --format json
dino sync --strict --sync-timeout 20000 --format json
```

Persistent login, run by the user in their own terminal:

```bash
printf '%s' "$DINOX_TOKEN" | dino auth login --token-stdin --sync-timeout 20000 --format json
dino sync --strict --sync-timeout 20000 --format json
```

Security note: never paste tokens into chat logs. `DINOX_TOKEN` takes precedence over saved config, supports raw token or `Bearer ...`, and is not persisted by the CLI. A running agent only sees a process token when it inherited that environment at launch; exporting it in another terminal does not update the agent process.

For completeness-sensitive analysis such as latest notes, date ranges, monthly summaries, duplicates, stats, or exports, use `dino sync --strict --sync-timeout 20000 --format json` first or add `--require-sync` to the read command. Set the host tool timeout several seconds higher. Strict freshness waits for the current whole-second checkpoint, downloads, and local token indexing; active uploads do not block it, and structured success appears only after runtime cleanup.

## Repository Source

Canonical development source for packaged skills:

```bash
https://github.com/ryzencool/dinox-cli/tree/main/skills
```

Standalone distribution mirror:

```bash
https://github.com/ryzencool/dinox-cli-skills
```

The standalone repository is generated during a tagged CLI release. Do not edit release files there; make changes under `<dinox-cli>/skills` and publish them through the CLI release workflow. The mirror preserves only `.git`, so every file included in a release tag comes from the canonical source.

`src/skills/registry.ts` is the single list of published skills and the generated blocks each skill contains. Regenerate every generated block (runtime contract, command sections, full command reference) and `collection.json` with:

```bash
pnpm skills:sync
```

Validate skill structure, evals, generated-block freshness, the collection manifest, and that every `dino ...` invocation in skill markdown resolves to a real command and option with:

```bash
pnpm skills:check
```

Preview or verify the standalone mirror without changing it:

```bash
pnpm run skills:sync:standalone:dry-run
pnpm run skills:sync:standalone:check
```

Apply the mirror locally when preparing or diagnosing a release:

```bash
pnpm run skills:sync:standalone
```

`skills/collection.json` records the collection version, exact source tag, standalone tag, pinned install command, license, and public skill inventory. The standalone tag annotation and mirror commit also record the raw CLI source SHA.

Tagged releases generate and validate the canonical source, fully stage and verify the standalone mirror, and dry-run the npm artifact without release credentials. They then commit and tag the preflighted skills mirror before publishing npm, so every CLI package points to an existing immutable skills release. A retry accepts an existing npm version only when its registry integrity matches the local tarball, and accepts an existing skills tag only when its tree matches the generated release.

Configure `SKILLS_REPO_SSH_KEY` with the private half of a write-enabled deploy key whose public half is attached only to `dinox-cli-skills`; do not reuse a personal GitHub token.

Skill authoring conventions live in:

```bash
skills/BEST_PRACTICES.md
```

## Available Skills

| Skill | Description |
|-------|-------------|
| **dino-auth** | Check auth status and safely guide login/logout |
| **dino-sync** | Sync local cache with the cloud and report status |
| **dino-config** | Read or set CLI config such as `sync.timeoutMs` |
| **dino-note** | Search, read, create, update, organize, star, or delete notes |
| **dino-manage-todo** | Search, create, append, or update todo tasks from notes |
| **dino-storage** | List custom storage configs, test S3, upload files, and inspect usage |
| **dino-manage-tags** | List, create, rename, move, merge, or clean up tags |
| **dino-manage-boxes** | List, create, rename, move, merge, or clean up card boxes |
| **dino-manage-prompts** | List or create reusable prompt templates |
| **dino-manage-views** | List, create, update, query, count, or delete saved table views |
| **dino-update-cli** | Upgrade installed `@dinoxx/dinox-cli` |
| **dino-dinox** | Bootstrap installation and route Dinox requests to the focused workflow skills |

## Usage Examples

```
> /dino-auth status
> /dino-sync
> dino doctor --sync-timeout 20000 --format json
> /dino-config get sync.timeoutMs
> /dino-config set sync.timeoutMs 20000
> /dino-note 搜索最近 7 天的 AI 笔记
> /dino-note 01924f8a-... 预览前 30 行
> /dino-note 创建一条标题为“今日笔记”的笔记
> /dino-note 01924f8a-... 更新 tags=work,ai boxes=Inbox
> /dino-note 01924f8a-... 标星
> /dino-note 01924f8a-... 删除
> /dino-manage-todo 搜索最近 7 天未完成且带 work 标签的任务
> /dino-manage-todo 创建一个待办笔记，任务：补交报销单、整理发票
> /dino-manage-todo 在笔记 01924f8a-... 末尾追加任务：联系供应商
> /dino-manage-todo 把 task-uuid 更新为 completed
> /dino-storage list
> /dino-storage test --storage-id 0199...
> /dino-storage upload ./report.pdf --storage-id 0199... --category files
> /dino-storage stats
> /dino-manage-tags reading/tech
> /dino-manage-boxes 项目笔记
> /dino-manage-prompts
> /dino-manage-prompts --name 周报助手 --prompt "请基于本周笔记输出一份简洁周报"
> /dino-manage-views 创建一个只显示进行中项目的视图
> /dino-manage-views 查询 Reading Queue 视图里的全部笔记
> /dino-update-cli
```

Skill-name or slash invocation depends on the host agent. Natural-language requests are portable across clients, for example:

- "帮我搜索最近一周的笔记"
- "创建一条笔记，标题是..."
- "列出所有标签"
- "查看这条笔记的详细内容"
- "把这条笔记加到 Inbox 并打星"
- "把这个文件上传到我的 S3"
- "帮我创建一个 todo 列表"
- "把这个 task 标记为完成"
- "列出我保存的 prompts"
- "帮我新增一个周报 prompt"
- "创建一个按状态筛选、按优先级排序的项目视图"
- "查询 Reading Queue 视图中的全部笔记"
- "把 dinox-cli 更新到最新版本"
