---
name: esg-research
description: "ESG / 碳市场 / 绿色金融研究 skill。覆盖 ESG 评分、碳交易市场、绿色债券、可持续投资、气候风险。接入 Bloomberg ESG / Refinitiv ESG / CDP / Carbon Budget / GreenBiz 数据。Use when user says 'ESG研究', '碳市场', '绿色金融', '可持续发展', 'ESG rating', 'carbon market', 'green bond', 'climate risk'."
argument-hint: "[topic] — sub: esg | carbon | green-bond | climate-risk | all (default: all)"
allowed-tools: Bash, Read, Write, Glob, WebSearch, WebFetch, terminal
category: research
---

# ESG & Sustainable Finance Research

Research topic: **$ARGUMENTS**

## Constants

| Constant | Default | Description |
|----------|---------|-------------|
| `OUTPUT_DIR` | `~/finance_data/research/esg/` | 输出目录 |
| `SUB_TOPICS` | `all` | esg / carbon / green-bond / climate-risk |
| `PUSH_TARGET` | `telegram:-1003786012521` | 推送目标 |

---

## Sub-Topic Coverage

| Sub | 数据范围 | 典型数据源 |
|-----|---------|-----------|
| `esg` | ESG评分/排名 | Bloomberg ESG, Refinitiv, MSCI, CDP |
| `carbon` | 碳交易市场/价格 | EU ETS, CA CT, China ETS, Carbon Budget |
| `green-bond` | 绿色债券标准/规模 | Climate Bonds Initiative, ICMA |
| `climate-risk` | 物理/转型风险 | TCFD, NGFS, Swiss Re |

---

## Data Sources

### ESG Ratings

**Bloomberg ESG:**
```bash
WebSearch: site:bloomberg.com/green/ ESG [topic]
WebFetch: https://www.bloomberg.com/professional/support/article-library/
```

**Refinitiv ESG:**
```bash
WebSearch: site:refinitiv.com ESG score [company]
```

**MSCI:**
```bash
WebSearch: site:msci.com ESG rating [topic]
```

**CDP (Carbon Disclosure Project):**
```bash
WebSearch: site:cdp.net climate change [company]
```

### Carbon Markets

**EU ETS (European Union Emissions Trading System):**
```bash
WebSearch: site:ets.europa.eu carbon price
WebFetch: https://ec.europa.eu/clima/eu-action/eu-emissions-trading-system-eu-ets_en
```

**China ETS (全国碳市场):**
```bash
WebSearch: site:mee.gov.cn 碳市场 碳价
WebFetch: https://www.mee.gov.cn/
```

**California Cap-and-Trade:**
```bash
WebSearch: site:arb.ca.gov cap-and-trade allowance price
```

**Carbon Budget (Global Carbon Project):**
```bash
WebSearch: site:globalcarbonproject.org carbon budget [year]
```

### Green Bonds

**Climate Bonds Initiative:**
```bash
WebSearch: site:climatebonds.net green bond standard
```

**ICMA (International Capital Market Association):**
```bash
WebSearch: site:icmagroup.org sustainable finance green bond principles
```

### Climate Risk

**TCFD (Task Force on Climate-related Financial Disclosures):**
```bash
WebSearch: site:tcfdhub.org climate risk scenario
```

**NGFS (Network for Greening the Financial System):**
```bash
WebSearch: site:ngfs.net climate scenario
```

---

## Workflow

### Phase 1: Parse Arguments

- Topic
- `— sub:` esg / carbon / green-bond / climate-risk / all

### Phase 2: Source Coverage

必须覆盖：
- [ ] 至少 1 个 ESG 数据源
- [ ] 至少 1 个碳市场数据源
- [ ] 绿色债券或气候风险数据（至少 1）

### Phase 3: Data Collection

### Phase 4: Analysis

- [ ] ESG 评分与财务表现相关性
- [ ] 碳价历史走势 + 驱动因素
- [ ] 绿色债券市场规模与增速
- [ ] 气候风险敞口评估

### Phase 5: Output

```
v1.0 | [DATE] | ESG Research
═══════════════════════════════════════════════

## 研究问题

## ESG 评分发现
| 公司/行业 | ESG评分 | 来源 | 日期 |
|---------|--------|------|------|

## 碳市场发现
| 市场 | 碳价 | 日期 | YoY变化 |
|------|------|------|---------|
| EU ETS | ... | ... | ... |

## 绿色债券发现

## 气候风险发现

## 矛盾证据
⚠️ CONTRADICTION: [描述 + 来源]

## 结论
[结构化 + 数据支撑]
```

---

## Integration Contract

| 组件 | 规范 |
|------|------|
| Activation predicate | task 含"ESG"/"碳"/"绿色"/"可持续"/"climate"/"carbon" → fire |
| Canonical helper | 每个 sub-topic 独立数据获取 |
| Failure policy | ESG：失败 → 降级到公开报告 |
| Output artifact | `ESG_REPORT.md` → `~/finance_data/research/esg/` |
| Verifier | 数据附来源 + 日期 |
| Version pin | `esg-research v1.0 | 2026-05-18` |

---

## Skill Metadata

| Field | Value |
|-------|-------|
| Version | v1.0 |
| Author | GARCH QUANT |
| Updated | 2026-05-18 |
| Category | research |
| Based on | ARIS finance-lit + ESG data sources |
