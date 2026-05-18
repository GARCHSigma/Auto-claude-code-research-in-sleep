---
name: garch-survey
description: "GARCH 族模型专项调研 skill。覆盖 GARCH/GJR-GARCH/EGARCH/IGARCH/FIGARCH/Component-GARCH 等变体，以及 t-Copula/Skew-t 等高阶分布。用于波动率建模、条件VaR、尾部风险分析。Use when user says 'GARCH调研', 'GARCH波动率', 'GARCH模型', 'GARCH族', 'volatility modeling', 'GARCH survey'."
argument-hint: "[topic] — sub: garch-family | high-order | copula | trading | risk (default: all)"
allowed-tools: Bash, Read, Write, Glob, WebSearch, WebFetch, terminal
category: research
---

# GARCH Model Survey

Research topic: **$ARGUMENTS**

## Constants

| Constant | Default | Description |
|----------|---------|-------------|
| `OUTPUT_DIR` | `~/finance_data/research/garch/` | 输出目录 |
| `PAPER_LIBRARY` | `~/finance_data/papers/` | 论文库 |
| `SUB_TOPICS` | `all` | garch-family / high-order / copula / trading / risk |
| `CHART_LANG` | `en` | 图表标签英文 |
| `PUSH_TARGET` | `telegram:-1003786012521` | 推送目标 |

---

## Sub-Topic Mapping

| Sub-Topic | 覆盖内容 | 关键文献类型 |
|-----------|---------|-------------|
| `garch-family` | GARCH(1,1) / GJR-GARCH / EGARCH / IGARCH / FIGARCH / Component-GARCH | Bollerslev / Engle / Nelson |
| `high-order` | GARCH(p,q) 阶数选择 / 高阶矩 / 分布假设 (t / Skew-t / GED) | Hansen / Duan |
| `copula` | t-Copula / Clayton / Gumbel / Dynamic Copula | Patton / Joe |
| `trading` | 波动率交易 / Volatility smile / VIX / 波动率择时 | Carr / Madan |
| `risk` | VaR / CVaR / ES / 条件风险 / 极值理论 | Artzner / McNeil |

---

## Data Sources

### Academic Sources

**arXiv (q-fin):**
```
GARCH volatility forecasting
GARCH option pricing
GARCH copula
GJR-GARCH
EGARCH
Component GARCH
t-Copula volatility
```

**Semantic Scholar (JF / JFQA / RFS / MS / Econometrica):**
```bash
curl -s "https://api.semanticscholar.org/graph/v1/paper/search?query=[TOPIC]&fields=title,abstract,year,venue,citationCount&limit=15"
```

**NBER / SSRN:**
```bash
WebSearch: site:ssrn.com GARCH volatility
WebSearch: site:nber.org GARCH
```

### Key Papers to Reference

| 论文 | 作者 | 年份 | 贡献 |
|------|------|------|------|
| ARCH模型 | Engle | 1982 | 诺贝尔奖基础 |
| GARCH | Bollerslev | 1986 | 标准GARCH(1,1) |
| GJR-GARCH | Glosten et al. | 1993 | 非对称GARCH |
| EGARCH | Nelson | 1991 | 指数GARCH |
| Component GARCH | Ding et al. | 1993 | 永久/暂时波动率分解 |
| FIGARCH | Baillie et al. | 1996 | 分数阶积分GARCH |
| GARCH-t | Bollerslev | 1987 | GARCH with t-dist |
| Copula-GARCH | Patton | 2006 | 条件相关Copula |

---

## Workflow

### Phase 1: Parse Arguments

解析：
- Research topic
- `— sub:` 子主题（可组合：garch-family,high-order,copula,trading,risk）
- `— limit:` 结果数（默认 15）

### Phase 2: Multi-Source Query

必须覆盖的来源：
- [ ] arXiv q-fin（GARCH 波动率/期权定价）
- [ ] Semantic Scholar（顶会期刊）
- [ ] NBER / SSRN（工作论文）
- [ ] 市场数据验证（如果涉及实证）

### Phase 3: GARCH Family Analysis

对每个模型变体：

```
模型名称
  数学形式: [公式]
  适用场景: [条件]
  优点: [列举]
  缺点: [列举]
  实证文献: [paper + 结果]
```

### Phase 4: Research Gap Identification

识别：
- [ ] 理论空白（如：高阶矩的解析解）
- [ ] 实证空白（如：商品期货的 Component-GARCH）
- [ ] 方法论空白（如：GARCH + 机器学习）

### Phase 5: Output

```
v1.0 | [DATE] | GARCH Model Survey
═══════════════════════════════════════════════

## 研究问题
[精炼陈述]

## GARCH 族模型概览

### GARCH(1,1)
- 数学形式: r_t = σ_t ε_t, σ²_t = ω + αε²_{t-1} + βσ²_{t-1}
- 适用场景: ...
- 代表文献: Bollerslev (1986)
- 实证结果: ...

[重复其他模型变体...]

## 高阶分布

### t分布
### Skew-t
### GED

## Copula 扩展

## 波动率交易

## 风险度量 (VaR/CVaR)

## 矛盾证据
⚠️ CONTRADICTION: [描述 + 来源]

## 研究空白
| 空白类型 | 具体描述 | 潜在贡献 |
|---------|---------|---------|
| 理论 | ... | ... |
| 实证 | ... | ... |

## 结论与建议
[结构化 + 数据支撑]

## 附录
- 关键论文清单
- 数据源
- 工具调用数
```

---

## Integration Contract

| 组件 | 规范 |
|------|------|
| Activation predicate | task 含"GARCH"/"波动率"/"volatility"/"风险建模" → fire |
| Canonical helper | 每个子主题独立调用路径 |
| Failure policy A | 学术论文：失败 → 降级到 WebSearch |
| Failure policy B | 市场数据：失败 → 标注 ⚠️ DATA UNAVAILABLE |
| Output artifact | `GARCH_SURVEY.md` → `~/finance_data/research/garch/` |
| Verifier | 每条结论附 `[source: DOI/URL + year]`，无验证标注"推测" |
| Version pin | `garch-survey v1.0 | 2026-05-18` |

---

## Skill Metadata

| Field | Value |
|-------|-------|
| Version | v1.0 |
| Author | GARCH QUANT |
| Updated | 2026-05-18 |
| Category | research |
| Based on | ARIS research-lit + finance-lit |
