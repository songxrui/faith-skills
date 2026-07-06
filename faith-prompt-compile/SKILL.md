---
name: faith-prompt-compile
description: Compile fuzzy tasks into reproducible, verifiable, low-bloat prompts — a five-step algorithm with task-type routing and a built-in auditor. Use when the user says "优化提示词", "prompt engineering", "编译提示词", "写提示词", or finds a prompt too wordy. Not for direct task execution (→ corresponding skill) or context compression (→ faith-context-compress).
disable-model-invocation: true
argument-hint: "[task-description]"
version: "2.0.0 | R2: mattpocock deep reconstruction | 2026-07-06 | model: agnostic"
---

# faith-prompt-compile — 第一性 Prompt 编译器

> **compile** — five-step algorithm: question → delete → simplify → accelerate → template.

## Five-step compile algorithm

### Step 1: Question
What pseudo-requirements, over-formatting, and false assumptions are in the user's request? Remove everything whose absence won't degrade output quality.
**Completion criterion**: Output a cleaned requirement list — original items marked [kept]/[removed] with one-line reasons.

### Step 2: Delete
Rules with high parroting-index (rule cost ÷ real benefit) must go. For each deletion, ask: "Without this, would output quality drop?"
**Completion criterion**: Deletion log — N items removed, each with cost/benefit rationale (≤15 words each).

### Step 3: Simplify
Cover with fewest modules: Goal → Input → Flow → Output Format → Verification Standard → Bans.
**Completion criterion**: Six modules all present, each ≤3 bullet points.

### Step 4: Accelerate
Make it directly copy-paste usable by an average user — no secondary interpretation needed.
**Completion criterion**: A non-expert reads the prompt and can execute without asking clarifying questions.

### Step 5: Template
When structure is stable and reusable, sediment it as a template.
**Completion criterion**: Template tagged with task type + complexity level (simple/medium/complex).

## Task-type routing

| Type | Must include |
|------|-------------|
| Fact/research | Source tier, verification path, no-fabrication rule |
| Decision | Options, trade-offs, recommendation, abandonment conditions |
| Writing/creative | Audience, purpose, tone, structure, bans |
| Code/development | Read files first, minimal changes, test verification |
| Prompt optimization | Role, objective function, input variables, output format, quality gate |

## Anti-LLM-defect rules

- No warm-up openings — deliver directly
- No "等等" or "自行补充" shortcuts
- No principles-only (give actions), no conclusions-only (give evidence)
- No "如果你需要我可以继续" endings
- Abstract judgments must bind to decidable conditions (threshold/checklist/boundary)

## Built-in auditor

Final prompt runs through 8 checks:
1. Real deep goal surfaced, or just restated surface?
2. Any filler / repetition / templates?
3. Missing key variables / output format / verification?
4. Anything that invites laziness or fabrication?
5. Too long → key points diluted?
6. Directly copy-paste usable?
7. Complies with five-step algorithm?
8. Can produce genuinely useful output?

**Result**: ✓ Pass / 🔧 Minor fix / 🔄 Rewrite

## Output format

```
### 1. Task reconstruction (surface → deep goal)
### 2. Self-audit result (Pass/Minor fix/Rewrite)
### 3. Final Prompt (directly copyable)
### 4. Usage notes (what to replace, how to run, when not applicable)
```

## Length budget

Simple: 300-700 chars / Medium: 700-1500 chars / Complex: 1500-2500 chars.
Exceeding 2500 chars requires confirmation that every section directly improves output quality.

---
*version: 2.0.0 | leading word: compile | ref: mattpocock/skills writing-great-skills*