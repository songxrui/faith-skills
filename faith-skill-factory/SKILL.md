---
name: faith-skill-factory
description: "Build a complete skill from scratch end-to-end — requirements through architecture, content filling, and deployment. Not for single-step operations (design → faith-skill-architect, fill → faith-skill-solution, deploy → faith-skill-deploy)."
disable-model-invocation: true
argument-hint: "[skill-requirements]"
version: "3.0.0 | R3: mattpocock full alignment | 2026-07-06"
---

# faith-skill-factory — Skill 全生命周期流水线

> **manufacture** 一条龙不跳过任何 Phase。每个 Phase 的验证清单通过才能进下一阶段。

## 流水线（6 Phase，严格顺序。Phase 5-6 不使用外部 skill，内联执行）

| Phase | 调用 skill | 做什么 | 门禁（必须通过才进入下一Phase） |
|-------|-----------|-------|------|
| 1 设计 | faith-skill-architect | 收集需求 → 搭 L1-L6 骨架 | 骨架完整，需求已确认 |
| 2 锻造 | faith-skill-solution | 填写触发条件/执行步骤/验证/兜底 | G1-G6 全通过或标记例外 |
| 3 部署 | faith-skill-deploy | 生成 README/License/CI → git push | 仓库可访问 |
| 4 注册 | faith-skill-os | 加载到本地 skill 目录 → 更新路由 | 系统可调用 |
| 5 监控 | — (内联) | 创建 EVIDENCE.md + 首次审计基线 | EVIDENCE.md 已创建，含基线时间戳 + 初始审计评分 |
| 6 优化 | — (内联) | 建立每次使用后自动记录 EVIDENCE.md 的反馈循环 | 反馈循环已建立，每次使用后自动追加记录 |

Phase 1 → 2 → 3 → 4 → 5 → 6 按序执行。任一 Phase 门禁不通过 → 停止，提示用户。

## 执行流程（6步，每步有完成标准）

### 步骤1: Phase 1 设计
调用 faith-skill-architect 收集需求。
**完成标准**: 输出需求文档——功能描述 + 输入格式 + 输出格式 + 边界说明 + 不适用场景(3-5条)。

### 步骤2: Phase 2 锻造
调用 faith-skill-solution 填充内容。
**完成标准**: 完整 SKILL.md——包含步骤(每步有完成标准) + 门禁 + 失败模式 + 强词(leading word)。

### 步骤3: Phase 3 部署
调用 faith-skill-deploy 部署到仓库。
**完成标准**: 仓库中有完整的 SKILL.md + README.md + LICENSE，git push 成功并输出 commit hash。

### 步骤4: Phase 4 注册
加载到本地 skill 目录。
**完成标准**: 技能目录可被系统调用，faith-index 已更新路由映射。

### 步骤5: Phase 5 监控
设置监控模板。
**完成标准**: EVIDENCE.md 已创建，包含基线时间戳 + 初始审计评分。

### 步骤6: Phase 6 优化
进入持续优化循环。
**完成标准**: 反馈循环已建立，每次使用后自动记录 EVIDENCE.md。

## 产出物

```
skill-{name}/
├── SKILL.md             # Phase 1+2
├── README.md            # Phase 3
├── LICENSE              # Phase 3
├── .github/workflows/   # Phase 3
└── EVIDENCE.md          # Phase 5
```

## Phase 5 监控 — 内联说明

本 Phase 不再调用 faith-skill-overseer（该 skill 不存在）。由 factory 自身内联执行：
1. 在 skill 目录创建 `EVIDENCE.md` 模板，包含字段：基线时间戳、初始审计评分、使用次数、平均评分、最后审计结果
2. 执行首次审计：对照 Matt Pocock 7 铁律逐项打分（0/1），计算 G1-G6 通过率
3. 将审计结果写入 EVIDENCE.md 作为基线

## Phase 6 优化 — 内联说明

本 Phase 不再调用 faith-r3-optimize（该 skill 不存在）。由 factory 自身内联执行：
1. 建立反馈机制：每次 skill 被调用时，提示使用者记录表现数据（得分 0-10、遗漏/错误项）
2. 累计 ≥5 次使用记录后，分析薄弱 Gate → 建议 SKILL.md 修改点
3. 修改必须走 diff-reviewer 审查后方可提交

## 失败模式

| 失败 | 兜底 |
|------|------|
| Phase 2 锻造后 G1-G6 不通过 | 标记未通过项 → 返回 Phase 2 修复 |
| Phase 3 部署失败（网络/权限） | 跳过部署，本地保留完整产物，标注"需手动推送" |
| Phase 5 overseer 审计不通过 | 输出审计报告 → 返回 Phase 2 修复 |
| Phase 4 注册失败（索引未更新） | 手动执行 `faith-index rebuild`，确认路由表含新 skill |
| Phase 5 EVIDENCE.md 写入失败 | 输出审计报告到 stdout，标注"EVIDENCE.md 需手动创建" |
| Phase 6 反馈循环中断（≥3次使用无记录） | 降级为手动记录模式，每次使用后在 EVIDENCE.md 追加时间戳行 |
| 用户需求不够具体 | 反问"这个 skill 的核心功能是什么？输入是什么？输出是什么？"后重试 |

---
*version: 3.0.0 | principle: manufacture | ref: Matt Pocock 7 Principles*
