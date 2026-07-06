---
name: faith-humanizer
description: Humanize AI-generated Chinese text — audit AI fingerprints, replace with natural handwritten voice calibrated against four verified human writing paradigms (dontbesilent oral + 认知觉醒 narrative + 被讨厌的勇气 Socratic + 专注的艺术 philosophical), verify no information loss. v6.3 adds M-class detection: AI's "lazy connector" patterns (em-dash overuse, statistical filler, simplification tags, self-Q&A, parallel openings, reader-prediction, universal-conclusion, unnecessary-definition). Use when the user says "去AI味", "太AI了", "改成手写感", "像人写的", "人味不够", or when faith-pipeline reaches Phase 4.
disable-model-invocation: true
argument-hint: "[ai-generated-text]"
version: "6.3.0 | R6.3: M-class lazy-connector detection + Voice D mechanism-depth recalibration | 2026-07-06 | model: agnostic"
---

# faith-humanizer — AI文本转自然手写

> **audit** AI fingerprints → **replace** with human patterns → **verify** nothing was lost → **calibrate** against authentic voice.
> Three disclosed references (consult on every run):
> - [`references/ai-patterns-checklist.md`](references/ai-patterns-checklist.md) — 98 detection patterns (A-M classes, v2.3)
> - [`references/positive-writing-patterns.md`](references/positive-writing-patterns.md) — Four-voice positive calibration (v2.3, Voice D recalibrated: mechanism depth replaces metaphor building)
> **v6.3 核心发现（来自 v1→v3 三轮迭代）**: AI文本的本质指纹不是"用了某个词"，是**用形式替代了实质**。破折号替代句子间逻辑设计。"大多数人"替代举证。"一句话概括"替代精炼表达。隐喻框架替代因果机制解释。反AI的核心不是禁用这些形式，是强迫自己完成它们替你跳过的那一步工作。

## Voice selection (run first, before any pass)

| If content is... | Use... | Rationale |
|-----------------|--------|-----------|
| Short-form (<500 chars) OR social media | **Voice A** (dontbesilent) | Punchy, oral, raw. ⚠️ Mandatory M+L class scan |
| Long-form article/course/book (>1500 chars) | **Voice B** (认知觉醒/周岭) | Story-driven, inclusive |
| Debate/argument/psychological deep-dive | **Voice C** (被讨厌的勇气) | Socratic dialogue |
| Personal growth/philosophical reflection | **Voice D** (专注的艺术/Dan Koe) | ⚠️ v6.3: mechanism depth, NOT metaphor building |
| Medium (500-1500 chars) | **Auto-detect** | Dialogue markers→C. Abstract philosophy→D. Instructional≥3→B. Else→A |

## The loop

Every run executes **audit → replace → verify → calibrate**, five passes. Never skip a pass.

### Pass 1: High-priority detection (run first, always)

Scan every sentence against:
- **Class A** — Fixed AI sentence skeletons
- **Class B** — False depth vocabulary
- **Class G** — Authenticity defects
- **Class L** — AI oral-style low-level forgery (L1-L10, mandatory for ALL voices)
- **Class M** — AI lazy-connector patterns (M1-M8, v6.3 NEW, mandatory for ALL voices)
- If Voice B: also scan **Class K** — 认知觉醒 style forgery

**Completion criterion**: Every A/B/G/L/M (and K if applicable) hit replaced. Output hit list: `[pattern-id] [original fragment] [location: paragraph N]`.

⚠️ **Red-line rules:**
- L1-L6 + M1-M4: any hit = automatic fail. Fix before proceeding.
- M1 (破折号密度): >1 per 300 chars = FAIL. Replace with periods/commas/colons. Keep only legitimate paired parenthetical em-dashes.
- M2 (统计腔): zero tolerance. Replace "大多数人" with direct description.

### Pass 2: Mid-priority cleanup

- **Class C** — Layout scaffolding
- **Class D** — Hollow generalities
- **Class H** — Structural smells

