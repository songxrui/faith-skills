---
name: faith-viral-write
description: Sharpen short-form content for spread — score the hook, tighten structure, design for virality. Use when the user wants to optimize a draft for social media, asks for better hooks, or says "帮我优化开头", "这个选题能爆吗", "帮我改成爆款". Not for long-form articles or de-AI work (→ faith-humanizer).
disable-model-invocation: true
argument-hint: "[draft-content]"
version: "3.0.0 | R3: mattpocock deep reconstruction | 2026-07-06 | model: agnostic"
---

# faith-viral-write — 短内容传播优化

> **sharpen** the hook, score the topic, structure for spread.
> Core framework: HKR (Hook-Knowledge-Resonance) × 6 drivers × platform adaptation.

## Process (5 steps)

### Step 1: Confirm inputs

Confirm four things: topic/material, target platform (公众号/小红书/抖音/unspecified), audience, tone. If the user already stated these, proceed directly.

**Completion criterion**: All four items confirmed or explicitly defaulted.

### Step 2: HKR scoring

Score the topic on three axes (1-5 each):

| Axis | What it measures |
|------|-----------------|
| **H**ook | Does the title/opening make someone stop scrolling? |
| **K**nowledge | What new thing does the reader learn? |
| **R**esonance | Does the reader think "this is about me"? |

**Total ≥ 9 → proceed.** Total ≤ 8 → tell the user: "HKR score is X, weak on [axis]. Suggest a sharper angle or more insight." Do not force-write a weak topic.

**Completion criterion**: HKR scores output with a one-line diagnosis per axis.

### Step 3: Apply drivers (pick ≥3)

| # | Driver | The question it answers |
|---|--------|------------------------|
| 1 | Core thesis | What one sentence should the reader remember? Must take a stance. |
| 2 | Persuasion strategy | Data / story / authority / analogy — pick 1 primary + 1 backup |
| 3 | Emotional rhythm | Opening tension → mid起伏 → climax → closing resonance |
| 4 | Anchor quotes | 2-3 lines that spread independently. Must be insight, not slogan. |
| 5 | Argument variety | Alternate personal story / data / analogy / rhetorical question / counterexample — ≥3 types |
| 6 | Engagement hook | Make the reader feel "I have something to say about this" |

**Completion criterion**: ≥3 drivers applied, each with a one-line result.

### Step 4: Platform adaptation

| Platform | Rules |
|----------|-------|
| **公众号** | Title ≤30 chars. Body 1500-2500 chars. Paragraphs 2-6 sentences. No "随着…发展" openings. |
| **小红书** | Title ≤20 chars, keyword-first + emoji. Body 300-800 chars, short sentences. ≥1 first-person experience. |
| **抖音** | First 3 lines: suspense or counter-intuitive. Body 200-500 chars, ultra-short sentences. Must use "你". |
| **Unspecified** | Use 公众号 standards, note "adjust for target platform". |

**Completion criterion**: Platform rules checked — character counts verified, platform-specific patterns applied.

### Step 5: Titles + visuals

**Titles**: ≥5 alternatives, each tagged with strategy (curiosity gap / data shock / pain-point resonance / counter-intuitive / social currency). No duplicate strategies.

**Visuals**: Per image — position, function (break rhythm / support argument / amplify emotion), scene description prompt.

**Completion criterion**: ≥5 titles with non-repeating strategies. ≥1 visual prompt.

## Gates

- [ ] HKR ≥ 9
- [ ] ≥3 drivers applied
- [ ] ≥5 titles, strategies non-repeating
- [ ] Platform format verified

## Failure modes

| Failure | Fallback |
|---------|----------|
| HKR ≤ 8 | Suggest angle change or more material — don't force it |
| Insufficient user input | Ask: "What's your core thesis? What material do you have? Who's the audience?" |
| User only wants polish/润色 | Skip full process, only do Step 4 format adjustment + Step 5 title supplement |

---
*version: 3.0.0 | leading word: sharpen-score-structure | ref: mattpocock/skills writing-great-skills*