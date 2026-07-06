---
name: faith-skill-distill
description: Extract the executable skeleton from a skill — strip reference, keep only steps and gates. Use when the user wants to "提取skill骨架", "精简skill", or needs a minimal runnable version of a skill. Not for skill creation (→ faith-skill-architect) or deployment (→ faith-skill-deploy).
disable-model-invocation: true
argument-hint: "[skill-path]"
version: "2.0.0 | R2: mattpocock deep reconstruction | 2026-07-06 | model: agnostic"
---

# faith-skill-distill — Skill 骨架提取

> **distill** — read a skill, strip all reference, keep only the executable skeleton (steps + gates + failure modes).

## Process (3 steps)

### Step 1: Read and classify
Read the skill file. Classify every section as **step** (ordered action), **reference** (definition/rule/fact consulted on demand), or **meta** (version/changelog).
**Completion criterion**: Output a classification map — every section tagged [step]/[reference]/[meta] with line ranges.

### Step 2: Strip reference
Remove all [reference] and [meta] sections. Keep [step] sections, gates, and failure modes intact.
**Completion criterion**: The distilled skill has: (1) all steps preserved in order, (2) all gates preserved, (3) all failure modes preserved, (4) zero reference content.

### Step 3: Validate
Check the distilled skill is self-contained — can an agent execute it without the removed reference?
**Completion criterion**: Walk through each step — verify every referenced term has an inline definition remaining in the distilled version. Flag any missing definitions.

## Output

```
# [Skill Name] — Distilled

[Steps only — numbered, with completion criteria]

## Gates
[Preserved gates]

## Failure Modes
[Preserved failure modes]
```

## Failure modes

| Failure | Fallback |
|---------|----------|
| Skill has no steps (all reference) | Output "Skill is all reference — no executable skeleton to distill" |
| Distilled version missing key definitions | Add inline definitions for flagged terms, re-validate |
| Skill file unreadable | Output error, stop |

---
*version: 2.0.0 | leading word: distill-classify-strip | ref: mattpocock/skills writing-great-skills*