**Completion criterion**: Every C/D/H hit replaced. Information preserved.

### Pass 3: Deferred fixes

- **Class E** — Citation formatting
- **Class F** — Title/punctuation patterns

### Pass 4: Soul restoration

- **Class I** — Soul deficit
- **Class J** — Human feature deficit (J1-J12, with v6.2 stricter thresholds)

### Pass 5: Positive calibration — four-voice (v6.3 Voice D recalibrated)

#### 5A: Voice A — dontbesilent oral/spoken

| # | Signature | Target |
|---|-----------|--------|
| A1-A12 | [同v6.2] | ≥8/12 PASS + zero L1-L6 + zero M1-M4 |

#### 5B: Voice B — 认知觉醒/周岭 written/narrative

| # | Signature | Target |
|---|-----------|--------|
| B1-B12 | [同v6.2] | ≥8/12 PASS |

#### 5C: Voice C — 被讨厌的勇气 Socratic dialogue

| # | Signature | Target |
|---|-----------|--------|
| C1-C12 | [同v6.2] | ≥8/12 PASS |

#### 5D: Voice D — 专注的艺术 philosophical reflection (v6.3 RECALIBRATED)

| # | Signature | Target | v6.3 Change |
|---|-----------|--------|-------------|
| D1 | Cosmic perspective reframe | ≥1 instance linking personal problem to real natural mechanism, NOT metaphor | No change |
| D2 | **Mechanism depth** (was "metaphor depth") | Track causal chain ≥3 steps (A→B→C→observable). Use established scientific terms. **Zero new concept labels** | 🔄 REPLACED: metaphor building → mechanism tracing |
| D3 | Anti-motivational stance | Zero "动力/激情/正能量" vocabulary | No change |
| D4 | Pattern recognition | ≥1: 2+ cases → common structure → name pattern → apply. Pattern must trace to original data | Tightened |
| D5 | Long-sentence flow | Average 20-40 chars/sentence. **Em-dash density ≤1/400 chars** | Added em-dash constraint |
| D6 | Paradoxical framing | ≥1: opposing sides → underlying unity | No change |
| D7 | Concrete-to-abstract ladder | Concrete scene(≥1句) → mechanism(≥2句) → principle(≥1句). No metaphor jumps | Tightened: ban metaphor jumps |
| D8 | Choice as meta-theme | ≥1 elevation of "choice" | No change |
| D9 | Detachment instruction | ≥1 moment teaching self-observation | No change |
| D10 | Cyclical/natural metaphors | Use real biological/seasonal cycles | No change |
| D11 | No cheap comfort | Never "一切都会好起来"; offer cognitive tools | No change |
| D12 | Integrative ending | Ties personal struggle back to universal pattern | No change |

**Completion criterion**: ≥8/12 PASS. D2+D5+D7: all three must PASS (core v6.3 changes).

#### 5E: Universal cross-voice calibration

| # | Universal Check | Threshold |
|---|----------------|-----------|
| U1 | Zero AI template sentences | =0 |
| U2 | Concrete anchoring | Every abstract concept has ≥1 example |
| U3 | Emotional temperature | ≥1 emotion with scene context |
| U4 | Source attribution | Data has origin or [推断] |
| U5 | Rhythm irregularity | Sentence-length stddev > mean |
| U6 | Personal voice | ≥1 distinctive habit |
| U7 | No softener addiction | "可能/或许" < 1/500 chars |
| U8 | No hollow summary | Zero "综上所述/总而言之" |
| U9 | Logical progression | ≥50% natural transitions |
| U10 | Cross-domain citations | ≥2 fields/sources |
| U11 | Reader awareness | ≥1 response to anticipated question |
| U12 | Original concept | ≥1 reusable concept |

**Completion criterion**: ≥8/12 PASS.

## Verify

