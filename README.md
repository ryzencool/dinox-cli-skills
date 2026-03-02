# dinox-cli-skills

Claude Code skills for [Dinox CLI](https://github.com/ryzencool/dinox-cli) — manage your Dinox knowledge base (notes, tags, card boxes) directly from Claude Code.

## Prerequisites

Install Dinox CLI globally:

```bash
npm install -g @dinoxx/dinox-cli
```

Login:

```bash
dino auth login "Bearer <your-token>"
dino sync
```

## Installation

Add this skills plugin to your Claude Code project:

```bash
claude --add-dir /path/to/dinox-cli-skills
```

Or clone and add:

```bash
git clone https://github.com/ryzencool/dinox-cli-skills.git
claude --add-dir ./dinox-cli-skills
```

## Available Skills

| Skill | Command | Description |
|-------|---------|-------------|
| **dino-search-notes** | `/dino-search-notes [keyword]` | Search notes by keyword, tags, date range |
| **dino-create-note** | `/dino-create-note [title]` | Create a new note with Markdown content |
| **dino-update-note** | `/dino-update-note [id]` | Update note tags/card boxes (single or batch) |
| **dino-view-note** | `/dino-view-note [id]` | View full note details by ID (single or batch) |
| **dino-manage-tags** | `/dino-manage-tags [name]` | List all tags or create a new tag |
| **dino-manage-boxes** | `/dino-manage-boxes [name]` | List all card boxes or create a new one |
| **dino-manage-prompts** | `/dino-manage-prompts [name] [cmd]` | List prompts or create a reusable prompt template |
| **dino-update-cli** | `/dino-update-cli` | Upgrade installed `@dinoxx/dinox-cli` to the latest version |
| **dino-dinox** | *(auto)* | Background context — Claude automatically knows all `dino` commands |

## Usage Examples

```
> /dino-search-notes 机器学习
> /dino-search-notes --tags "work AND NOT archived" --days 7
> /dino-create-note 今日笔记
> /dino-update-note 01924f8a-... --tags "work,ai"
> /dino-update-note --ids @/tmp/note-ids.txt --boxes "Inbox,Project"
> /dino-view-note 01924f8a-...
> /dino-manage-tags reading/tech
> /dino-manage-boxes 项目笔记
> /dino-manage-prompts
> /dino-manage-prompts --name 周报助手 --cmd "请基于本周笔记输出一份简洁周报"
> /dino-update-cli
```

You can also just ask naturally — the `dino-dinox` background skill lets Claude understand requests like:

- "帮我搜索最近一周的笔记"
- "创建一条笔记，标题是..."
- "列出所有标签"
- "查看这条笔记的详细内容"
- "列出我保存的 prompts"
- "帮我新增一个周报 prompt"
- "把 dinox-cli 更新到最新版本"
