

# Context Compressor

Build compressed but complete context summaries for long-running CodeWhale
sessions. The goal is to keep context lean without losing anything the
model needs to make correct decisions.

## Context Layer Model

CodeWhale uses a layered context architecture:

| Layer | Content | Compression | Update Trigger | Risk if Lost |
|-------|---------|-------------|----------------|--------------|
| L0: Task Capsule | Current goal, constraints, DoD, forbidden actions | Never compress | Task change | Task drift |
| L1: Architecture Map | Crate tree, entry points, config, test commands | Path+role table | Architecture change | Slower orientation |
| L2: Diff Digest | Files changed, risk level, verification status | File+risk+verify | Each commit | Missed regression |
| L3: Test Baseline | pass/fail count, failing test names | Counts+names | Each test run | False confidence |
| L4: Skills Index | Available skills, triggers, I/O shape | Name+trigger | Skill install/remove | Tool misuse |
| L5: Engineering Memory | Stable rules, pitfalls, forbidden zones | Never compress | Discovery | Repeat mistakes |

## When to Compress

- **Soft seam L1 (192K tokens)**: Compress L2 (diff digest) and L3 (test
  baseline)
- **Soft seam L2 (384K tokens)**: Also compress tool outputs into summaries
- **Soft seam L3 (576K tokens)**: Also compress older turns into briefings
- **Hard cycle (768K tokens)**: Full checkpoint: archive current session,
  build carry-forward with task capsule + architecture map + briefing

## SESSION_MEMORY_PROMPT

When the model is asked to produce a self-summary, inject this prompt
template. It was extracted from Claude cookbook's `session_memory_compaction.ipynb`
and measured at **58.6% token reduction** (208K 鈫?86K on a 5-ticket customer
support task with 35 tool calls).

```
<analysis-instructions>
Analyze the conversation above and answer these questions:
1. What was the user's original intent? (the goal, not the steps)
2. What work was completed? What succeeded and what failed?
3. What corrections did the user make? (explicit corrections, rejections,
   or direction changes)
4. What is currently active? (in-progress work not yet finished)
5. What tasks are pending? (promised but not yet started)
6. What key details must be preserved? (identifiers, error messages,
   specific values, user preferences)
</analysis-instructions>

<summary-format>
Produce a <summary> block with exactly these sections:
- **User Intent**: One sentence on the original goal.
- **Completed Work**: Bullet list of finished items, each annotated
  with [SUCCESS] or [FAILED].
- **Errors & Corrections**: Every error encountered, the exact error
  message, and the fix applied. Include user corrections word-for-word.
- **Active Work**: What was in progress at the moment of compaction.
  Include file paths and line numbers if known.
- **Pending Tasks**: Bullet list of tasks the user asked for but that
  haven't started.
- **Key References**: Concrete identifiers 鈥?file paths, function names,
  config keys, commit hashes, test names, error codes.
</summary-format>

<preserve-rules>
- ALWAYS keep identifiers verbatim: function names, file paths, config
  keys, test names, error codes.
- ALWAYS keep error messages verbatim: do not paraphrase or shorten.
- ALWAYS keep user corrections: if the user said "no, don't do X" or
  "instead do Y," preserve the exact instruction.
- ALWAYS keep specific values: thresholds, counts, version numbers,
  measured timings.
</preserve-rules>

<compression-rules>
- Weight recent messages more heavily than older ones.
- Omit pleasantries, acknowledgments, and filler.
- Keep each summary section under 500 words.
- Prefer concrete facts over narrative description.
</compression-rules>
```

## Proactive (Instant) Compaction

Traditional compaction makes the user wait while the model generates a
summary (~41s measured). Proactive compaction generates the summary in a
background task *before* the threshold is reached, so it's ready instantly.

```
Traditional: [threshold hit] 鈫?[generate summary ~41s] 鈫?[user waits] 鈫?[apply]
Proactive:   [background warmup] 鈫?[threshold hit, summary ready] 鈫?[apply 0s]
```

**How it works in DS TUI**:
1. A background compaction task monitors token usage.
2. When usage crosses a "warmup" threshold (e.g., 70% of the hard limit),
   the task requests a summary from the model using the SESSION_MEMORY_PROMPT.
3. `add_cache_control()` places `cache_control={"type":"ephemeral"}` on the
   last user message so the background request hits the same cache as the
   main conversation, reducing background summary cost by ~80%.
4. When the hard threshold is reached, the pre-built summary is applied
   immediately 鈥?zero user-facing latency.

**Requirements for proactive mode**: background thread/task architecture,
model API that supports `cache_control`. This is an R3 capability for DS TUI.

## DS TUI Compaction Infrastructure

