---
name: macro-research
description: "宏观经济研究 skill。覆盖利率、汇率、信用利差、货币供应、全球宏观周期。接入 FRED / PBOC / BOK / BIS / IMF / WorldBank 数据。Use when user says '宏观研究', '利率分析', '汇率研究', '信用利差', '货币供应', 'macro research', 'macro economics'."
argument-hint: "[topic] — indicators: rates | fx | credit | money | all (default: all)"
allowed-tools: Bash, Read, Write, Glob, WebSearch, WebFetch, terminal
category: research
---

# Macro Economics Research

Research topic: **$ARGUMENTS**

## Constants

| Constant | Default | Description |
|----------|---------|-------------|
| `OUTPUT_DIR` | `~/finance_data/research/macro/` | 输出目录 |
| `DATA_DIR` | `~/finance_data/market/macro/` | 宏观数据缓存 |
| `INDICATORS` | `all` | rates / fx / credit / money / all |
| `PUSH_TARGET` | `telegram:-1003786012521` | 推送目标 |

---

## Indicator Coverage

| Indicator | 数据源 | 典型指标 |
|-----------|--------|---------|
| **利率** | FRED / PBOC / BOK | DFF (Fed Funds) / LPR / 政策利率 |
| **汇率** | BIS / PBOC / FRED | DXY / USD/CNY / KRW |
| **信用利差** | FRED / Bloomberg | IG / HY spreads, OAS |
| **货币供应** | PBOC / FRED / BIS | M2 / M0 / 社融 |
| **全球周期** | IMF / WorldBank / OECD | PMI, GDP growth, CLI |

---

## Data Sources

### FRED (Federal Reserve Economic Data)

```bash
# 关键序列 ID
# DFF: Fed Funds Rate
# DGS10: 10Y Treasury
# DGS2: 2Y Treasury
# T10Y2Y: 10Y-2Y Spread
# DAAA: BAA Corporate
# BAMLEMU: EM spreads

curl -s "https://api.stlouisfed.org/fred/series/observations?series_id=DFF&limit=30&api_key=${FRED_API_KEY}&file_type=json"
```

### PBOC (中国人民银行)

```bash
# 贷款市场报价利率 (LPR)
WebSearch: site:pbc.gov.cn LPR 贷款市场报价利率
WebFetch: https://www.pbc.gov.cn/ewebeditor/uploadfile/2024/...

# 社融数据
WebSearch: site:pbc.gov.cn 社会融资规模
```

### BOK (韩国银行)

```bash
# 基准利率
WebSearch: site:bok.or.kr base rate policy interest rate
# 贸易数据
WebSearch: site:bok.or.kr trade balance semiconductor export
```

### BIS (Bank for International Settlements)

```bash
# BIS Statistics
WebSearch: site:bis.org effective exchange rate
WebSearch: site:bis.org credit to non-financial sector
```

### IMF / WorldBank

```bash
WebSearch: site:imf.org World Economic Outlook [topic]
WebSearch: site:worldbank.org GDP growth [country]
```

---

## Workflow

### Phase 1: Parse Arguments

- Topic
- `— indicators:` rates / fx / credit / money / all（默认 all）

### Phase 2: Indicator Mapping

确定需要哪些指标，建立数据获取计划：

```
利率驱动 → DFF, DGS10, DGS2, T10Y2Y
汇率驱动 → DXY, USD/CNY, USD/KRW
信用驱动 → DAAA, BAMLEMU
货币驱动 → M2, 社融规模
```

### Phase 3: Data Collection

必须覆盖至少 2 个来源类别：
- [ ] FRED (或 BIS)
- [ ] PBOC (或 BOK)
- [ ] IMF / WorldBank

### Phase 4: Analysis

- [ ] 计算利差（名义/实际）
- [ ] 识别周期位置（扩张/收缩）
- [ ] 跨国比较
- [ ] 预测信号提取

### Phase 5: Output

```
v1.0 | [DATE] | Macro Research
═══════════════════════════════════════════════

## 研究问题

## 利率分析
| 指标 | 最新值 | 日期 | 趋势 |
|------|--------|------|------|

## 汇率分析
| 货币对 | 最新值 | 日期 | 趋势 |
|--------|--------|------|------|

## 信用利差
| 利差类型 | 最新值 | 历史分位 | 信号 |

## 货币供应
| 指标 | 最新值 | YoY | 信号 |

## 全球周期位置
[基于 OECD CLI / IMF GDP]

## 矛盾证据
⚠️ CONTRADICTION: [描述 + 来源]

## 结论
[结构化 + 数据支撑]
```

---

## Integration Contract

| 组件 | 规范 |
|------|------|
| Activation predicate | task 含"宏观"/"利率"/"汇率"/"信用利差"/"货币"/"macro" → fire |
| Canonical helper | 每个 indicator 独立数据获取路径 |
| Failure policy A | FRED：失败 → 降级到 BIS |
| Failure policy B | PBOC：失败 → WebSearch 替代 |
| Output artifact | `MACRO_REPORT.md` → `~/finance_data/research/macro/` |
| Verifier | 数据附来源 + 日期 |
| Version pin | `macro-research v1.0 | 2026-05-18` |

---

## Skill Metadata

| Field | Value |
|-------|-------|
| Version | v1.0 |
| Author | GARCH QUANT |
| Updated | 2026-05-18 |
| Category | research |
| Based on | ARIS finance-lit + macro data sources |
