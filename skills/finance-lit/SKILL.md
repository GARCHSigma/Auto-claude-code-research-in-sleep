---
name: finance-lit
description: "金融领域文献与数据调研 skill。搜索学术论文、宏观经济数据、市场数据，覆盖 q-fin / SSRN / NBER / Tushare / Alpha Vantage / FRED 等金融专属资源。Use when user says '金融文献调研', '查找学术文献', '市场数据查询', '宏观数据', 'finance literature survey', 'quant research data'."
argument-hint: "[research-topic] — sources: academic | market | macro | alt | all (default: all)"
allowed-tools: Bash, Read, Write, Glob, WebSearch, WebFetch, terminal
category: research
---

# Finance Literature & Data Survey

Research topic: **$ARGUMENTS**

## Constants

| Constant | Default | Description |
|----------|---------|-------------|
| `OUTPUT_DIR` | `~/finance_data/research/` | 研究输出目录 |
| `PAPER_LIBRARY` | `~/finance_data/papers/` | 本地论文库 |
| `DATA_DIR` | `~/finance_data/market/` | 市场数据缓存目录 |
| `MIN_SOURCE_CATEGORIES` | `3` | 最少数据源类别 |
| `CHART_LANG` | `en` | 图表标签英文 |
| `PUSH_TARGET` | `telegram:-1003786012521` | 推送目标 |

---

## Data Sources

### Source Coverage Requirement

> 必须覆盖至少 3 个不同类别的数据源

| Category | Sources | 说明 |
|----------|---------|------|
| `academic` | SSRN / NBER / arXiv q-fin / Semantic Scholar | 理论支撑、方法论 |
| `market` | Tushare / Alpha Vantage / Twelve Data / Baostock | A股/美股/加密货币 |
| `macro` | FRED / PBOC / BOK / Korean Trade | 利率/汇率/信用利差/货币供应 |
| `alt` | Exa Search (金融新闻/研报) | 新闻、舆情、研报 |

---

## Workflow

### Phase 1: Parse Arguments

解析：
- Research topic / query
- `— sources:` academic / market / macro / alt / all（默认 all）
- `— limit:` 结果数量（默认 10）

### Phase 2: Source Coverage Check

必须覆盖至少 3 个不同类别：
```
academic ✅  → SSRN / NBER / arXiv
market  ✅  → Tushare / Alpha Vantage / Baostock
macro   ✅  → FRED / PBOC / BOK
alt     ✅  → Exa Search
```

### Phase 3: Execute Queries

#### Academic Sources

**arXiv (q-fin):**
```bash
curl -s "https://export.arxiv.org/api/query?search_query=cat:q-fin.GN%20OR%20cat:q-fin.ST%20OR%20cat:q-fin.TR&sortBy=submittedDate&max_results=10"
```

**SSRN / NBER:** 使用 WebSearch 搜索

**Semantic Scholar:**
```bash
curl -s "https://api.semanticscholar.org/graph/v1/paper/search?query=[TOPIC]&fields=title,abstract,year,venue,citationCount,externalIds&limit=10"
```

#### Market Data Sources

**Tushare Pro (A股):** 使用 tushare Python API，token 从 env `TUSHARE_TOKEN` 读取

**Alpha Vantage (美股/外汇):** 使用 Alpha Vantage API，key 从 env `ALPHA_VANTAGE_KEY` 读取

**Twelve Data:** 使用 Twelve Data API，key 从 env `TWELVE_DATA_KEY` 读取

**Baostock (A股/港股):** 使用 baostock Python 库

#### Macro Data Sources

**FRED:** 使用 FRED API，key 从 env `FRED_API_KEY` 读取

**PBOC:** 使用 WebSearch / WebFetch 抓取人民银行官网数据

**BOK (韩国银行):** 使用 WebSearch / WebFetch 抓取韩国央行数据

#### Alternative Data

**Exa Search (金融新闻/研报):** 使用 Exa API，key 从 env `EXA_API_KEY` 读取

### Phase 4: Synthesize

- [ ] 对比不同来源结论
- [ ] 识别矛盾证据并分类（方向 / 程度 / 方法论）
- [ ] 形成分级结论（强 / 弱 / 推测）

### Phase 5: Output

```
v1.0 | [DATE] | Finance Literature Survey
═══════════════════════════════════════════════

## 研究问题
[精炼陈述]

## 学术文献发现
| 论文 | 来源 | 年份 | 关键结论 |
|------|------|------|---------|
| ... | SSRN | 2024 | ... |

## 市场数据发现
| 指标 | 来源 | 最新值 | 日期 |
|------|------|--------|------|
| ... | Tushare | ... | 2025-01-01 |

## 宏观数据发现
| 指标 | 来源 | 最新值 | 趋势 |
|------|------|--------|------|
| ... | FRED | ... | ↑ |

## 矛盾证据
⚠️ CONTRADICTION: [描述 + 来源]

## 数据缺口
⚠️ DATA UNAVAILABLE: [描述 + 原因]

## 结论
[结构化 + 数据支撑]

## 附录
- 数据源清单（含 API/URL）
- 工具调用数
```

---

## Integration Contract

| 组件 | 规范 |
|------|------|
| Activation predicate | task 含"金融"/"finance"/"市场"/"宏观"/"论文" → fire |
| Canonical helper | 每个数据源独立调用，token/key 从 env 读取 |
| Failure policy A | 学术/市场：失败 → 降级到同类备用源 |
| Failure policy B | 宏观：失败 → 标注 ⚠️ DATA UNAVAILABLE，继续 |
| Failure policy C | 另类：失败 → 静默跳过 |
| Output artifact | `FINANCE_SURVEY.md` → `~/finance_data/research/` |
| Verifier | 每条结论附 `[source: URL + date]`，无验证的标注"推测" |
| Version pin | `finance-lit v1.0 | 2026-05-18` |

---

## API Key Reference

> API keys stored in environment variables. Skill reads from `${VAR}` — actual values NOT hardcoded.

| Source | Env Variable |
|--------|-------------|
| Tushare Pro | `TUSHARE_TOKEN` |
| Alpha Vantage | `ALPHA_VANTAGE_KEY` |
| Twelve Data | `TWELVE_DATA_KEY` |
| FRED | `FRED_API_KEY` |
| Semantic Scholar | `S2_API_KEY` |
| Exa Search | `EXA_API_KEY` |

用户已有 keys（来自记忆）：
- Tushare: `3953d8e4941aad5cf1a2b5212856d208b4b5c0a5259ad0ef46df04a7`
- Alpha Vantage: `G45JJUVUQ1ZO7JM0`
- Twelve Data: `f00f38fce5e1494db68ffcdd2683eb66`

---

## Skill Metadata

| Field | Value |
|-------|-------|
| Version | v1.0 |
| Author | GARCH QUANT |
| Updated | 2026-05-18 |
| Category | research |
| Based on | ARIS research-lit format |
