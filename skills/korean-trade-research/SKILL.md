---
name: korean-trade-research
description: "韩国贸易数据与半导体周期研究 skill。追踪韩国出口数据（半导体/显示器/汽车）、KITA 贸易统计、韩国央行数据、BOK 利率决策。Use when user says '韩国贸易', '韩国半导体', 'Korea trade', 'Korean semiconductor', 'KITA', 'BOK research', '韩国出口数据'."
argument-hint: "[topic] — sub: trade | semiconductor | rates | all (default: all)"
allowed-tools: Bash, Read, Write, Glob, WebSearch, WebFetch, terminal
category: research
---

# Korean Trade & Semiconductor Research

Research topic: **$ARGUMENTS**

## Constants

| Constant | Default | Description |
|----------|---------|-------------|
| `OUTPUT_DIR` | `~/finance_data/research/korea/` | 输出目录 |
| `DATA_DIR` | `~/finance_data/market/korea/` | 韩国数据缓存 |
| `SUB_TOPICS` | `all` | trade / semiconductor / rates / all |
| `PUSH_TARGET` | `telegram:-1003786012521` | 推送目标 |

---

## Sub-Topic Coverage

| Sub | 数据范围 | 数据源 |
|-----|---------|--------|
| `trade` | 进出口统计 | KITA, Korean Customs, K-stat |
| `semiconductor` | 半导体出口/设备 | KITA, SEMES, SIA |
| `rates` | BOK 利率/汇率 | BOK, FRED (USD/KRW) |

---

## Data Sources

### KITA (Korean International Trade Association)

```bash
# KITA 贸易统计
WebSearch: site:kita.net Korea export import statistics
WebFetch: https://tradedata.kita.net/

# 半导体出口数据
WebSearch: site:kita.net semiconductor export
```

### Korean Customs Service

```bash
WebSearch: site:customs.go.kr trade statistics
WebFetch: https://www.customs.go.kr/english/
```

### BOK (Bank of Korea)

```bash
# 经济统计
WebSearch: site:bok.or.kr economic statistics
WebFetch: https://www.bok.or.kr/eng/esronb/...

# 利率决策
WebSearch: site:bok.or.kr base rate policy rate decision
```

### USD/KRW 汇率

```bash
# Twelve Data
curl -s "https://api.twelvedata.com/time_series?symbol=USD/KRW&interval=1day&apikey=${TWELVE_DATA_KEY}&format=JSON"

# Alpha Vantage
curl -s "https://www.alphavantage.co/query?function=FX_DAILY&from_symbol=USD&to_symbol=KRW&apikey=${ALPHA_VANTAGE_KEY}&outputsize=compact"
```

### SIA (Semiconductor Industry Association) - 全球对比

```bash
WebSearch: site:semiconductors.org global semiconductor sales
```

---

## Workflow

### Phase 1: Parse Arguments

- Topic
- `— sub:` trade / semiconductor / rates / all（默认 all）

### Phase 2: Source Coverage

必须覆盖：
- [ ] KITA 贸易数据
- [ ] BOK 经济数据
- [ ] USD/KRW 汇率（市场数据源）

### Phase 3: Data Collection

```bash
# 韩国出口数据
WebSearch: KITA Korea exports [year] semiconductor chip

# 半导体周期
WebSearch: Korea semiconductor export cycle [year]

# BOK 利率
WebSearch: BOK base rate [year]
```

### Phase 4: Analysis

- [ ] 韩国出口增速 vs 全球半导体周期
- [ ] 半导体出口占比趋势
- [ ] BOK 利率与韩元汇率相关性
- [ ] 作为全球贸易领先指标的有效性

### Phase 5: Output

```
v1.0 | [DATE] | Korean Trade Research
═══════════════════════════════════════════════

## 研究问题

## 韩国出口数据
| 品类 | 出口额 | YoY | 占比 | 日期 |
|------|--------|-----|------|------|

## 半导体出口分析
| 指标 | 最新值 | 历史均值 | 周期位置 |

## 汇率与利率
| 指标 | 最新值 | 日期 | 趋势 |

## 作为领先指标
[分析韩国出口数据对全球/中国经济的领先性]

## 矛盾证据
⚠️ CONTRADICTION: [描述 + 来源]

## 结论
[结构化 + 数据支撑]
```

---

## Integration Contract

| 组件 | 规范 |
|------|------|
| Activation predicate | task 含"韩国"/"Korea"/"韩元"/"KITA"/"BOK"/"半导体出口" → fire |
| Canonical helper | 每个 sub-topic 独立数据获取 |
| Failure policy | KITA：失败 → 降级到 WebSearch / Korean Customs |
| Output artifact | `KOREA_TRADE_REPORT.md` → `~/finance_data/research/korea/` |
| Verifier | 数据附来源 + 日期 |
| Version pin | `korean-trade-research v1.0 | 2026-05-18` |

---

## Skill Metadata

| Field | Value |
|-------|-------|
| Version | v1.0 |
| Author | GARCH QUANT |
| Updated | 2026-05-18 |
| Category | research |
| Based on | ARIS finance-lit + Korean trade sources |
