# paper-agent

> 每天自动读论文、出日报/周报的 Claude Code 插件。`/paper-daily <主题> <关键词>` 一键生成结构化日报。

## 它做什么

- 按你给的 **主题 + 关键词** 搜 arXiv / Web（WebSearch 为主，不可用时自动退回 `curl` / arXiv API / GitHub raw）。
- 对**已读论文去重**（基于 `papers/seen_papers.md`）。
- 按**与主题的相关性**分高 / 中 / 低优先级。
- 每篇重要论文用**六段式**总结：Challenge / Motivation / Insight / Main method / Effect / Results / Research Value。
- 生成**中文日报**（默认）+ **英文 related-work** 段落；可切全英文。
- `/paper-weekly` 把最近 7 天的日报综合成一份**周报**。
- 所有文件写在你**当前工作目录**，插件本身不带任何个人数据，换机器拷过去照样用。

## 前置条件

- 已安装 [Claude Code](https://claude.com/claude-code) 并登录。
- （可选）设置 `ANTHROPIC_API_KEY`。
- 能用 WebSearch / WebFetch；不可用时会自动退回 `curl` + arXiv API。

## 安装

**方式 A — marketplace（推荐）**

```
/plugin marketplace add <your-github-url>
/plugin install paper-agent
```

**方式 B — 手动 clone**

```bash
git clone <your-github-url> ~/.claude/plugins/paper-agent
```

装好后重启或重载 Claude Code，即可用 `/paper-daily` 和 `/paper-weekly`。

## 用法

```
/paper-daily robot manipulation VLA pi0 flow matching
```

- 第一个词组 = 主题，后面 = 关键词。
- 不带参数：`/paper-daily` → 读当前目录的 `memory.md` 复用上次配置。
- 切英文日报：在参数里加 `lang=en`，或在 `memory.md` 写 `language: en`。

```
/paper-weekly
```

- 综合最近 7 天的日报 + 论文记录，输出周报。建议先连续跑几天 `/paper-daily` 再用。

## 它会在当前目录创建的文件

| 文件 | 作用 |
|---|---|
| `papers/seen_papers.md` | 已读论文清单（去重基准） |
| `papers/<日期>/papers.md` | 当天所有论文的六段式总结 |
| `reports/<日期>_morning.md` | 当天日报 |
| `reports/<日期>_weekly.md` | 周报 |
| `memory.md` | 首次运行自动生成，存主题/关键词/语言/监控清单 |

## 定制（`memory.md`）

首次 `/paper-daily` 会自动生成 `memory.md`。你可以手动编辑：

```markdown
topic: <一句话研究主题>
keywords:
  - <关键词>
  - <关键词>
language: zh          # zh（默认，中文日报）或 en（英文日报）
watch-repos:          # 可选：要监控的 GitHub awesome 列表 / 仓库
  - <repo url>
```

规则：**命令行参数优先于 `memory.md`**。所以临时换主题直接在 `/paper-daily` 后面带参数即可，不用改文件。

## 自检（安装后）

1. 新建一个空目录并 `cd` 进去。
2. 跑：`/paper-daily retrieval-augmented generation RAG benchmark`
3. 检查：
   - 生成了 `papers/seen_papers.md`、`papers/<今天>/papers.md`、`reports/<今天>_morning.md`、`memory.md`。
   - 日报含 **Top 3** 和**英文 related-work 句子**。
   - 报告标题是 `# 今日 retrieval-augmented generation Paper 晨报`（动态拼接你的主题，证明没有写死领域）。
   - `papers/seen_papers.md` 已记录今天看过的论文。

如果当天没搜到新论文，日报仍会生成，并明确写"今日无新论文"——不会留空报告，也不会编造内容。

## 许可

MIT
