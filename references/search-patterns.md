# search-layer v2 Usage Patterns

## Intent-Based Search Commands

| Scenario | Command | Notes |
|------|------|------|
| **Project research (default)** | `python3 skills/search-layer/scripts/search.py --queries "<project> review" "<project> 评测" --mode deep --intent exploratory --num 5` | Multi-query parallel, authority-ranked |
| **Latest updates** | `python3 skills/search-layer/scripts/search.py "<project> latest release" --mode deep --intent status --freshness pw --num 5` | Freshness-prioritized, past-week filter |
| **Competitor comparison** | `python3 skills/search-layer/scripts/search.py --queries "<project> vs <competitor>" "<project> alternatives" --mode deep --intent comparison --num 5` | Comparison intent, keyword+authority weighted |
| **Quick link lookup** | `python3 skills/search-layer/scripts/search.py "<project> official docs" --mode fast --intent resource --num 3` | Exact match, fastest |
| **Community discussion** | `python3 skills/search-layer/scripts/search.py "<project> discussion experience" --mode deep --intent exploratory --domain-boost reddit.com,news.ycombinator.com --num 5` | Community site boosting |

## Intent Types

| Intent | Purpose |
|--------|---------|
| `factual` | Facts and definitions |
| `status` | Current state / recent updates |
| `comparison` | Head-to-head evaluation |
| `tutorial` | How-to guides |
| `exploratory` | Broad research (default for project analysis) |
| `news` | Recent news coverage |
| `resource` | Locate specific resources/docs |

## Degradation Rules

- Exa/Tavily 429/5xx → continue with remaining sources
- Script failure → fall back to `web_search` single-source
- Without `--intent` flag → v1 behavior (no scoring, original order)
