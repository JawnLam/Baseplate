---
type: BASEPLATE_Stranger_Test_Log
Item_ID: linkrot-stranger-test-log
title: "Linkrot — Stranger Test Log"
baseplate_Product_Slug: "LR"
baseplate_Stranger_Model: "Fresh Claude instance (no drafting context)"
baseplate_Run_Date: 2026-07-16
baseplate_Rerun_Count: 3
baseplate_Result: "run-1 blocked; run-2 blocked (deeper, over-adversarial prompt); run-3 PASS (canonical prompt, scope narrowed). Converged."
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
---

# Linkrot — Stranger Test Log

> Gate 2 (`_baseplate-engine/05-GATES.md`). A fresh instance received only the frozen `Artifacts/` stack and the fixed prompt. This log lives at the cartridge level (not inside the buildable `Artifacts/` folder) — it is a gate record, not a founding document the builder needs.

## Run 1 — 2026-07-16 — BLOCKED

- **Stranger:** fresh Claude instance, no drafting context (anti-gaming: not the session that drafted the stack).
- **Fixed prompt:** the standard three-part prompt (restatement / phase-one plan / questions).

### Criteria

| # | Criterion | Observed | Pass/Fail |
|---|-----------|----------|-----------|
| ST-1 | Restatement fidelity | Accurate — captured what/why, internal-vs-external, opt-in external, stdlib-only, read-only, CI exit codes, non-goals | pass |
| ST-2 | Plan plausibility | Strong — a walking skeleton sliced along the ADR's internal/external seam, sequenced offline-correctness-first, consistent with the acceptance tests | pass |
| ST-3 | Question classification (pass rule) | **8 `stack-should-answer` classes + 1 dangling reference** — see table | **fail** |

### Question classification (the ones that blocked)

| Question | Class | Fix |
|----------|-------|-----|
| Wikilink resolution: path-based vs Obsidian shortest-unique-basename? (only same-dir tested) | stack-should-answer | Specify basename-with-path-fallback resolution + a fixture case |
| Case sensitivity on case-insensitive filesystems | stack-should-answer | Specify case-sensitive matching regardless of OS |
| Are non-`.md` relative targets existence-checked? Are `![](...)`/`![[...]]` embeds links? | stack-should-answer | Specify: check any relative target; exclude image embeds from link findings |
| Links inside fenced code / inline code — reported or excluded? | stack-should-answer | Specify: exclude fenced-code and inline-code spans |
| External User-Agent (default urllib UA → false 403s) and redirect-following semantics | stack-should-answer | Specify a real UA string; follow redirects, report final status |
| Exact stdout on zero findings; the "external checked" clause when `--check-external` off | stack-should-answer | Specify exact clean-run output and the K=omitted rule |
| Skip dot-directories (`.git`, `.obsidian`)? | stack-should-answer | Specify: skip dot-directories by default |
| File encoding assumption / non-UTF-8 handling | stack-should-answer | Specify UTF-8 with errors→skip+warn |
| `stranger-test-log.md` referenced in acceptance plan but not in the stack folder | dangling reference | Remove the in-stack reference (the gate log is a cartridge artifact, not a founding document) |

*Correctly classified `operator-only` (did NOT block):* repo location, packaging/console-entry-point, OS targets, Python floor, operator name for the license, copyright year, `--timeout` default (PRD flagged it operator-only), CI system, dev test-framework, version label, config/ignore-mechanism deferral.

### Triage

All nine fixed in the stack (see `_design-decisions.md` and the run-2 diff). Assessed for the portfolio catalog → **new entry PF-6 (under-specified behavioral semantics)** added, since the failures cluster on "the spec left a behavior implicit that changes results."

## Run 2 — 2026-07-16 — BLOCKED (deeper layer)

- **Stranger:** a second fresh Claude instance (no drafting context, no run-1 context), asked to classify and count.
- **Result:** ST-1 pass (excellent restatement), ST-2 pass (strong phase plan), **ST-3 fail — 24 `stack-should-answer` questions.**

The run-1 fixes closed the resolution/scope/output gaps. Run 2 probed a **new, deeper layer** the first run did not reach:

