---
name: faith-truth-check
description: Verify factual claims — cross-check data sources, assess reliability tiers, flag unverifiable assertions. Use when the user says "核实一下", "这个数据是真的吗", "fact check", or before publishing content with claims. Not for content quality (→ faith-gate-check) or style (→ faith-humanizer).
disable-model-invocation: true
argument-hint: "[claim-or-content]"
version: "3.0.0 | R3: mattpocock deep reconstruction | 2026-07-06 | model: agnostic"
---

# faith-truth-check — 事实核查

> **verify** — cross-check every claimable fact, assign a reliability tier, flag what can't be confirmed.

## Reliability tiers

| Tier | Label | Definition |
|------|-------|-----------|
| **T1** | Confirmed | Verified against ≥2 independent authoritative sources |
| **T2** | Likely | Matches 1 authoritative source or multiple consistent non-authoritative sources |
| **T3** | Inference | Logical deduction from confirmed facts — plausible but unverified |
| **T4** | Unverifiable | No source found, or sources contradict |
| **T5** | Suspect | Evidence contradicts the claim |

## Process (4 steps)

### Step 1: Extract claims
Scan the content and extract every factual assertion — numbers, dates, names, causal claims, quotations.
**Completion criterion**: Output a claims list — each claim quoted verbatim with its location (paragraph N).

### Step 2: Source search
For each claim, search for verifying sources. Use exa-search for web verification, context7 for code/docs.
**Completion criterion**: Each claim annotated with: [sources found: N] + [best source title/URL].

### Step 3: Assign tiers
Apply the 5-tier system to each claim.
**Completion criterion**: Every claim has a tier assignment, with a one-line rationale for T3-T5.

### Step 4: Output report
**Completion criterion**: Report includes — summary (total claims / T1-T5 breakdown) + per-claim details (claim + tier + rationale + source) + recommendations (claims to remove/hedge/keep).

## Output format

```
## Truth Check Report

### Summary
Total claims: N | T1: N | T2: N | T3: N | T4: N | T5: N

### Per-claim
| # | Claim | Tier | Rationale | Source |
|---|-------|------|-----------|--------|
| 1 | "..." | T2 | ... | [source] |

### Recommendations
- Remove: claims that are T5
- Hedge: claims that are T3/T4 — add "推断" or "据称" qualifiers
- Keep: T1/T2 claims
```

## Failure modes

| Failure | Fallback |
|---------|----------|
| No claims found in content | Output "No factual claims detected — content is opinion/narrative only" |
| Search tools unavailable | Assign all claims T4, flag "unable to verify — search tools not available" |
| Claim is vague ("很多人认为") | Flag as "unverifiable due to vagueness — specify who/how many" |

---
*version: 3.0.0 | leading word: verify-tier-report | ref: mattpocock/skills writing-great-skills*