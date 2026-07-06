---
name: faith-kb-scan
description: "Use when the user asks for knowledge base health check — detects duplicates, orphans, broken cross-references, and stale INDEX.md entries. Not for single-article editing (dbs-content) or skill evaluation (faith-skill-solution)."
disable-model-invocation: true
argument-hint: "[directory-path]"
version: "2.0.0 | R2: Matt Pocock 7铁律对齐 | 2026-07-06 | model: agnostic"
---

# faith-kb-scan — 知识库健康巡检

> **audit** 只读不写——检测问题，输出修复清单。不自动删除/移动文件。修复需人工确认。

根目录：`D:\KnowledgeBase\`。INDEX.md：`D:\KnowledgeBase\INDEX.md`。
排除：`.git`、`node_modules`、`_logs/`。

## 巡检流程（5步，每步有完成标准）

### 步骤1: 结构扫描

统计文件总数，验证 PARA 结构（0_Inbox/01_Projects/02_Areas/03_Resources/04_Archive）和核心目录（cards/media/_content-system/）是否存在。
**完成标准**: 输出文件总数 + 目录结构完整性报告（每目录存在与否 + 文件数）。

### 步骤2: 去重检测

同名文件：全库搜索同名 .md，标记重复路径。
标题去重：提取每个文件第一个 `# ` 标题行，标记相同标题。
跨目录重叠：`_alchemist/` vs `media/` 下的同名/同标题文件。
**完成标准**: 输出去重报告——重复组数 + 每组路径列表 + 建议保留/删除判定。

### 步骤3: 孤点检测

提取每个 .md 文件名，搜索全库是否有其他文件引用 `[[文件名]]`。未被引用的文件标记为孤点。
排除：`_logs/`、`.gitignore`、frontmatter 文件。
**完成标准**: 输出孤点文件清单（路径 + 大小 + 最后修改日期）。

### 步骤4: INDEX.md 一致性

读取 INDEX.md，对比实际目录结构。标记：过期引用（INDEX有但目录无）、缺失条目（目录有但INDEX无）、数字过期。
**完成标准**: 输出 INDEX 差异报告——过期引用数 + 缺失条目数 + 建议更新项。

### 步骤5: 交叉引用断裂

搜索所有 `[[文件名]]` 引用，检查目标文件是否存在。不存在则标记为"断裂引用"。
排除外部 URL。
**完成标准**: 输出断裂引用清单（源文件路径 → 断裂目标 + 建议修复）。

## 输出报告模板

```
# 知识库健康巡检报告
扫描：[时间] | 根：D:\KnowledgeBase\ | 文件数：N

## 结构概览 | 去重候选项 | 孤点文件 | INDEX一致性 | 断裂引用
## 修复优先级（Top 10）
1. [问题描述] → [建议操作]
...
```

## 约束

只读不写（不自动删除/移动）。修复需人工确认。

## 失败模式

| 失败 | 动作 |
|------|------|
| KB 根目录不可达 | 输出原因，中止 |
| rg 不可用 | 降级为 PowerShell Select-String |
| INDEX.md 不存在 | 输出缺失，继续其他巡检 |

---
*version: 2.0.0 | principle: audit-detect-report | ref: Matt Pocock 7 Principles*