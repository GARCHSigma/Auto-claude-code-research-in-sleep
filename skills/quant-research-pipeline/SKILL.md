---
name: quant-research-pipeline
description: "量化研究全流程 pipeline skill。从选题调研 → 文献综述 → 方向生成 → novelty验证 → 实验 → 论文写作的完整生命周期。调用 deep-research-protocol / finance-lit / idea-discovery / novelty-check 等 skill。Use when user says '量化研究全流程', 'quant research pipeline', '从选题到论文', 'end-to-end quant research'."
argument-hint: "[research-topic] — type: survey | review | systematic (default: survey)"
allowed-tools: Bash, Read, Write, Glob, WebSearch, WebFetch, terminal, Skill, Agent, session_search
category: research
---

# Quant Research Pipeline

Research topic: **$ARGUMENTS**

## Constants

| Constant | Default | Description |
|----------|---------|-------------|
| `RESEARCH_DIR` | `~/finance_data/research/` | 研究根目录 |
| `OUTPUT_DIR` | `auto` | 自动创建 `research_dir/[topic]/` |
| `PUSH_TARGET` | `telegram:-1003786012521` | 推送目标 |
| `CHART_LANG` | `en` | 图表英文标签 |
| `AUTO_PROCEED` | `true` | 自动进入下一阶段 |
| `REVIEWER_MODEL` | `gpt-5.5` | 评审模型 |

---

## Pipeline Overview

```
Phase 1: 深度调研
  /deep-research-protocol — type: [survey|review|systematic]
       → SURVEY_REPORT.md

Phase 2: 文献综述
  /finance-lit — sources: academic,market,macro
       → FINANCE_SURVEY.md

Phase 3: 创意生成
  /idea-discovery — 读取 SURVEY_REPORT.md
       → IDEA_REPORT.md

Phase 4: Novelty 验证
  /novelty-check — 验证 idea 新颖性
       → NOVELTY_REPORT.md

Phase 5: 实验执行 (可选)
  /run-experiment — 执行实验
       → EXPERIMENT_LOG.md

Phase 6: 论文写作 (可选)
  /paper-writing — 生成论文
       → paper/main.tex / main.pdf
```

---

## Phase 1: Deep Research

执行 `/deep-research-protocol`：

```
/deep-research-protocol "$ARGUMENTS" — type: survey
```

**输出:** `SURVEY_REPORT.md`（含矛盾证据、数据缺口、primary sources）

**Gate:** 检查 checklist 全部通过 + 工具调用数达标

---

## Phase 2: Finance Literature Survey

执行 `/finance-lit`：

```
/finance-lit "$ARGUMENTS" — sources: academic,market,macro
```

**输出:** `FINANCE_SURVEY.md`（3+类别数据源覆盖）

**与 Phase 1 合并去重：**
- 合并两个 survey 的 sources
- 去重重复论文/数据
- 补充矛盾证据

---

## Phase 3: Idea Generation

基于 Phase 1+2 的输出，执行 idea 生成：

```
从 SURVEY_REPORT.md 和 FINANCE_SURVEY.md 提取：
  - 研究空白
  - 矛盾证据（潜在突破口）
  - 实证差距
  ↓
生成 3-5 个候选 idea
  ↓
每个 idea 包含：
  - 标题
  - 假设
  - 潜在贡献
  - 所需数据
  - 风险（被现有方法碾压的可能性）
  ↓
输出 IDEA_REPORT.md
```

**Gate:** 呈现 top ideas，等待用户确认（若 AUTO_PROCEED=false）

---

## Phase 4: Novelty Check

对 Phase 3 确认的 idea 执行 novelty 验证：

```
检查：
  1. 是否已有类似工作（literature coverage）
  2. 方法论是否与现有工作有本质区别
  3. 实证证据是否充分
  
输出 NOVELTY_REPORT.md：
  - NOVELTY: CONFIRMED / WEAK / REJECTED
  - 理由
  - 改进建议
```

**Gate:** 如果 NOVELTY: REJECTED → 返回 Phase 3 重新生成

---

## Phase 5: Experiment Execution (Optional)

```
根据 IDEA_REPORT.md 执行实验：
  - 数据获取（Tushare / Baostock / FRED）
  - 模型实现（GARCH族 / LightGBM / Copula）
  - 回测验证
  ↓
记录 EXPERIMENT_LOG.md
  - 超参数
  - 中间结果
  - 最终结论
```

---

## Phase 6: Paper Writing (Optional)

```
/paper-writing "EXPERIMENT_LOG.md + IDEA_REPORT.md"
     ↓
paper/main.tex + main.pdf
```

---

## Output Structure

```
~/finance_data/research/[topic]/
├── SURVEY_REPORT.md          # Phase 1 输出
├── FINANCE_SURVEY.md         # Phase 2 输出
├── IDEA_REPORT.md            # Phase 3 输出
├── NOVELTY_REPORT.md         # Phase 4 输出
├── EXPERIMENT_LOG.md         # Phase 5 输出
└── paper/
    ├── main.tex              # Phase 6 输出
    └── main.pdf
```

---

## Integration Contract

| 组件 | 规范 |
|------|------|
| Activation predicate | task 含"量化研究"/"quant"/"全流程"/"pipeline"/"从选题" → fire |
| Canonical helper | 每个 Phase 是独立 Skill 调用 |
| Failure policy | Phase 失败 → 标注后继续或终止（取决于 Gate） |
| Output artifact | 见上方 Output Structure |
| Verifier | 每个 Phase 有 Gate 检查，不通过不进入下一 Phase |
| Version pin | `quant-research-pipeline v1.0 | 2026-05-18` |

---

## Quick Reference

```
/quant-research-pipeline "商品期货动量 + GARCH波动率" — type: survey
/quant-research-pipeline "A股因子选股" — type: review
/quant-research-pipeline "skewness premium" — type: systematic
```

---

## Skill Metadata

| Field | Value |
|-------|-------|
| Version | v1.0 |
| Author | GARCH QUANT |
| Updated | 2026-05-18 |
| Category | research |
| Based on | ARIS research-pipeline + deep-research-protocol + finance-lit |
