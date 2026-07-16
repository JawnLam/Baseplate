---
Item_ID: "UUID-OR-SLUG"
type: KEEL_Stranger_Test_Log
title: "<Product> — Stranger Test Log"
keel_Product_Slug: ""
keel_Stranger_Model: ""     # model family/version of the fresh instance (ideally different family than drafter)
keel_Run_Date:
keel_Rerun_Count: 0
keel_Result: ""             # pass | blocked
Date_Added:
Date_Modified:
Needs_Processing: false
AI_Instructions: ""
---

# <Product> — Stranger Test Log

> The Typed record of the per-cartridge ship gate (`05-GATES.md` Gate 2). A fresh model instance receives only the frozen `Artifacts/` folder and the fixed prompt; the evaluator scores below. **Any `stack-should-answer` question blocks ship.** Mirrors OVE's golden-session log pattern.

## Run

- **Stranger model / date:** `<model family; different family than drafter if possible>` / `<YYYY-MM-DD>`
- **Fixed prompt (verbatim, never augmented):** *"You have been handed the complete founding documents for a product. Produce three things: (a) a one-page restatement of what is being built and why; (b) your plan for the first phase of work; (c) every question you would need answered before starting."*
- **Stranger outputs (a)/(b)/(c):** *archived verbatim below or linked.*

## Criteria

| # | Criterion | Observed | Pass/Fail | Triage |
|---|-----------|----------|-----------|--------|
| ST-1 | Restatement fidelity — (a) matches operator intent | | | |
| ST-2 | Plan plausibility — (b) consistent with the stack's own sequencing/gates | | | |
| ST-3 | Question classification — no `stack-should-answer` question remains | | | |

## Question classification table (ST-3 — the pass rule)

| Question from output (c) | `operator-only` or `stack-should-answer` | Disposition |
|--------------------------|--------------------------------------------|-------------|
|                          |                                            |             |

*Any `stack-should-answer` row = ship block: fix the stack, rerun with a fresh instance. Two consecutive reruns blocked by the same question class → candidate `_portfolio/failure-catalog.md` entry.*

## Naming

- **Filename:** `stranger-test-log.md` in the cartridge `Artifacts/`.
- **Type value:** `type: KEEL_Stranger_Test_Log`.
