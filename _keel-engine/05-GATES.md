---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: keel-engine-05-gates
title: "Keel Engine — 05 Gates"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
doc_type: keel-engine
role: gates
scope: subject-agnostic
---

# 05 — GATES (consistency audit, stranger test, revision)

> **A stack ships only after it passes the consistency audit and then the stranger test. The audit checks the stack's form; the stranger test executes its function — a fresh instance actually tries to build from it. Both are manually runnable with no tooling (ONF-1). Any failure: fix, or operator-waive with a reason, and assess as a candidate `_portfolio/failure-catalog.md` entry (OFR-11).**

## Gate 1 — Consistency audit (run first)

Mechanical checklist over the frozen `Artifacts/` folder. Every item is a hard check:

- [ ] **Requirement ↔ verification bijection.** Every numbered requirement has a verification in the traceability document; every verification maps back to a requirement. No orphans either direction.
- [ ] **Cross-references resolve within `Artifacts/`.** Every internal reference (document, section, requirement ID) points at something present in the folder. The stranger receives only this folder (ONF-5).
- [ ] **Precedence declared and extractable.** The stack's precedence declaration exists as a field (S-3), not buried in prose.
- [ ] **No unresolved placeholders** (`[TBD]`, `[UNVERIFIED]`, `<...>`) outside a declared open-questions section.
- [ ] **Dependency log complete.** Every named external (tool, package, API, dataset, service) in any document has an entry in `_dependency-log.md` with method-of-check and date.
- [ ] **Anti-staleness marks present.** Every imported fact carries its source + verification date.
- [ ] **Non-goals present** in every requirements-bearing document.

Any unchecked box: fix, or operator-waive with a written reason in `_decisions.md`. Then re-run. Advance each document to `keel_document_status: audited`.

## Gate 2 — The Stranger Test (the ship gate — the heart)

Operationalizes the stack's defining claim: **buildable by a stranger with no further operator input.**

### Procedure

1. **Freeze** the cartridge's `Artifacts/` folder. The stranger receives *only* this folder — no interview, no decisions log, no conversation.
2. **A fresh model instance** — no conversation history, ideally a *different model family* than the one that drafted (substrate-agnosticism check) — receives the folder with this fixed prompt, verbatim, never augmented with hints:
   > *"You have been handed the complete founding documents for a product. Produce three things: (a) a one-page restatement of what is being built and why; (b) your plan for the first phase of work; (c) every question you would need answered before starting."*
3. **The evaluator** (the operator, or an agent that has read the stack but not the conversation) scores:
   - **ST-1 Restatement fidelity** — (a) matches the operator's intent. A misreading is a stack defect, not a stranger defect.
   - **ST-2 Plan plausibility** — (b) is a reasonable phase-one plan consistent with the stack's own sequencing and gates.
   - **ST-3 Question classification — the pass rule** — classify every question in (c) as `operator-only` (credentials, taste, business decisions, information genuinely unavailable at ship time) or `stack-should-answer`. **Any `stack-should-answer` question is a ship block:** fix the stack, rerun with a fresh instance.
4. **Log** the run in a `KEEL_Stranger_Test_Log`: stranger model + date, verbatim outputs archived, per-criterion results, the question-classification table, and the rerun count.

### Anti-gaming rules

- The drafting session never plays the stranger.
- Reruns use fresh instances.
- The fixed prompt is used **verbatim** and is never augmented — in particular, do not instruct the stranger to "classify, count, or be exhaustively adversarial." That inflates `stack-should-answer` findings and makes the gate unreproducible.
- Two consecutive reruns blocked by the *same* question class → candidate `_portfolio/failure-catalog.md` entry.

### Evaluator stance (reproducibility)

Classify **charitably**: a question is `operator-only` if a *competent builder* would naturally take it to the operator (a name, a credential, a taste call, a repo/deploy choice, a genuinely-unavailable fact), and `stack-should-answer` only if the documents' silence would make two competent builders build the product *observably differently*. Do not count questions an adversary could invent but a real builder would not ask (pedantic edge cases the spec's non-goals already bound). The gate measures buildability, not exhaustiveness. *(Calibration lesson from the Linkrot shakedown: an over-adversarial evaluator prompt produced 24 findings on a stack that a charitable canonical-prompt run passed cleanly.)*

## Revision (REVISE-STACK — OFR-14)

A shipped stack meets reality and must change. Do not hand-edit ad hoc. Run the change-request protocol:

1. **Change spec** — what reality contradicted, and which requirements/decisions it invalidates. Record it (it feeds the portfolio catalog).
2. **Acceptance deltas** — what "fixed" means, as verification changes.
3. **Affected-document edits** — edit only the documents the change touches; keep IDs stable.
4. **Re-run only the affected gates** — the consistency audit over the touched documents, and the stranger test if the change altered what the stranger must understand.
5. **Close-out** — state write + portfolio-catalog update.

## AUDIT-STACK

For a stack of any origin (even one Keel did not produce): run Gate 1 and Gate 2 as read-only checks and emit a findings report. Do not edit the stack — report what fails, classified by gate criterion.

## Close-out (every session type)

End with a state write to `_design-state.md` (or a loud sandbox declaration), and — at cartridge close — an update to `_portfolio/failure-catalog.md` with any new failure mode the engagement surfaced (OFR-12/13).
