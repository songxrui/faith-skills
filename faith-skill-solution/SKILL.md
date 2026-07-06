---
name: faith-skill-solution
description: Evaluate, forge, or synthesize skills — 6-gate audit, targeted optimization, and multi-skill merging. Use when the user says "评测这个skill", "优化skill", "合成两个skill", "forge skill", "skill质量审计", or mentions multiple skill names together. Not for initial design (→ faith-skill-architect) or deployment (→ faith-skill-deploy).
disable-model-invocation: true
argument-hint: "[skill-path-or-paths]"
version: "2.0.0 | R2: mattpocock deep reconstruction | 2026-07-06 | model: agnostic"
---

# faith-skill-solution — Skill 评测/锻造/合成

> **evaluate** → **forge** → **synthesize** — three modes covering the full skill improvement lifecycle.

## Mode routing (if-then-else)

```
User says "评测"/"评分"/"检查"/"门禁"           → Mode A: Evaluate
User says "优化"/"升级"/"锻造"/"修复"            → Mode B: Forge
User says "合成"/"融合"/"组合"/"新skill"         → Mode C: Synthesize
User mentions ≥2 skill names                     → Mode C: Synthesize
Cannot determine                                  → Ask: "Evaluate, forge, or synthesize?"
```

---

## Mode A: Evaluate (6-gate audit)

Run G1-G6 checks on the target SKILL.md. Each gate returns PASS/FAIL.

| Gate | Requirement | How to check |
|------|------------|-------------|
| G1 Size | ≤10KB | Estimate lines × avg line length |
| G2 Frontmatter | name + description present, ≤1024 chars | Parse YAML frontmatter |
| G3 Steps | Steps have completion criteria (boolean/checkable) | Scan for "完成标准" or "completion criterion" per step |
| G4 Reference | Heavy reference disclosed behind pointers | Check for linked files in skill folder |
| G5 Gates | At least one gate or verification checkpoint | Scan for "门禁" or "gates" section |
| G6 Leading word | A compact concept word anchors the skill | Check for bold word in overview line |

**Completion criterion**: Output 6-gate report — per-gate PASS/FAIL + overall rating (A/B/C/D/F) + top 3 fixes.

---

## Mode B: Forge (targeted optimization)

Take an evaluated skill and apply fixes.
**Completion criterion**: (1) All G1-G6 gates that failed in evaluation now pass; (2) Changelog output (what changed, why, per fix).

---

## Mode C: Synthesize (multi-skill merge)

Merge ≥2 skills into one cohesive skill.
**Completion criterion**: (1) Unified leading word; (2) No duplicate sections; (3) All original gates preserved or consolidated; (4) Boundary section defines relationship with source skills.

---

## Failure modes

| Failure | Fallback |
|---------|----------|
| Target skill file not found | Output error path, stop |
| Mode unclear from user input | Ask explicit question, don't guess |
| Synthesize with incompatible skills | Flag incompatibilities, suggest keeping separate |

---
*version: 2.0.0 | leading word: evaluate-forge-synthesize | ref: mattpocock/skills writing-great-skills*