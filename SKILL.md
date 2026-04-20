---
name: github-explorer
description: "Deep-dive analysis of GitHub projects. Use when the user mentions a GitHub repo/project name and wants to understand it — triggered by phrases like '帮我看看这个项目', '了解一下 XXX', '这个项目怎么样', '分析一下 repo', or any request to explore/evaluate a GitHub project. Covers architecture, community health, competitive landscape, and cross-platform knowledge sources."
---

# GitHub Explorer — 项目深度分析

> **Philosophy**: README 只是门面，真正的价值藏在 Issues、Commits 和社区讨论里。

## Workflow

```
[项目名] → [1. 定位 Repo] → [2. 多源采集] → [3. 分析研判] → [4. 结构化输出]
```

### Phase 1: 定位 Repo

- 用 `web_search` 搜索 `site:github.com <project_name>` 确认完整 org/repo
- 用 `search-layer`（Deep 模式 + 意图感知）补充获取社区链接和非 GitHub 资源：
  ```bash
  python3 skills/search-layer/scripts/search.py \
    --queries "<project_name> review" "<project_name> 评测 使用体验" \
    --mode deep --intent exploratory --num 5
  ```
- 用 `web_fetch` 抓取 repo 主页获取基础信息（README、Stars、Forks、License、最近更新）

### Phase 2: 多源采集（并行）

**⚠️ GitHub 页面抓取规则（强制）**：GitHub repo 页面是 SPA（客户端渲染），`web_fetch` 只能拿到导航栏壳子，**禁止用 web_fetch 抓 github.com 的 repo 页面**。一律使用 GitHub API：
- README: `curl -s -H "Authorization: token {PAT}" -H "Accept: application/vnd.github.v3.raw" "https://api.github.com/repos/{owner}/{repo}/readme"`
- Repo 元数据: `curl -s -H "Authorization: token {PAT}" "https://api.github.com/repos/{owner}/{repo}"`
- Issues: `curl -s -H "Authorization: token {PAT}" "https://api.github.com/repos/{owner}/{repo}/issues?state=all&sort=comments&per_page=10"`
- Commits: `curl -s -H "Authorization: token {PAT}" "https://api.github.com/repos/{owner}/{repo}/commits?per_page=10"`
- File tree: `curl -s -H "Authorization: token {PAT}" "https://api.github.com/repos/{owner}/{repo}/git/trees/{branch}?recursive=1"`

PAT 见 TOOLS.md。

以下来源**按需检查**，有则采集，无则跳过：

| 来源 | URL 模式 | 采集内容 | 建议工具 |
|---|---|---|---|
| GitHub Repo | `github.com/{org}/{repo}` | README、About、Contributors | GitHub API (see above) |
| GitHub Issues | `github.com/{org}/{repo}/issues?q=sort:comments` | Top 3-5 高质量 Issue | `browser` |
| 中文社区 | 微信/知乎/小红书 | 深度评测、使用经验 | `content-extract` |
| 技术博客 | Medium/Dev.to | 技术架构分析 | `web_fetch` / `content-extract` |
| 讨论区 | V2EX/Reddit | 用户反馈、槽点 | `search-layer`（Deep 模式） |

#### search-layer 调用规范

使用 `search-layer` v2 Deep 模式 + 意图感知（Brave + Exa + Tavily 三源并行去重）。默认项目调研命令：

```bash
python3 skills/search-layer/scripts/search.py \
  --queries "<project> review" "<project> 评测 使用体验" \
  --mode deep --intent exploratory --num 5
```

完整意图类型和场景用法参见 [references/search-patterns.md](references/search-patterns.md)。

降级规则：Exa/Tavily 任一 429/5xx → 继续用剩余源；脚本整体失败 → 退回 `web_search` 单源。

#### 抓取降级规则

当 `web_fetch` 遇到以下情况时，**必须**升级为 `content-extract`（`python3 skills/content-extract/scripts/content_extract.py --url <URL>`）：
1. **域名限制**: `mp.weixin.qq.com`, `zhihu.com`, `xiaohongshu.com`
2. **结构复杂**: 大量公式/复杂表格/返回的 Markdown 极其凌乱
3. **内容缺失**: 反爬返回空内容或 Challenge 页面

### Phase 3: 分析研判

基于采集数据进行判断：

- **项目阶段**: 早期实验 / 快速成长 / 成熟稳定 / 维护模式 / 停滞（基于 commit 频率和内容）
- **精选 Issue 标准**: 评论数多、maintainer 参与、暴露架构问题、或包含有价值的技术讨论
- **竞品识别**: 从 README 的 "Comparison"/"Alternatives" 章节、Issues 讨论、以及 web 搜索中提取

### Phase 4: 结构化输出

严格按照 [references/output-template.md](references/output-template.md) 中的报告模板和排版规则输出。**每个模块都必须有实质内容或明确标注"未找到"**。输出前必须完成模板中的自检清单。

## Execution Notes

- 优先使用 `web_search` + `web_fetch`（非 GitHub 页面），browser 作为备选
- 并行采集不同来源以提高效率
- 所有链接必须真实可访问，不要编造 URL
- 中文输出，技术术语保留英文

## Dependencies

- **Built-in tools**: `web_search`, `web_fetch`, `browser`
- **Skills**: `search-layer` (multi-source search + intent scoring), `content-extract` (anti-crawler extraction fallback)
