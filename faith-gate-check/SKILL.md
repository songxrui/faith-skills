---
name: faith-gate-check
description: Binary quality gate — check language quality, task completion, and publish readiness. Returns PASS or FAIL only, never edits content. Use when the user says "能发了吗", "过一遍质量门禁", "发布前检查", "gate check", or when faith-pipeline reaches Phase 7. Not for AI-pattern detection (→ faith-humanizer) or content diagnosis (→ dbs-content).
disable-model-invocation: true
argument-hint: "[content-to-check]"
version: "3.0.0 | R3: mattpocock deep reconstruction | 2026-07-06 | model: agnostic"
---

# faith-gate-check — 统一质量门禁

> **gate** — binary PASS/FAIL only. No improvement suggestions. No quality scores. A gate is a result, not a service.
> If content is missing or incomplete → refuse to run. Output: "Content missing or incomplete — cannot execute gate check."

## Three gates

### Gate 1: Language quality

| Check | Method | Pass condition |
|-------|--------|---------------|
| AI traces | Scan banned words (本质上/赋能/抓手/闭环/底层逻辑/综上所述/需要更多的研究/引起重视) | Zero hits |
| Template patterns | Scan "首先…其次…最后", "不是…而是…" chains, "值得注意的是…" | ≤2 occurrences total |
| Platform compliance | Characters / paragraphs / style match target platform rules | Compliant |

### Gate 2: Task completion

| Check | Method | Pass condition |
|-------|--------|---------------|
| Structure | Has complete opening / body / closing — no obvious truncation | Yes |
| Evidence | Each core claim has ≥1 supporting argument or case | ≥1 per claim |
| Traceability | Cited research/data has source (book/DOI/URL) or marked "personal experience" | All cited |

### Gate 3: Publish readiness

| Check | Method | Pass condition |
|-------|--------|---------------|
| Title count | Body has ≥1 title (pass if present; if none, mark "no title drafted") | ≥1 |
| Format clean | No abnormal characters, broken code blocks, unclosed HTML | Clean |
| Source trace | Every data point/quotation either has a source or is marked "推断" | All traced |

## Output

```
## Quality Gate Report

### Gate 1: Language Quality
- AI traces: X violations → ✓ / ✗
- Template patterns: X occurrences → ✓ / ✗
- Platform compliance → ✓ / ✗

### Gate 2: Task Completion
- Structure complete → ✓ / ✗
- Evidence supported → ✓ / ✗
- Citations traceable → ✓ / ✗

### Gate 3: Publish Readiness
- Title → ✓ / ✗
- Format clean → ✓ / ✗
- Source traced → ✓ / ✗

### Final: ✓ ALL GATES PASS — READY TO PUBLISH
        ✗ Gate [X] FAILED — DO NOT PUBLISH
```

## Failure modes

| Failure | Fallback |
|---------|----------|
| Content missing or blank | Refuse to run, no gate report |
| Target platform unspecified | Only run Gate 2, mark "platform info missing, skipped platform check" |
| Single article targets multiple platforms | Judge by strictest rule |

---
*version: 3.0.0 | leading word: gate-pass-fail | ref: mattpocock/skills writing-great-skills*