---
name: faith-workflow
description: Design multi-agent execution plans — decompose tasks into capsules, orchestrate as parallel/sequential/pipeline, define rollback rules. Use when the user wants to "编排工作流", "设计多Agent方案", "并行处理", or needs a complex task broken into coordinated sub-tasks with recovery. Not for content-only pipelines (→ faith-pipeline).
disable-model-invocation: true
argument-hint: "[workflow-goal]"
version: "3.0.0 | R3: mattpocock deep reconstruction | 2026-07-06 | model: agnostic"
---

# faith-workflow — 多Agent工作流编排

> **orchestrate** — decompose tasks into capsules, arrange as parallel/sequential/pipeline, define rollback rules.

## Orchestration mode quick reference

| Mode | When | Example |
|------|------|---------|
| **parallel** | Independent sub-tasks (non-overlapping write sets) | Write 3 articles on different topics simultaneously |
| **sequential** | Dependent task chains | Evaluate → optimize → verify |
| **pipeline** | Stage-gated flow (artifacts pass between stages) | Source → structure → create → publish |

## Workflow definition template (4 fields)

1. **Goal**: One-sentence output objective
2. **Capsules**: Per-stage = {skill name, input, expected output, verification condition}
3. **Execution Plan**: Arrangement — which mode, which dependencies
4. **Rollback**: Interrupt conditions + target rollback stage

**Constraints**: ≤10 capsules; no invocation cycles (A→B→C→A); every capsule's verification condition is decidable (boolean or threshold).

## Process (5 steps)

### Step 1: Gather requirements

Clarify the output objective and boundaries.
**Completion criterion**: Output a Goal field (one sentence) + boundary notes (≤3 exclusions).

### Step 2: Decompose into capsules

Break the task into ≤10 capsules, each with skill/input/output specified.
**Completion criterion**: Every capsule has a verification condition written as a decidable boolean expression or numeric threshold.

### Step 3: Determine orchestration mode

Assign each capsule its mode and dependencies.
**Completion criterion**: Output Execution Plan with each capsule's mode (parallel/sequential/pipeline) and dependency edges explicitly drawn.

### Step 4: Set rollback rules

Define recovery paths.
**Completion criterion**: Output Rollback field — each rule formatted as `[trigger condition] → [rollback to capsule N]`. At least 1 rule.

### Step 5: Output the workflow design document

The complete 4-field design.
**Completion criterion**: All four fields present and internally consistent.

## Gates

- [ ] Capsule count ≤ 10
- [ ] No invocation cycles
- [ ] Every capsule has a decidable verification condition
- [ ] Rollback rules defined (≥1)

## Boundary with faith-pipeline

| Scenario | Use |
|----------|-----|
| Content creation from source to publish | faith-pipeline (fixed-phase pipeline) |
| Any task's general orchestration (including parallel/rollback) | faith-workflow |
| Pipeline is a special case of workflow | Sequential + fixed phases |

## Failure modes

| Failure | Fallback |
|---------|----------|
| Capsule count exceeds 10 | Suggest merging similar stages or split into multiple sub-workflows |
| Invocation cycle detected | Redesign dependencies |
| Post-rollback state inconsistency | Each capsule's output files managed separately; rollback never overwrites passed stages |

---
*version: 3.0.0 | leading word: orchestrate | ref: mattpocock/skills writing-great-skills*