---
name: faith-index
description: Router skill for the faith skill family. Names every faith skill and when to reach for each.
disable-model-invocation: true
argument-hint: "[what-do-you-need]"
version: "2.2.0 | R2.2: +faith-skillwriter | 2026-07-06 | model: agnostic"
---

# Faith Index — Router Skill

> **router**: one user-invoked skill that names the others and when to reach for each.  
> Full terminology: GLOSSARY.md

## Content & Writing

| When you need to... | Reach for |
|---------------------|-----------|
| Remove AI flavor from text, de-template copy, restore natural voice | **faith-humanizer** |
| Check if content is ready to publish (language quality, task completion) | **faith-gate-check** |
| Verify facts, validate data sources, assess reliability | **faith-truth-check** |
| Create deep book notes from WeChat Reading exports | **faith-booknote** |
| Produce a full article from raw material to multi-platform publish | **faith-pipeline** |
| Audit a completed pipeline run for bottlenecks | **faith-pipeline-audit** |
| Optimize short-form content hooks and structure for virality | **faith-viral-write** |

## Skill Engineering

| When you need to... | Reach for |
|---------------------|-----------|
| Design a new skill from scratch | **faith-skill-architect** (mode A) |
| Review an existing skill for quality | **faith-skill-architect** (mode B) |
| Optimize an underperforming skill | **faith-skill-architect** (mode C) |
| Evaluate + forge + synthesize multiple skills | **faith-skill-solution** |
| Extract the executable skeleton from a skill | **faith-skill-distill** |
| Validate and deploy a skill to production | **faith-skill-deploy** |
| Build a skill end-to-end (requirements → deploy) | **faith-skill-factory** |
| Author new skills from requirements or sharpen existing ones with Matt Pocock principles | **faith-skillwriter** |

## Workflow & Infrastructure

| When you need to... | Reach for |
|---------------------|-----------|
| Design a multi-agent workflow with rollback | **faith-workflow** |
| Compile a fuzzy task into a reproducible prompt | **faith-prompt-compile** |
| Compress conversation context to essentials | **faith-context-compress** |
| Record and restore session state across runs | **faith-session** |
| Scan a knowledge base and produce a searchable index | **faith-kb-scan** |

## Boundaries (when NOT to use a faith skill)

- Short-form platform adaptation → use baoyu-* skills
- Format/markdown cleanup → use baoyu-format-markdown
- Image generation → use baoyu-image-gen
- Code review → use code-review (mattpocock pattern)

---
*version: 2.0.0 | pattern: Matt Pocock router skill | ref: GLOSSARY.md*