---
name: faith-skill-architect
description: "Triage skill requests into design/review/optimize modes, then build skills with a fixed 6-layer structure. Use when the user says \`"设计一个skill\`", \`"评审这个skill\`", \`"优化我的skill\`", or needs a new skill architected from scratch. Not for deployment (→ faith-skill-deploy) or evaluation-only (→ faith-skill-solution)."
disable-model-invocation: true
argument-hint: "[skill-name-or-goal]"
version: "2.1.0 | R2.1: mattpocock description alignment | 2026-07-06 | model: agnostic"
---

# faith-skill-architect -- Skill架构引擎

> **triage** 用户意图到A/B/C三种模式，然后按固定结构搭建或评审skill。

## 模式判定

`
设计新skill → 模式A
评审已有skill → 模式B
优化已有skill → 模式C
无法判定 → 反问：你要设计新的、评审已有的还是优化现有的？
`

## 模式A: 设计新Skill

### 步骤1: 需求澄清（不确定时先问）
**完成标准**: 确认3个关键信息后才动手：
1. 核心功能（一句话）
2. 输入是什么？输出是什么？
3. 什么情况下不应该触发？

### 步骤2: 搭建SKILL.md（6层结构）

| 层 | 名称 | 内容 |
|----|------|------|
| L1 | frontmatter | name + description（嵌入触发词+不适用+正反例）+ version |
| L2 | 触发层 | 适用场景 + 触发关键词 |
| L3 | 执行层 | 编号步骤(1. 2. 3.)，每步带完成标准 |
| L4 | 验证层 | checkbox清单([] 格式)，至少3项 |
| L5 | 兜底层 | 表格，至少2行失败模式+兜底动作 |
| L6 | 门禁层 | description含至少3个触发词、不适用场景、正反例；文件大小不超过10KB |

**完成标准**: 输出完整的SKILL.md，包含L1-L6全部6层。引用型内容（规则/清单/分支材料）放入子目录，主文件保留指针。

### 步骤3: 自检
- [ ] 每步有可判定的完成标准（不是"分析清楚"而是"输出包含X,Y,Z三个字段"）
- [ ] description是if-else触发器，不是产品简介
- [ ] 文件大小不超过10KB
- [ ] 有至少2个失败模式+兜底

## 模式B: 评审已有Skill

### 步骤1: 读取
read_file 读取目标 SKILL.md。

### 步骤2: 6层审计

| 层 | 检查项 | 判定 |
|----|-------|------|
| L1 | frontmatter存在？description含触发词+不适用+正反例？ | 通过/失败 |
| L2 | 触发条件清晰？有正反例？ | 通过/失败 |
| L3 | 步骤带完成标准（可验证的输出要求）？ | 通过/失败 |
| L4 | 验证清单至少3项？ | 通过/失败 |
| L5 | 失败模式至少2条？ | 通过/失败 |
| L6 | 文件小于10KB？触发词至少3个？ | 通过/失败 |

**完成标准**: 输出6项评分表 + 总分 + 修复优先级排序。

## 模式C: 优化已有Skill

### 步骤1: 先走模式B评审
### 步骤2: 按优先级逐项修复
### 步骤3: 输出优化后的SKILL.md + 改动清单

**完成标准**: 改动清单列出每项改动的原因和效果（一句话）。

## 失败模式

| 失败 | 兜底 |
|------|------|
| 用户不给需求 | 反问3个核心问题后再动手 |
| 与faith-skill-solution冲突 | 已有skill的评审/优化走faith-skill-solution，新设计走这里 |
| 输出SKILL.md超过10KB | 拆参考内容到references子目录 |

---
*version: 2.0.0 | principle: triage-design-audit | ref: Matt Pocock 7 Principles*