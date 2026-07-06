---
name: faith-pipeline
description: Full content pipeline — source extraction through multi-platform publish, 7 phases in sequence. Use when the user says "帮我出篇文章", "从素材到发布", "做一期内容", "全流程创作", "一鱼多吃", or wants to go from raw material to published content across platforms. Not for single-phase work (creation → faith-viral-write; de-AI → faith-humanizer; format → baoyu-*).
disable-model-invocation: true
argument-hint: "[source-material]"
version: "2.0.0 | R2: mattpocock deep reconstruction | 2026-07-06 | model: agnostic"
---

# faith-pipeline — 统一内容全管线

> **orchestrate** 7 phases in sequence. Each phase has a gate — pass it before moving to the next. Content that can't ship is content that wasn't made.
> **Skip-friendly**: users can start from any phase they already have material for.

## Phase map (gates > creation)

| Phase | What | Skill called | Gate (must pass) |
|-------|------|-------------|------------------|
| 1 Source | Pull ≥1 piece of source material | weread-skills / exa-search | ≥1 valid source with source attribution |
| 2 Structure | Extract 5 content-unit types (QST/CON/OPI/CAS/SOL) + build links | dbs-content-system | All 5 types present, ≥3 relationship links |
| 3 Create | Generate body text + ≥5 title alternatives | faith-viral-write / khazix-writer | ≥5 titles, word count meets platform rules |
| 4 De-AI | 5-dimension diagnosis + banned-word purge + anti-example check | faith-humanizer | All 5 dims ≥3.5, zero banned words, ≥3 stance expressions |
| 5 Media | Cover/image/slide prompts | baoyu-cover-image / xhs-images / slide-deck | Cover prompt includes style/color/layout |
| 6 Adapt | Generate per-platform publish-ready versions | baoyu-post-to-wechat / crosspost | Per-platform versions complete, format compliant |
| 7 Verify | Final banned-word scan + source traceability + task completeness | faith-gate-check | All gates pass (binary PASS) |

## Execution rules

**Phase selection**: Before starting, ask "Which phase should I start from?" Default: full pipeline Phase 1 → 7.

**Phase 4 is mandatory**: Regardless of starting phase, Phase 4 (de-AI) must run before publishing. No skip.

**Exception handling**: Gate fails → return to current phase, redo. Three consecutive fails → output "Gate stuck at [specific gate item], needs human intervention", stop.

**Missing env vars**: Phase 1 — if weread-skills/exa-search fail (no token), fall back to manual source input.

## Output artifacts

```
D:\KnowledgeBase\_alchemist\output\<date>_<topic>\
├── source_material.md    # Phase 1
├── content_units.md      # Phase 2
├── draft.md              # Phase 3
├── draft_humanized.md    # Phase 4
├── media_assets.md       # Phase 5
├── publish_ready/        # Phase 6
│   └── [platform].md
└── final_audit.md        # Phase 7
```

---
*version: 2.0.0 | leading word: orchestrate-gate-ship | ref: mattpocock/skills writing-great-skills*