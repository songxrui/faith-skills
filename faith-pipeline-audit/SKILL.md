---
name: faith-pipeline-audit
description: "Use when a faith-pipeline run has completed and you need a post-mortem — measures per-phase duration, CJK coverage, card utilization, and identifies bottlenecks."
disable-model-invocation: true
argument-hint: "[output-directory]"
version: "2.0.0 | R2: Matt Pocock 7铁律对齐 | 2026-07-06 | model: agnostic"
---

# faith-pipeline-audit — 内容流水线事后审计

> **audit** 回顾性统计分析——过去一段时间的产出率、达标率、瓶颈定位。
> 不是前瞻性门禁（那是 faith-gate-check 的职责）。

## 审计维度（5 维）

| # | 维度 | 计算方式 |
|---|------|---------|
| M1 | 产出总量 | 指定目录下 .md 文件数 |
| M2 | CJK 达标率 | CJK 字符 ≥3000 的篇数 / 总篇数 |
| M3 | 卡片覆盖率 | 文章中引用的卡片 ID 去重数 / 卡片目录总数 |
| M4 | 发布频率 | 按文件修改时间分组到日，计算日均产出、最长断更天数 |
| M5 | 瓶颈定位 | 标记 CJK<2500 的文章为需补充，未覆盖卡为待产出 |

## 执行流程（5步，每步有完成标准）

### 步骤1: 环境验证

确认内容目录和卡片目录可访问。
**完成标准**: 输出可访问性报告——内容目录路径 + 卡片目录路径 + 各自文件数 + 是否可读。

### 步骤2: CJK 扫描

逐篇统计 CJK 字符数。
**完成标准**: 输出达标/未达标列表——文件名 | CJK字符数 | 状态(PASS/FAIL) + 达标率百分比。

### 步骤3: 卡片覆盖率

提取每篇文章引用的卡片 ID。
**完成标准**: 输出覆盖率百分比 + 未覆盖卡片ID列表 + 高频引用卡片Top5。

### 步骤4: 发布频率

按文件修改时间分组统计。
**完成标准**: 输出日均产出 + 最长断更天数 + 发布热力图（按周汇总）。

### 步骤5: 输出仪表盘

汇总所有维度。
**完成标准**: 五维数据一览表 + 瓶颈诊断(1-2个瓶颈，每个配1条修复建议)。

## 门禁

- [ ] 内容目录存在
- [ ] CJK 达标率已计算
- [ ] 卡片覆盖率已计算
- [ ] 瓶颈诊断已完成

## 与 faith-gate-check 的边界

| 场景 | 用哪个 |
|------|--------|
| 单篇内容发之前，能发吗？ | faith-gate-check（前瞻性门禁） |
| 过去一个月，整体产出质量如何？ | faith-pipeline-audit（回顾性审计） |

## 失败模式

| 失败 | 兜底 |
|------|------|
| 内容目录不存在 | 输出原因，中止 |
| 无 .md 文件 | 输出"目录无文章"，中止 |
| 卡片目录不可达 | 跳过 M3，提示"卡片目录未找到" |

---
*version: 2.0.0 | principle: audit-measure-bottleneck | ref: Matt Pocock 7 Principles*