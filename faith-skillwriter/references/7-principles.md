# The 7 Principles — Writing Great Skills

> Disclosed reference for faith-skillwriter. Derived from Matt Pocock's `writing-great-skills` and `GLOSSARY.md`.
> A skill exists to wrangle **predictability** out of a stochastic system — the agent taking the same *process* every run.

---

## Principle 1: Predictability is the root virtue

A skill exists to make the agent behave the same *way* on every run — same process, not same output. Not "write well" but "write consistently."

**Check**: If you ran the skill 10 times on similar inputs, would the agent follow the same decision path each time?

**Anti-pattern**: Adding "要有创意", "语言要生动" to a content skill — these invite variance, not convergence.

---

## Principle 2: Invocation is a design decision

| Axis | Cost | When to use |
|------|------|------------|
| **User-invoked** (`disable-model-invocation: true`) | Cognitive load on human | Default. Human is the index. |
| **Model-invoked** (has description) | Context load on agent | Only when another skill must reach it, or agent must auto-detect. |

**Check**: Does another skill call this? Does the agent need to recognize the trigger autonomously? If both answers are no, make it user-invoked.

**Anti-pattern**: Making every skill model-invoked "because auto is better" — every description burns context tokens.

---

## Principle 3: Description = triggers, not summary

A model-invoked description lists branches that should fire the skill. Every word spends context load.

**Format**: "Use when [trigger conditions]. Not for [exclusions]."

**Check**: Does each trigger represent a genuinely distinct branch, not a synonym? Are there words that don't contribute to routing?

**Anti-pattern**: "Generates high-quality XXX content supporting multiple styles for YYY scenarios" — every skill says this, so it routes nothing.

---

## Principle 4: Information hierarchy — steps above, reference below

| Tier | What | Where |
|------|------|-------|
| Primary | Steps (ordered actions) | SKILL.md |
| Secondary | Reference (definitions, rules, facts) | SKILL.md |
| Tertiary | Heavy reference (>10 lines per concept) | Disclosed file behind a context pointer |

**Check**: If you removed all reference from SKILL.md, would the steps still make sense? If a reference file disappeared, would the skill still function (degraded, not broken)?

**Anti-pattern**: A 3000-word SKILL.md where steps, definitions, examples, and caveats are interleaved — the agent treats all text equally and dilutes attention.

---

## Principle 5: Every step needs a completion criterion

A completion criterion is a **checkable condition** that tells the agent "this step is done." Not "分析清楚" (which the agent can't self-assess), but "输出包含选题判断、目标读者画像、核心信息点三个字段的结构化分析" (which it can).

**Two qualities of a good criterion**:
1. **Checkable** — can the agent tell done from not-done? (boolean or threshold)
2. **Exhaustive** (where it matters) — "every modified model accounted for", not "produce a change list"

**Check**: Can the agent objectively determine if the criterion is met without human judgment?

**Anti-pattern**: "分析用户需求" — vague, uncheckable. The agent guesses "good enough" and moves on.

---

## Principle 6: Split when post-completion steps tempt rushing

Two cuts justify splitting:

1. **By invocation** — a distinct leading word should trigger a sub-skill independently, or another skill must reach it. You pay context load for the new description.
2. **By sequence** — the steps still ahead tempt the agent to rush the current step (premature completion). Hiding them encourages more legwork on the current task.

**Check**: When the agent reaches step 3, does seeing steps 4-7 in the same file make it skim step 3? If yes, split there.

**Anti-pattern**: A 9-step skill where the agent rushes steps 5-9 because it can see the endpoint and wants to reach it.

---

## Principle 7: Leading words anchor behavior

A **leading word** is a compact concept from the model's pretraining that the agent thinks with while running the skill. It anchors execution (body) and invocation (description).

| Instead of writing... | Use the leading word... |
|----------------------|------------------------|
| "fast, deterministic, low-overhead triage" | `triage` |
| "carefully check every item against every rule" | `audit` |
| "reduce to essential structure, remove everything else" | `distill` |

**Check**: Is there a single English word that, if placed in the overview line, would orient the agent's entire approach? Does it appear in the description?

**Anti-pattern**: "请认真对待", "注意质量" — no-ops. The agent already defaults to this. The word adds no behavioral change.

---

## Bonus: The no-op test

> A sentence is a **no-op** if deleting it does not change the agent's behavior.

**Test**: Delete the sentence. Run the skill. Did the output change? If not, permanently delete it.

Most prose that fails this test should be removed, not rewritten. Aggressive pruning keeps attention where it matters.

**Common no-ops**: "Be thorough", "pay attention to detail", "ensure high quality", "remember to", "don't forget to".

---

## Self-audit checklist

Run this against any skill you write or optimize:

- [ ] **P1 Predictability**: 10 runs, same process each time?
- [ ] **P2 Invocation**: User-invoked by default; model-invoked only with justification?
- [ ] **P3 Description**: Triggers and boundaries only? No filler?
- [ ] **P4 Hierarchy**: Steps primary, reference secondary, heavy content disclosed?
- [ ] **P5 Completion**: Every step ends on a checkable criterion?
- [ ] **P6 Split**: No premature-completion temptation from visible post-completion steps?
- [ ] **P7 Leading word**: One compact English word anchors the skill in overview + description?
- [ ] **No-op**: Any sentence that can be deleted without behavioral change?

---
*ref: mattpocock/skills — writing-great-skills + GLOSSARY.md*