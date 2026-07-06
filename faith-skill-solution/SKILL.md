---
name: faith-skill-solution
description: Evaluate, forge, or synthesize skills — 6-gate audit, targeted optimization, and multi-skill merging. Use when the user says "评测这个skill", "优化skill", "合成两个skill", "forge skill", "skill质量审计", or mentions multiple skill names together. Not for initial design (→ faith-skill-architect) or deployment (→ faith-skill-deploy).
disable-model-invocation: true
argument-hint: "[skill-path-or-paths]"
version: "3.0.0 | R3: mattpocock full alignment | 2026-07-06 | model: agnostic"
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

Take an evaluated skill and apply fixes step by step.

- **Step 1: Read the evaluation report** — Load the Mode A report (or a provided evaluation file) and extract all FAIL gates, ordered by impact (G1>G2>G3>G4>G5>G6).
- **Step 2: Apply fixes in priority order** — For each FAIL gate, apply the minimal fix:
  - G1 (Size): Strategic-compact or split into referenced sub-files.
  - G2 (Frontmatter): Add missing name, description (<1024 chars), or argument-hint.
  - G3 (Steps): Add completion criteria (checkable condition) to every step.
  - G4 (Reference): Move heavy reference content behind file pointers.
  - G5 (Gates): Add at least one gate or verification checkpoint.
  - G6 (Leading word): Add a compact concept word in bold on the overview line.
- **Step 3: Re-run gates to verify** — Execute G1-G6 on the patched file.
- **Completion criterion**: All previously FAIL gates now PASS; changelog output (what changed, why, per fix).

---

## Mode C: Synthesize (multi-skill merge)

Merge ≥2 skills into one cohesive skill step by step.

- **Step 1: Read all source skills** — Load every target SKILL.md in full. Note each skill's leading word, frontmatter, mode sections, and gates.
- **Step 2: Identify overlaps and conflicts** — Map sections across skills (e.g., both have "Setup", both have "G1-G6"). Flag conflicts where two skills define the same section differently.
- **Step 3: Merge with unified leading word** — Choose or synthesize a single compact concept word that covers all source skills. Merge frontmatter (description ≤1024 chars combining all intent). For conflicting sections, pick the more complete definition or fuse them with clear attribution.
- **Step 4: Deduplicate and consolidate gates** — Collapse duplicate steps/sections into single definitions. Preserve all unique gates from every source skill; merge overlapping gates into one stronger gate. Verify no section is repeated.
- **Completion criterion**: Single cohesive SKILL.md with unified leading word, no duplicate sections, all original gates preserved or consolidated, boundary section defines relationship with source skills.

---

## Failure modes

| Failure | Fallback |
|---------|----------|
| Target skill file not found | Output error path, stop |
| Mode unclear from user input | Ask explicit question, don't guess |
| Synthesize with incompatible skills | Flag incompatibilities, suggest keeping separate |
| Evaluation report missing in forge mode | Run Mode A first, then proceed with forge |
| Patch introduces new FAIL gate | Revert patch, try alternative fix, flag if blocked 3x |
| Source skills share identical content | Flag redundancy, recommend single skill instead of synthesize |
| Merged file exceeds G1 size limit | Apply strategic-compact or split into referenced sub-files |
| User provides unspecified mode hint (colloquial) | Re-parse against mode routing table; if still unclear, ask |

---
*version: 3.0.0 | leading word: evaluate | ref: mattpocock/skills writing-great-skills*
