---
name: faith-session
description: "Use when the user says save/archive/record state or continue/resume previous session — preserves key conclusions as structured text (no filesystem dependency) and reconstructs context from pasted archives."
disable-model-invocation: true
argument-hint: "[save-or-resume]"
version: "2.0.0 | R2: Matt Pocock 7铁律对齐 | 2026-07-06 | model: agnostic"
---

# faith-session — 对话状态保存与恢复

> **checkpoint** 不依赖文件系统。保存时输出结构化文本（用户可自行存储），恢复时由用户粘贴存档内容。

## 触发条件

- **适用场景**: 对话中产出了有价值的结论需要保存供下次参考、想接着上次继续、一个 session 太长需要分段
- **不适用场景**: 对话长度 <10 条消息 → 不需要存档；用户明确拒绝存档 → 不存档；还没得到结论 → 先执行再存
- **正例**: "把这次结论存下来" → 保存模式；"接着上次继续" → 恢复模式
- **反例**: "帮我分析一下" → 先分析再存

## 执行流程（2模式，各有完成标准）

### 模式A: 保存（3步）

#### 步骤1: 提取关键信息

从当前对话中提取：核心结论(3-5条)、已排除的方向、推荐的下一步。
**完成标准**: 输出提取清单，每条结论一行，标注"结论/排除/下一步"分类。

#### 步骤2: 格式化输出

输出结构化存档文本：
```
=== faith-session 存档 ===
日期：[YYYY-MM-DD]
主题：[一句话概括]

结论：
1. ...
2. ...

已排除：
- ...

推荐下一步：
- ...

--- 存档结束，可复制保存 ---
```
**完成标准**: 三种分类齐全，每条结论可独立理解（不依赖对话上下文）。

#### 步骤3: 提示保存

提示用户复制保存。不写入任何文件。
**完成标准**: 用户收到明确提示"请复制以上存档内容保存到你的笔记中"。

### 模式B: 恢复（2步）

#### 步骤1: 要求粘贴存档

要求用户粘贴之前保存的存档内容。
**完成标准**: 用户已粘贴可解析的存档文本。

#### 步骤2: 重建上下文

根据存档内容重建上下文：确认结论、确认排除方向、继续下一步。
**完成标准**: 输出：
```
已加载存档（[日期]）
主题：[主题]
待继续：[推荐下一步]
```

## 自检

- 保存前：对话中是否有至少 1 个可陈述的结论？无则不存
- 恢复前：用户是否提供了可解析的存档文本？无则要求提供

## 失败模式

| 失败 | 兜底 |
|------|------|
| 对话中无有效结论 | 提示"当前对话尚未产出可存档的结论，继续对话后再保存" |
| 用户粘贴的内容无法解析 | 提示"无法识别存档格式，请确认粘贴的是 faith-session 存档内容" |

---
*version: 2.0.0 | principle: checkpoint-save-restore | ref: Matt Pocock 7 Principles*