---
name: faith-skillwriter
description: Author new skills from requirements or sharpen existing ones — applies Matt Pocock writing-great-skills principles by default. Use when the user says "写一个skill", "创建skill", "帮我写skill", "优化这个skill", "按matt原则改skill", or describes what a new skill should do. Not for deployment to GitHub (→ faith-skill-deploy) or multi-skill synthesis (→ faith-skill-solution).
disable-model-invocation: true
argument-hint: "[create-or-optimize]"
version: "1.0.0 | R1: initial release | 2026-07-06 | model: agnostic"
---

# faith-skillwriter — 原则驱动的 Skill 写作器

> **author** new skills from scratch — ask three questions, pick a leading word, structure the body, self-audit.
> **sharpen** existing skills — diagnose against the 7 principles, fix the top issues, verify.
> The 7 principles live in [`references/7-principles.md`](references/7-principles.md) — consult them on every run.

This is opinionated. It defaults to user-invoked, leading-word-anchored, completion-criterion-gated skills. If you want formal 6-layer architecture, use faith-skill-architect. If you want an end-to-end pipeline, use faith-skill-factory.

## Mode routing

```
User says "创建"/"写一个"/"新建" + describes what the skill does    → Mode A: Author
User says "优化"/"改进"/"按原则改" + points to existing skill      → Mode B: Sharpen
Cannot determine                                                    → Ask: "Create a new skill or optimize an existing one?"
```

---

## Mode A: Author — create a new skill from scratch

### Step 1: Ask the three questions

Only ask what isn't already clear from the user's input:

1. **What does this skill do?** (one sentence — the core function)
2. **When should someone use it?** (trigger conditions — what the user says or does)
3. **When should they NOT use it?** (boundaries — what nearby skills handle instead)

**Completion criterion**: Three answers captured — core function, ≥2 trigger conditions, ≥1 boundary.

### Step 2: Pick the leading word

Choose one compact English word that anchors the skill's behavior. Requirements:
- Lives in the model's pretraining (common concept, not invented jargon)
- Captures the skill's essence in one token
- Appears in both the overview line and the description

| Pattern | Leading word | Example |
|---------|-------------|---------|
| Detection + replacement | `audit` | faith-humanizer |
| Quality gate | `gate` | faith-gate-check |
| Multi-step process | `orchestrate` | faith-pipeline |
| Extraction + simplification | `distill` | faith-skill-distill |
| Creation from parts | `author` | this skill |
| Search + verification | `verify` | faith-truth-check |

**Completion criterion**: Leading word chosen + one-line justification linking it to the skill's core function.

### Step 3: Choose the invocation axis

Default: **user-invoked** (`disable-model-invocation: true`). Only switch to model-invoked if:

- Another skill must reach it (model-invoked skills can call each other)
- The agent must recognize when to fire it autonomously (no human cue)

**Completion criterion**: Invocation axis decided with one-line rationale. If model-invoked, description written with "Use when [triggers]. Not for [exclusions]." format.

### Step 4: Decide the information hierarchy

Not every skill needs steps. A skill can be:

| Type | Example | Structure |
|------|---------|-----------|
| All steps | tdd | Numbered steps with completion criteria |
| All reference | code-review | Rules, definitions, patterns — consulted on demand |
| Mixed | triage | Steps for process + disclosed reference for details |

**Completion criterion**: Hierarchy decision made. Skeleton outline: section labels + one-line content descriptions.

### Step 5: Write the skill body

Apply these rules from the 7-principles reference:

1. **Every step ends on a completion criterion** — checkable. Not "understand the problem" but "output a problem statement ≤50 words with root cause identified."
2. **Push heavy reference behind pointers** — if a definition, checklist, or rule-set exceeds ~10 lines, disclose it to a linked file.
3. **Co-locate related concepts** — definition, rules, caveats, and examples for one concept live under one heading.
4. **Delete every no-op** — if removing a sentence doesn't change what the agent does, remove it. "Be thorough" is a no-op; "check every file modified in this commit" is not.
5. **Make gates checkable** — checkbox format, pass/fail decidable without judgment.
6. **Cover failure modes** — table: failure → cause → recovery. At least 2 entries.

