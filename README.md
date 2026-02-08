# GitHub Explorer — 项目深度分析 Skill

> README 只是门面，真正的价值藏在 Issues、Commits 和社区讨论里。

GitHub Explorer 是一个 [OpenClaw](https://github.com/openclaw/openclaw) Agent Skill，能够对任意 GitHub 项目进行**多源深度分析**，输出结构化的项目研判报告。

## ✨ 核心特性

- 🔍 **多源采集** — 不只看 README，还会抓取 Issues、Commits、中文社区（知乎/V2EX/微信）、技术博客（Medium/Dev.to）、Twitter 讨论
- 🧠 **AI 研判** — 自动判断项目阶段（早期实验 / 快速成长 / 成熟稳定 / 停滞），精选高质量 Issue
- 🆚 **竞品对比** — 自动识别同赛道项目，给出差异分析
- 🌐 **知识图谱** — 检查 DeepWiki、Zread.ai 等知识源收录情况
- 📰 **社区声量** — 具体引用推文和帖子内容，附原始链接，拒绝空泛描述
- 📄 **结构化输出** — 固定模板，信息密度高，方便快速决策

## 🚀 快速开始

### 使用 npx 安装（推荐）

```bash
npx skills add https://github.com/blessonism/github-explorer-skill
```

### 手动安装

```bash
cd ~/.openclaw/skills/
git clone https://github.com/blessonism/github-explorer-skill.git github-explorer
```

### 使用方法

安装后，直接对你的 OpenClaw Agent 说：

```
帮我看看这个项目 langchain
```

```
分析一下 https://github.com/microsoft/graphrag
```

```
了解一下 ollama 这个项目怎么样
```

Agent 会自动触发 GitHub Explorer，执行多源采集和分析，输出完整的项目研判报告。

## 📋 输出报告结构

报告标题为项目名，**必须链接到 GitHub 仓库**（格式：`# [Project Name](GitHub URL)`）。

| 模块 | 内容 |
|---|---|
| 🎯 一句话定位 | 项目是什么、解决什么问题 |
| ⚙️ 核心机制 | 技术原理/架构，用人话讲清楚 |
| 📊 项目健康度 | Stars/Forks/License/团队/Commit 趋势 |
| 🔥 精选 Issue | Top 3-5 高质量 Issue + 核心讨论点 |
| ✅ 适用场景 | 什么时候该用 |
| ⚠️ 局限 | 什么时候别碰 |
| 🆚 竞品对比 | 同赛道项目差异，附链接 |
| 🌐 知识图谱 | DeepWiki / Zread.ai 收录情况 |
| 🎬 Demo | 在线体验链接 |
| 📄 关联论文 | arXiv 链接 |
| 📰 社区声量 | Twitter + 中文社区具体讨论 |
| 💬 判断 | 主观评价和建议 |

## 🔧 依赖

### 内置工具（OpenClaw 自带）

- `web_search` — Brave Search 检索
- `web_fetch` — 网页内容抓取
- `browser` — 动态页面渲染（备选）

### 依赖 Skills（需额外安装）

| Skill | 用途 | 安装 |
|-------|------|------|
| [search-layer](https://github.com/blessonism/search-layer-skill) | 多源搜索去重（Brave + Exa + Tavily 三源并行） | `npx skills add ...` |
| [content-extract](https://github.com/blessonism/content-extract-skill) | 高保真内容提取（微信/知乎等反爬站点降级方案） | `npx skills add ...` |

> 缺少依赖 Skills 时，github-explorer 会自动降级为仅使用内置工具，核心功能不受影响。

## 📝 设计哲学

1. **信息溯源** — 所有引用必须附原始链接，让读者能追溯到源头
2. **抓取降级** — `web_fetch` 失败时自动升级为 `content-extract`（MinerU），保证内容质量
3. **拒绝空泛** — 社区声量必须引用具体内容，不允许"评价很高"这种概括
4. **并行采集** — 多源同时抓取，提高效率

## 📄 License

[MIT](LICENSE)

---

Made with 🧊 by [blessonism](https://github.com/blessonism) & Ms.Q
