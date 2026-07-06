---
name: faith-skill-factory
description: "Use when the user wants to build a complete skill from scratch end-to-end — requirements through architecture, content filling, and deployment. Not for single-step operations (evaluate → faith-skill-solution, deploy → faith-skill-deploy)."
disable-model-invocation: true
argument-hint: "[skill-requirements]"
version: "2.0.0 | R2: Matt Pocock 7铁律对齐 | 2026-07-06 | model: agnostic"
---

# faith-skill-factory — Skill 全生命周期流水线

> **manufacture** 一条龙不跳过任何 Phase。每个 Phase 的验证清单通过才能进下一阶段。

## 流水线（6 Phase，严格顺序）

| Phase | 调用 skill | 做什么 | 门禁（必须通过才进入下一Phase） |
|-------|-----------|-------|------|
| 1 设计 | faith-skill-architect | 收集需求 → 搭 L1-L6 骨架 | 骨架完整，需求已确认 |
| 2 锻造 | faith-skill-solution | 填写触发条件/执行步骤/验证/兜底 | G1-G6 全通过或标记例外 |
| 3 部署 | faith-skill-deploy | 生成 README/License/CI → git push | 仓库可访问 |
| 4 注册 | faith-skill-os | 加载到本地 skill 目录 → 更新路由 | 系统可调用 |
| 5 监控 | faith-skill-overseer | 设置 EVIDENCE.md 模板 + 首次审计 | 监控规则已激活 |
| 6 优化 | faith-r3-optimize | 持续优化反馈循环 | — |

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
**完成标准**: 仓库中有完整的 SKILL.md + README.md + LICENSE。

### 步骤4: Phase 4 注册
加载到本地 skill 目录。
**完成标准**: 系统可通过 skill 名称调用。

### 步骤5: Phase 5 监控
设置监控模板。
**完成标准**: EVIDENCE.md 已创建 + 首次审计基线已记录。

### 步骤6: Phase 6 优化
进入持续优化循环。
**完成标准**: 反馈循环已建立（每次使用后记录表现数据）。

## 产出物

```
skill-{name}/
├── SKILL.md             # Phase 1+2
├── README.md            # Phase 3
├── LICENSE              # Phase 3
├── .github/workflows/   # Phase 3
└── EVIDENCE.md          # Phase 5
```

## 失败模式

| 失败 | 兜底 |
|------|------|
| Phase 2 锻造后 G1-G6 不通过 | 标记未通过项 → 返回 Phase 2 修复 |
| Phase 3 部署失败（网络/权限） | 跳过部署，本地保留完整产物，标注"需手动推送" |
| Phase 5 overseer 审计不通过 | 输出审计报告 → 返回 Phase 2 修复 |
| 用户需求不够具体 | 反问"这个 skill 的核心功能是什么？输入是什么？输出是什么？"后重试 |

---
*version: 2.0.0 | principle: manufacture-design-forge-deploy | ref: Matt Pocock 7 Principles*