---
type: BASEPLATE_Stranger_Test_Log
Item_ID: D666D479-E93E-423D-AB62-D53C7DCB4274
title: "Inbox-Datestamper — Stranger Test Log"
baseplate_Product_Slug: "DS"
baseplate_Stranger_Model: "Fresh Claude instance (isolated subagent, no drafting context)"
baseplate_Run_Date: 2026-07-19
baseplate_Rerun_Count: 1
baseplate_Result: "run-1 PASS (canonical prompt, zero stack-should-answer). Passed on the first run — PF-6 front-run held."
Date_Added: 2026-07-19
Date_Modified: 2026-07-19
Needs_Processing: false
---

# Inbox-Datestamper — Stranger Test Log

> Gate 2 (`_baseplate-engine/05-GATES.md`). A fresh instance received only the frozen `Artifacts/` stack (eight files) and the canonical fixed prompt. This log lives at the cartridge level (outside the buildable `Artifacts/` folder) — a gate record, not a founding document the builder needs.

## Run 1 — 2026-07-19 — PASS

- **Stranger:** a fresh, context-isolated Claude instance (a subagent with no drafting history, no interview, no conversation) — anti-gaming: **not** the session that drafted the stack.
- **Fixed prompt:** the canonical `05-GATES.md` three-part prompt (restatement / phase-one plan / questions), used **verbatim** — deliberately *not* augmented with any "classify / count / be exhaustively adversarial" instruction (the Linkrot calibration lesson: an over-adversarial prompt inflates findings and makes the gate unreproducible).

### Criteria

| # | Criterion | Observed | Pass/Fail |
|---|-----------|----------|-----------|
| ST-1 | Restatement fidelity | Accurate and complete — captured both chores, the `.md`-only-Item_ID vs all-types-stamp asymmetry, birth-time with mtime fallback, the baseline/new-files-only **safety** rationale (link-safety, no surprise mass-rename), the coldness gate, every guardrail (backstop, collision-skip, idempotency, `_AI-` exclusion, dry-run, non-blocking lock, quiet logging), stdlib-only/zero-network, and the safety-first organizing principle | **pass** |
| ST-2 | Plan plausibility | Strong — test-first to green on AT-1..23, pure-predicate layer first (the equivalence-class risk), then I/O, then the §4 control flow in exact order; deployment correctly deferred to a Phase 2 owned by the operator per the runbook | **pass** |
| ST-3 | Question classification (the pass rule) | **Zero `stack-should-answer`.** 20 questions, all classified operator-only or already-answered — see below | **pass** |

### Question classification (all 20 → operator-only or already-answered)

**Operator-only** (a competent builder would naturally take these to the operator): concrete real paths behind the runbook's placeholders (Q1); the exact baseline file path (Q2); the `[OPERATOR_NAME]` for the license (Q3); confirmation/sign-off of the three tunable defaults (Q4); the guaranteed target OS and installed Python/tzdata (Q5, Q16); subprocess hardening choices for the `stat` call (Q6); the target repo location and whether to also produce tests/README (Q15); whether the separate cartridge-level gate is in the builder's scope (Q20).

**Already answered by the frozen stack** (the stranger asked for confirmation; the documents are determinate): config surface and the absence of extra CLI flags — §5 lists exactly `--dry-run`, `--backfill`, positional `VAULT_ROOT` (Q7, Q8); the four Item_ID-parsing edge cases (empty `.md`, quotes/nested/mismatched values, "first frontmatter key" placement, case-only normalization) — the §3.5 state machine is **total**: any input resolves to exactly one of added/filled/replaced/uppercased/left-untouched (Q9, Q10, Q11, Q12); logging destination and whether the `WARN abort:` / `SKIP (target exists)` strings are contract — §3.13 (stdout) plus AT-10/AT-11 pin them (Q13, Q14); silent mtime fallback on `%W==0` — ADR-001 states it (Q17); symlink handling — §3.1's parenthetical enumerates the exclusions (directories, symlink-to-dir), so symlink-to-regular-file is in scope (Q18); collision re-check is live ("exists on disk", §3.12; AT-11) (Q19).

**None** of the 20 would make two competent builders build the product *observably differently*. Under the charitable, reproducible evaluator stance (`05-GATES.md`), that is a clean pass.

**Result: PASS on run 1.**

## Finding for Baseplate (the PF-6 front-run worked)

Linkrot — the invented example — took **three** stranger-test runs to converge, because its first draft left behavioral equivalence classes implicit (resolution rules, case sensitivity, code-block/embed scope, output shape). That shakedown produced **generation standard 10** (behavioral completeness) and portfolio entry **PF-6**. This cartridge — the first **real** product — applied that lesson up front: the technical design's § 3 "Frozen behavioral semantics" enumerated every equivalence class (top-level, already-stamped, exclusion, the Item_ID state machine, coldness, birth-time resolution, ordering, atomicity, backstop, collision, logging) *before* the stranger saw it. The stranger's deepest questions were all pre-answered. **The engine's own maturation is now demonstrated, not just claimed: the second real cartridge passed Gate 2 on the first run.**

## Close-out note

One new failure mode *was* surfaced — not by the stranger test, but by the **Gate 1 consistency audit**, which caught two cross-zone dangling references (an `Artifacts/` document citing cartridge-level files the stranger never receives). Because the same class blocked Linkrot's first stranger run, it earns a portfolio entry: **PF-7 (cross-zone dangling reference)**. This is the portfolio catalog growing its **second** use-derived entry — REQ-M1 (the flywheel) demonstrated, not merely committed.
