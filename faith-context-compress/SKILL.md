---
name: faith-context-compress
description: "Use when conversation context exceeds ~50 turns or token budget is strained — compresses to essentials while preserving key decisions, core data, unfinished tasks, and next actions."
disable-model-invocation: true
argument-hint: "[trigger-auto]"
version: "4.0.0 | R4: Matt Pocock 7铁律对齐 | 2026-07-06 | model: agnostic"
---

# faith-context-compress — 上下文压缩

> **compress** 保留决策和未决问题，丢弃已完成执行细节。不是存档（那是 faith-session 的职责）。

## 压缩决策

| 条件 | 行动 |
|------|------|
| 对话 ≥50 轮或上下文明显膨胀 | 提议压缩 |
| 用户刚完成一个独立任务 | 在该任务结束后压缩 |
| 有未解决的开放问题 | 保留不压缩（先完成再压） |

## 执行流程（4步，每步有完成标准）

### 步骤1: 评估

评估当前上下文长度（轮数/token 估算）。
**完成标准**: 输出评估结果——预估轮数 + token使用量 + 是否需要压缩（是/否，附理由）。

### 步骤2: 分类

区分已完成任务 vs 未解决问题。关键决策单独标注。
**完成标准**: 输出分类清单——已完成(N项) + 未解决(N项) + 关键决策(N项)。

### 步骤3: 生成压缩摘要

保留决策+未决问题，丢弃已完成细节。
**完成标准**: 生成压缩后摘要，格式如下：
```
## 上下文检查点 — {会话主题}

### 未解决问题
1. {仍在讨论或未决定的问题}

### 关键决策（不可丢失）
- {决策1 + 理由}
- {决策2 + 理由}

### 已完成（可丢弃细节）
- {主题1}: {1句话结果}
- {主题2}: {1句话结果}
```

### 步骤4: 输出

输出压缩结果并提示用户确认。
**完成标准**: 压缩率 ≥60% token减少 + 无关键信息丢失 + 输出被删除内容清单（仅标题级）。

## 门禁

- [ ] 关键决策完整保留
- [ ] 未解决问题无遗漏
- [ ] 压缩后体积显著减小（≥60%）

## 与 faith-session 的边界

| 场景 | 用哪个 |
|------|--------|
| 对话太长记不住，需要精简 | faith-context-compress |
| 想下次继续当前对话 | 先 compress → 再 session 存档 |
| 完整存档（含执行细节） | faith-session |

## 失败模式

| 失败 | 兜底 |
|------|------|
| 无足够内容压缩 | 跳过，提示"上下文仍短，不需要压缩" |
| 关键信息可能在压缩中丢失 | 标注待确认项，提醒用户检查 |
| 压缩后 token 节省 <60% | 提示"压缩空间有限，建议关闭不需要的文件后再试" |

---
*version: 4.0.0 | principle: compress-classify-preserve | ref: Matt Pocock 7 Principles*