- [ ] Every Pass 1 hit replaced? (includes M-class for all voices)
- [ ] Side-by-side: zero core fact loss?
- [ ] First-person claims have real basis? (Class G)
- [ ] **M1**: Em-dash density ≤1/300 chars? (replace periods/commas)
- [ ] **M2-M4**: Zero "大多数人"/"一句话概括"/"X吗——不是"?
- [ ] Voice-specific: ≥8/12 PASS?
- [ ] Universal: ≥8/12 PASS?
- [ ] **Voice D only**: D2 mechanism depth (≥3-step causal chain, zero new concept labels)? D5 em-dash ≤1/400? D7 no metaphor jumps?
- [ ] Read aloud: does it sound like the target voice?

## Output

1. Humanized complete text
2. Summary: `voice: A|B|C|D | ai-patterns-hit: N | M-class: N | L-class: N | voice-sigs-pass: N/12 | universal-pass: N/12`
3. Design-card prompt (for faith-gate-check)

## Failure modes

| Failure | Cause | Recovery |
|---------|-------|----------|
| M1: em-dash overuse (>1/300) | AI uses —— as universal connector | Replace each —— with . or , or :. Keep only paired parenthetical ——. |
| M2: "大多数人" | AI statistical filler | Replace with direct phenomenon description |
| M3: "一句话概括" | AI pretending to simplify | Delete the tag, state the point directly |
| M4: "X吗——不是" | AI fake dialogue | State the fact directly: "X is Y" |
| M5: N-consecutive "当...的时候" | AI parallel template | Vary paragraph openings |
| Voice D: metaphor building | AI naming concepts instead of explaining mechanisms | Replace every new concept label with ≥3-step causal description using established terms |
| Voice D: metaphor jumps in D7 | "这就像爬山" instead of "因为A导致B" | Every abstraction step must be connected by causal logic, not analogy |
| Oversimplified | Deleted modifiers carrying data | Return to Verify |
| Still reads AI | Skipped A/B/G/L/M | Return to Pass 1 |
| Voice A: 一句一段 (L2) | Each sentence on own line | Merge into paragraphs |

## Anti-examples (v6.3 upgraded — from real v1→v3 iteration)

### M-class anti-examples

| AI (FAIL) | Why | Human equivalent |
|-----------|-----|-----------------|
| 多巴胺驱动的是期待——是推力——是你做事的燃料。 | M1: 两个破折号替代了两个句号，跳过逻辑衔接 | 多巴胺驱动的是期待。它推着你去做下一件事。 |
| 大多数人第一反应是自责。 | M2: 统计腔，无举证 | 第一反应是自责。意志力太差了。 |
| 一句话概括：你不需要更自律，你需要更好的环境。 | M3: 降维宣告跳过精炼工作 | 你不需要更有纪律。你需要一个让正确行为变成默认选项的环境。 |
| 多巴胺受体的下调是不可逆的吗——不是。 | M4: 自问自答腔 | 多巴胺受体的下调是可逆的。 |

### Voice D anti-examples (v6.3)

| AI metaphor (FAIL) | Why | Human mechanism |
|--------------------|-----|----------------|
| 身体是操作系统，项目是APP。 | 隐喻命名替代因果解释 | 前额叶皮层的葡萄糖供应优先级低于脑干。当血糖波动时，前额叶第一个被削减供给。 |
| 健康是乘性因子。 | 概念贴标签 | 睡眠不足→前额叶效率下降→决策质量降低→项目执行受阻。这是一条可追踪的因果链。 |
| 三条线形成三角互锁。 | 隐喻框架替代分子层面因果 | 下丘脑-垂体-肾上腺轴控制皮质醇。它同时影响睡眠周期和多巴胺合成。胰岛素敏感度同时影响血糖和多巴胺信号转导。 |

---
*version: 6.3.0 | leading word: audit-replace-verify-calibrate | v1→v3 iteration learnings baked in: M-class lazy connectors + Voice D mechanism recalibration | ref: mattpocock/skills writing-great-skills*