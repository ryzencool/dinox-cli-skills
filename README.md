# Dinox CLI Skills

Bundled agent skills for [Dinox CLI](https://github.com/ryzencool/dinox-cli) — manage your Dinox knowledge base (notes, tags, card boxes) directly from the current repository.

> For agent / script integrations, prefer `dino --format json ...`.
> Legacy `--json` still works, but it returns YAML for backward compatibility.
> If an agent is unsure how to call a command, inspect it first with `dino schema <path>`.

## Prerequisites

Install Dinox CLI globally:

```bash
npm install -g @dinoxx/dinox-cli
```

Login:

```bash
dino auth login "<your-token>"
dino sync
```

Security note: never paste your token into chat logs. Run `dino auth login "<your-token>"` directly in your own terminal session.

## Location

These skills now live inside the `dinox-cli` repository:

```bash
<repo>/skills
```

If your agent supports adding a skills directory manually, point it at the current repo's `skills/` directory:

```bash
claude --add-dir /path/to/dinox-cli/skills
```

When command schemas change, regenerate the bundled background reference with:

```bash
pnpm skills:sync
```

Validate bundled skill structure and shared-guidance references with:

```bash
pnpm skills:check
```

Skill authoring conventions live in:

```bash
skills/BEST_PRACTICES.md
```

## Available Skills

| Skill | Command | Description |
|-------|---------|-------------|
| **dino-auth** | `/dino-auth [status|login|logout]` | Check auth status and safely guide login/logout |
| **dino-sync** | `/dino-sync` | Sync local cache with the cloud and report status |
| **dino-config** | `/dino-config [get|set] ...` | Read or set CLI config (e.g. `sync.timeoutMs`) |
| **dino-note** | `/dino-note [request or note id]` | Search, read, create, update, star, or delete notes |
| **dino-manage-todo** | `/dino-manage-todo [query or task]` | Search/create/append/update todo tasks from notes |
| **dino-storage** | `/dino-storage [list|test|upload <file>|stats]` | List custom storage configs, test your S3, upload a file, and inspect usage |
| **dino-manage-tags** | `/dino-manage-tags [name]` | List all tags or create a new tag |
| **dino-manage-boxes** | `/dino-manage-boxes [name]` | List all card boxes or create a new one |
| **dino-manage-prompts** | `/dino-manage-prompts [name] [prompt]` | List prompts or create a reusable prompt template |
| **dino-update-cli** | `/dino-update-cli` | Upgrade installed `@dinoxx/dinox-cli` to the latest version |
| **dino-shared** | *(internal)* | Shared safety/auth/output rules used by the other bundled skills |
| **dino-dinox** | *(auto)* | Background context — Claude automatically knows all `dino` commands |

## Usage Examples

```
> /dino-auth status
> /dino-sync
> /dino-config get sync.timeoutMs
> /dino-config set sync.timeoutMs 600000
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
> /dino-update-cli
```

You can also just ask naturally — the `dino-dinox` background skill lets Claude understand requests like:

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
- "把 dinox-cli 更新到最新版本"
