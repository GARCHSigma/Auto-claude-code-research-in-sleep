---
name: deep-research-protocol
description: "GARCH QUANT 深度研究执行协议 — Survey/Review 级别调研任务的防早停规则包。自动对标 min_tool_calls 门槛，未达标不得输出结论。以 ARIS skill 格式编写，支持与 ARIS 生态集成。"
argument-hint: "[research-topic] — type: quick | survey | review | systematic"
allowed-tools: Bash(*), Read, Write, Edit, Grep, Glob, WebSearch, WebFetch, Agent, Skill, session_search
category: research
---

# GARCH QUANT Deep Research Protocol v2.0

> **Format:** ARIS-compatible SKILL.md | **Based on:** ARIS integration-contract principles  
> **Version:** v2.0 | **Updated:** 2026-05-18

---

## Constants

| Constant | Default | Description |
|----------|---------|-------------|
| `MIN_TOOL_CALLS` | auto | 由任务类型决定，见下表 |
| `OUTPUT_DIR` | `~/finance_data/research/` | 研究输出目录 |
| `PRIMARY_SOURCE_REQUIRED` | `true` | 结论必须标注来源 |
| `CONTRADICTION_REPORT` | `true` | 必须标注矛盾证据 |
| `CHART_LANG` | `en` | 图表标题/轴标签/图例一律英文 |
| `PUSH_TARGET` | `telegram:-1003786012521` | TG论文推送频道 |

---

## Task Type Detection

### Trigger Keywords (英文优先，高门槛覆盖低门槛)

| Task Type | English Keywords | 中文等效 | min_tool_calls |
|-----------|-----------------|----------|---------------|
| `quick` | `quick`, `find`, `lookup` | 快速查找 | **10** |
| `survey` | `survey`, `research`, `investigate`, `调研`, `分析` | 调研综述 | **30** |
| `review` | `review`, `analyze`, `evaluate`, `深入`, `评审` | 深度评审 | **60** |
| `systematic` | `systematic`, `comprehensive`, `系统`, `全面` | 系统评审 | **100+** |

### Priority Rules

1. **英文关键词优先**：出现上表英文关键词 → 直接触发对应门槛
2. **多关键词共存**：以最高门槛为准（Systematic > Review > Survey > Quick）
3. **无关键词** → 默认为 `quick`（10 门槛），除非任务明显涉及多数据分析
4. **强制指定**：`— type: review` 参数可覆盖自动检测
5. **混合语言**：中文任务先转英文语义再匹配关键词

### Argument Format

```
/deep-research-protocol "GARCH模型在商品期货中的应用" — type: survey
/deep-research-protocol "利率期限结构研究" — type: review
/deep-research-protocol "quick: 查找SK Hynix波动率数据"
```

---

## Anti-Premature-Conclusion Checklist

> 任务结束前必须逐项检查，**全部通过**才能输出结论。任意一项未满足则继续工作。

### ✅ Checklist（6 项全部通过方可输出结论）

1. **✅ 3+ 数据源** — 至少使用了 3 个不同数据源/工具
2. **✅ 具体数值** — 关键数据有具体数值，非泛泛描述
3. **✅ 矛盾标注** — 找到了矛盾证据并明确标注 `⚠️ CONTRADICTION:`
4. **✅ Primary Source** — 核心结论有 primary source 支撑（注明来源、日期、URL）
5. **✅ 结构化输出** — 输出已组织成结构化格式（列表/表格/文件）
6. **✅ 门槛达标** — 当前工具调用数 ≥ `MIN_TOOL_CALLS`

### ⚠️ 数据缺失处理

```
1. 明确标注：⚠️ DATA UNAVAILABLE: [原因]
2. 尝试备用数据源（至少 2 个）
3. 均不可得 → 在 checklist #3（矛盾证据）中说明
```

---

## Pipeline

### Phase 1: Topic Normalization

```
输入任务 instruction
     ↓
提取关键词，判断 type（见 Task Type Detection）
     ↓
设置 MIN_TOOL_CALLS 和 OUTPUT_DIR
```

### Phase 2: Source Discovery

> 每个数据源单独调用，合并计入 tool_calls

必须覆盖的典型数据源（至少 3 个）：
- **市场数据**：Tushare Pro / Alpha Vantage / Twelve Data / Baostock
- **学术来源**：arXiv / Semantic Scholar / DeepXiv / Google Scholar
- **新闻/宏观**：Wind / Bloomberg API / 公开研报
- **专业数据**：Korean_trade_statistics / 彭博 / 路透