DS TUI has real compaction code. The skill references below point to
actual source files:

| Component | Location | Role |
|-----------|----------|------|
| Compaction engine | `crates/tui/src/compaction.rs` | Summary generation, anchor injection, carry-forward |
| Wire-level compaction | `crates/tui/src/client/chat.rs:937` | `compact_tool_result_for_wire()` 鈥?truncates large tool outputs |
| Compaction config | `crates/tui/src/tui/app.rs:4508` | `compaction_config()` 鈥?returns `CompactionConfig` |
| Config action | `crates/tui/src/tui/app.rs:4587` | `UpdateCompaction(CompactionConfig)` 鈥?applies new config |
| `/anchor` command | `crates/tui/src/compaction.rs:1030` | `read_workspace_anchors()` 鈥?pins facts across compaction |
| Anchor file | `<workspace>/.deepseek/anchors.md` | User-anchored facts, one per line |
| Orphaned tool safety | `crates/tui/src/client/chat.rs:1314` | Safety net: after compaction, strips orphaned tool_calls |

### `/anchor` Command

Users can pin facts that survive compaction:
```
/anchor CJK paths break GNU ld on Windows 鈥?always use tempdir_in()
```

Anchored facts appear in a dedicated section of the compaction summary and
are never dropped. They serve the same role as L5 (Engineering Memory) but
are user-managed rather than model-discovered.

## How to Build Each Artifact

### Task Capsule (L0)

```markdown
## Task Capsule

**Goal:** [one sentence 鈥?what are we trying to achieve?]
**Constraints:** [what must NOT be changed, touched, or assumed?]
**DoD:** [concrete, verifiable criteria for "done"]
**Risk Tier:** [R0-R4]
**Phase:** [current phase / total phases]
**Blocked By:** [what's blocking progress?]
**Next Action:** [single concrete next step]
```

### Diff Digest (L2)

```markdown
## Diff Digest

**Round:** [which round of changes]
**Files:**
| File | Change | Risk | Verified? |
|------|--------|------|-----------|
| path/to/file.rs | [what changed] | R0-R4 | [yes/no] |

**Test Delta:**
- Before: [X passed / Y failed]
- After:  [X passed / Y failed]

**Rollback:** [how to undo all changes in this round]
```

### Test Digest (L3)

```markdown
## Test Digest

**Command:** `cargo test --workspace`
**Result:** [X passed / Y failed / Z ignored]
**Date:** [timestamp]

### Failing Tests
| Test Name | Failure Type | Root Cause | Status |
|-----------|-------------|------------|--------|
| test_name | assertion/panic/timeout | [cause] | known/new/fixed |

### Test Baseline Trend
| Date | Passed | Failed | Delta |
|------|--------|--------|-------|
| ... | ... | ... | ... |
```

## Rules

1. **Never compress L0 (task capsule) or constraints.** Losing the goal
   causes task drift.
2. **Never paraphrase evidence.** "Test X failed" is evidence. "Tests look
   mostly okay" is a lie.
3. **Always include rollback info.** If something goes wrong, the next
   engineer needs to undo it.
4. **Prefer specific over general.** "Fixed skills isolation by adding
   injectable home_dir param" > "Fixed tests."
5. **Update digests in real time.** Don't wait until the end of a round 鈥?   stale digests are worse than no digests.

## What NOT to Compress

Even at the hardest compression level, these must survive:

- Current task goal and DoD
- Active constraints and forbidden actions
- Protected paths list
- Failing test names and their error messages
- `cargo check` / `cargo test` status
- User's explicit instructions and corrections
- Anchored facts (`/anchor` entries)

## Performance Benchmarks

From Claude cookbook measurements (reproducible):

| Strategy | Metric | Value |
|----------|--------|-------|
| Automatic compaction | Token reduction | 58.6% (208K 鈫?86K) |
| Proactive compaction | Summary wait time | 0s (vs 41s traditional) |
| cache_control on summary request | Background cost reduction | ~80% |
| Prompt caching (cache hit) | Input token cost | 0.1x base price |
| Prompt caching (cache hit) | Latency | 3.3x speedup (4.89s 鈫?1.48s) |

## Related

- `crates/tui/src/compaction.rs` 鈥?compaction engine with anchor support
- `crates/tui/src/client/chat.rs` 鈥?wire-level tool result compaction + orphaned tool safety net
- `crates/tui/src/tui/app.rs` 鈥?`compaction_config()` and `UpdateCompaction` action
- `task-capsule-builder` skill 鈥?specialize for task capsules
- `session-memory` skill 鈥?persist learnings before compaction; recall after restoration
- `ds-tui-iteration-engineer` skill 鈥?uses these templates