**Completion criterion**: Complete SKILL.md body — all steps have completion criteria, heavy content is disclosed, no-ops are removed, gates are checkable, failure modes are covered.

### Step 6: Self-audit

Run the completed skill against the 7-principles checklist from the reference. Flag every FAIL.

**Completion criterion**: Audit report — per-principle PASS/FAIL. Any FAIL has a concrete fix applied before output.

### Step 7: Output

Output the complete SKILL.md ready to save.

**Completion criterion**: User confirms the skill is complete. Remind them to register it in faith-index.

---

## Mode B: Sharpen — optimize an existing skill

### Step 1: Diagnose

Read the target SKILL.md. Check each principle:

| # | Principle | Check |
|---|-----------|-------|
| 1 | Predictability over creativity | Does the skill make the agent take the same *process* every run? |
| 2 | Correct invocation axis | Is it model-invoked only when necessary? If user-invoked, is description a clean one-liner? |
| 3 | Description triggers, not summarizes | Does the description list specific trigger conditions and boundaries? |
| 4 | Information hierarchy clear | Are steps primary, reference secondary, heavy reference disclosed? |
| 5 | Every step has completion criterion | Is each criterion checkable (boolean/threshold)? Not "分析清楚" but "输出包含X/Y/Z三个字段的结构化分析". |
| 6 | Need to split? | Do post-completion steps tempt premature completion? Hide them by splitting. |
| 7 | Leading word anchors behavior | Does a compact pretrained word appear in both overview and description? |
| 8 | No-ops removed | Can any sentence be deleted without changing agent behavior? |

**Completion criterion**: Diagnosis report — 8-item checklist, each PASS/FAIL, top 3 issues ranked by impact on predictability.

### Step 2: Fix

Apply fixes in priority order. Top 3 issues first.

**Completion criterion**: Every diagnosed FAIL has a fix applied or a documented reason for deferral (e.g., "router skill — no steps to complete").

### Step 3: Verify

Re-run the diagnosis. All previously FAIL items should now PASS.

**Completion criterion**: Re-diagnosis — zero FAILs on the top 3. Remaining minor FAILs documented.

### Step 4: Output

Output the optimized SKILL.md + changelog.

**Completion criterion**: Changelog lists every change with: what changed + why (which principle it addresses).

---

## Failure modes

| Failure | Fallback |
|---------|----------|
| User can't articulate what the skill does | Ask: "Walk me through an example — what would someone say to trigger this skill, and what should happen?" |
| No obvious leading word | Default to the most descriptive verb from the user's own description. Re-read the reference for candidates. |
| Existing skill is too broken to repair | Offer to switch to Mode A (author from scratch), carrying over salvageable content |
| User wants model-invoked but skill has no cross-skill need | Explain the context-load tradeoff. Default to user-invoked unless a concrete cross-skill dependency exists. |
| Skill exceeds ~10KB even after disclosure | Flag for splitting: identify a sub-sequence that can be its own skill (the "sequence cut") |

## Boundary with other faith skill-engineering skills

| You want to... | Use |
|---------------|-----|
| Quickly create a skill with principle guidance | **faith-skillwriter** (this skill) |
| Formally architect with 6-layer structure | faith-skill-architect |
| End-to-end pipeline (design → deploy) | faith-skill-factory |
| 6-gate audit or multi-skill merge | faith-skill-solution |
| Extract executable skeleton only | faith-skill-distill |
| Push to GitHub | faith-skill-deploy |

---
*version: 1.0.0 | leading word: author-sharpen | ref: mattpocock/skills writing-great-skills*