### Phase 3: Data Collection

每个调用必须产生**新可验证信息**，禁止重复查询同一数据。

**调用计数策略：**

| 工具 | 计数规则 |
|------|---------|
| `terminal()` | 1 call |
| `read_file()` / `search_files()` | 1 call |
| `browser_navigate()` + `browser_snapshot()` | 合并 1 call |
| `send_message()` | **不计入**（非研究工具） |
| 循环内重复操作 | 合并计数 |

### Phase 4: Analysis & Synthesis

必须完成：
- [ ] 对比不同数据源的结论差异
- [ ] 识别矛盾证据并分类（方向矛盾 / 程度矛盾 / 方法论矛盾）
- [ ] 形成分级结论（强结论 / 弱结论 / 推测）

### Phase 5: Output

**输出规范：**

```
v2.0 | [DATE] | GARCH QUANT Research Protocol
═══════════════════════════════════════════════

## 研究问题
[精炼陈述]

## 关键发现（N 条）
1. [发现] — 来源: [source] | 可信度: [高/中/低]
2. ...

## 矛盾证据
⚠️ CONTRADICTION: [描述 + 来源]
⚠️ CONTRADICTION: ...

## 数据缺口
⚠️ DATA UNAVAILABLE: [描述 + 原因]

## 结论
[结构化结论 + 数据支撑]

## 附录
- 数据源清单（含 URL/API/文件路径）
- 工具调用数: X / MIN_TOOL_CALLS
```

---

## Integration Contract（跨 Skill 协作规范）

### 6 个必需组件（参考 ARIS integration-contract）

| 组件 | 要求 |
|------|------|
| **Activation predicate** | 必须有可观测触发条件（不是 prose 描述） |
| **Canonical helper** | 数据获取逻辑必须在一处，不重复实现 |
| **Failure policy** | 数据源失败必须有明确的 A/B 策略（降级或终止） |
| **Output artifact** | 明确产出文件路径和格式 |
| **Verifier** | 输出必须可验证（不是"看起来对"） |
| **Version pin** | 标注 skill 版本，追踪变更 |

### 与 ARIS 生态集成

本 skill 可作为 ARIS `/research-lit` 的前置补充：

```
/deep-research-protocol "主题" — type: survey
     ↓
产出: research-stage/SURVEY_REPORT.md
     ↓
/idea-discovery (读取 SURVEY_REPORT.md 作为 context)
```

### Artifact Contract

| Artifact | Created by | Consumed by |
|----------|-----------|-------------|
| `SURVEY_REPORT.md` | deep-research-protocol | idea-discovery, research-wiki |
| `CONTRADICTION_LOG.md` | deep-research-protocol | research-review |
| `SOURCE_INVENTORY.md` | deep-research-protocol | any downstream skill |

---

## Pitfalls（已知陷阱）

| # | 陷阱 | 规避方法 |
|---|------|---------|
| P1 | API 限流后降级为合成数据，未标注 | 任何降级数据必须标注 `[DEMO DATA]`，并在结论中说明 |
| P2 | 缓存数据冒充实时数据 | 每个数据文件验证时间戳：`ls -l --time-style=full-iso` |
| P3 | 工具调用数靠浏览器截图刷门槛 | 截图必须附带可验证数值输出（不能是纯描述性截图） |
| P4 | 矛盾证据被忽略或淡化 | 矛盾证据必须在 checklist #3 明确标注，不得合并到其他项 |
| P5 | 结论无数据支撑 | 每条结论必须有 `[source: URL/API/filepath + date]` 标注 |

---

## Quick Reference

```
任务类型         关键词                  门槛      适用场景
──────────────────────────────────────────────────────────────
quick      quick / find / lookup         10      单点查找
survey     survey / research / investigate  30    调研综述
review     review / analyze / evaluate   60      深度评审
systematic systematic / comprehensive   100+    系统评审
──────────────────────────────────────────────────────────────

输出: [OUTPUT_DIR]/SURVEY_REPORT.md
推送: telegram:-1003786012521
图表: 英文标签 | 结论: 数据支撑 | 矛盾: ⚠️ CONTRADICTION 标注
```

---

## Skill Metadata

| Field | Value |
|-------|-------|
| Version | v2.0 |
| Author | GARCH QUANT |
| Updated | 2026-05-18 |
| Format | ARIS-compatible SKILL.md |
| Category | research |
| Based on | ARIS integration-contract, deep-research-protocol v1.0 |