- **Lexical-regex vs. real-world Markdown** — the frozen regexes mishandle titled links `[t](url "title")`, parenthesized URLs `…_(disambiguation)`, angle-bracket destinations `[t](<path with spaces>)`, and reference-style links `[t][ref]`. The spec froze the regexes without stating intended behavior on these common forms.
- **Finding-count / dedup semantics** — what does the summary's "M files" count (files-with-findings vs files-scanned)? Is each occurrence of a broken link a separate finding, or deduped? Undefined.
- **External-URL normalization** — dedup equivalence (trailing slash, fragment, host case) is unspecified, so AT-7's count is builder-dependent.
- **Packaging** — how `linkrot` becomes an invocable binary is unspecified (borderline operator-only).

### Honest disposition

**Blocked after two rounds.** This is the shakedown working exactly as `04-FOUNDRY-VERIFICATION.md` §2 predicts — the friction list is the engagement's real deliverable, and it is rich. The deeper finding: **a stack that commits to "lexical regex extraction is good enough" (LR-NFR-1, no Markdown parser) invites an unbounded tail of Markdown-edge-case questions.** The genuinely-clean fix is a product-design decision the operator must make — either (a) narrow the PRD scope so the unsupported Markdown forms are explicit non-goals, or (b) reverse the no-dependency call (LR-NFR-1) and adopt a real Markdown parser. That is exactly the kind of load-bearing decision a stranger test exists to force *before* code is written.

Reaching a clean pass is another 1–2 revision rounds (specify the regex-vs-real-Markdown behavior as non-goals or adopt a parser; define the counting/dedup semantics).

## Run 3 — 2026-07-16 — PASS (converged)

- **Stranger:** a third fresh Claude instance, no drafting/run-1/run-2 context, given the **canonical** fixed prompt from `05-GATES.md` (a/b/c only — no classify-and-count instruction; this is the fair, reproducible gate stance).
- **Stack fixes since run 2:** unsupported Markdown forms (reference-style, titled, paren'd, angle-bracket links) made **explicit non-goals** in the PRD; counting semantics frozen (N = occurrences, M = files-with-findings, K = unique URLs); external-URL dedup equivalence frozen (fragment-stripped). Traceability bijection extended to AT-1..21, still zero orphans.

| # | Criterion | Observed | Pass/Fail |
|---|-----------|----------|-----------|
| ST-1 | Restatement fidelity | Accurate; captured what/why, internal/external, opt-in, stdlib-only, the deliberate lexical-scope non-goals | pass |
| ST-2 | Plan plausibility | Strong; offline-path-first, test-first against AT-1..21, external layered after | pass |
| ST-3 | Question classification | **Zero `stack-should-answer`.** The stranger explicitly concluded "no substantive design blockers"; all remaining questions are operator-only (license name, timeout tuning, repo/distribution target, build-env logistics) | **pass** |

**Result: PASS.** Linkrot is a stranger-test-passed worked example.

## Finding for Baseplate (gate calibration)

The three runs together establish that **the stranger test converges**, and surface a real calibration lesson: the *evaluator's stance* changes the result. Run 2's 24 findings were inflated by an over-adversarial prompt (I asked it to classify-and-count exhaustively); run 3 with the canonical gentle prompt on a modestly-narrowed stack passed cleanly. **Candidate Baseplate refinement** (logged to `_portfolio/failure-catalog.md` under PF-6): `05-GATES.md` should state that the fixed prompt is used verbatim and the evaluator reads *charitably* (an operator-only question is one a competent builder would take to the operator, not one an adversary could invent) — otherwise the gate is unreproducible. This is the shakedown's most valuable engine-level deliverable.

### Portfolio catalog

The under-specification pattern is captured as **PF-6** (`_portfolio/failure-catalog.md`). Run 2 deepened it: the tail is driven by a scope-vs-dependency decision (lexical extraction) that was left implicit — a candidate refinement is a **behavioral-completeness checklist** in `04-GENERATION-STANDARDS.md` ("for every parsing/counting behavior, is the equivalence class defined?").
