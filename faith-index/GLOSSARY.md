# Faith Skills Glossary

> Shared terminology for the faith skill family. Each bold term is defined here; skills reference by name.

---

## Core Concepts

### humanize
The act of converting AI-generated text into natural, personal, handwritten Chinese. Not style adaptation — pattern removal. Executed by faith-humanizer.

### audit (faith audit)
A systematic check against a checklist of known failure patterns. Used by faith-humanizer (AI pattern audit), faith-pipeline-audit (pipeline audit), faith-truth-check (fact audit).

### triage
Route a user request to one of several modes based on intent signals. Used by faith-skill-architect (design/review/optimize modes) and faith-index (skill routing).

### orchestrate
Decompose a complex task into capsules, assign execution modes (parallel/sequential/pipeline), and define rollback rules. Used by faith-workflow.

### pipeline
A fixed-sequence production line from raw material to published output. A special case of workflow. Used by faith-pipeline.

### gate
A boolean pass/fail checkpoint. Does not modify — only judges. Used by faith-gate-check.

### compile
Transform a fuzzy natural-language task into a structured, verifiable prompt with constraints and examples. Used by faith-prompt-compile.

### distill
Extract the executable skeleton from a verbose document — keep the algorithm, discard exposition. Used by faith-skill-distill.

### deploy
Move a skill from development to production — validate syntax, resolve dependencies, test trigger routing. Used by faith-skill-deploy.

### scan
Traverse a directory tree and produce a structured inventory — classify files, detect duplicates, audit coverage. Used by faith-kb-scan.

---

## Skill Invocation Map

| Skill | Invocation | Called By |
|-------|:----------:|-----------|
| faith-humanizer | model-invoked | faith-pipeline, user |
| faith-gate-check | model-invoked | faith-pipeline, user |
| faith-truth-check | model-invoked | faith-humanizer boundary, user |
| faith-booknote | user-invoked | user only |
| faith-context-compress | user-invoked | user only |
| faith-index | user-invoked (router) | user only |
| faith-kb-scan | user-invoked | user only |
| faith-pipeline | user-invoked | user only |
| faith-pipeline-audit | user-invoked | user only |
| faith-prompt-compile | user-invoked | user only |
| faith-session | user-invoked | user only |
| faith-skill-architect | user-invoked | user only |
| faith-skill-deploy | user-invoked | user only |
| faith-skill-distill | user-invoked | user only |
| faith-skill-factory | user-invoked | user only |
| faith-skill-solution | user-invoked | user only |
| faith-viral-write | user-invoked | user only |
| faith-workflow | user-invoked | user only |

---

## Failure Mode Vocabulary

### premature completion
Ending a step before it is genuinely done. Defence: sharpen the completion criterion.

### no-op
An instruction that changes nothing because the model already does it by default. Test: does removing it change behaviour?

### duplication
The same meaning in more than one place. Costs maintenance and tokens.

### sediment
Stale layers that accumulate because adding feels safe and removing feels risky.

### sprawl
A skill too long even when every line is live. Cure: disclose reference behind pointers.

---

*version: 1.0.0 | ref: Matt Pocock writing-great-skills GLOSSARY.md pattern*