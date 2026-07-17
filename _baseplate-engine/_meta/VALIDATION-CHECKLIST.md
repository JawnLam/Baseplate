---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: baseplate-meta-validation-checklist
title: "Baseplate Meta — Validation Checklist (manual gate walkthrough)"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
doc_type: baseplate-meta
role: validation-checklist
scope: subject-agnostic
---

# Validation Checklist (manual gate walkthrough)

> **Baseplate ships no validator by design (ONF-1): both gates are runnable by a human with a text editor and a second chat window, and the stranger test cannot be mechanized (it needs a fresh model instance). This file IS the tooling — the manual walkthrough of the two gates in `05-GATES.md`. Run it before shipping any stack, and to self-audit the Baseplate engine itself.**

## Gate 1 — Consistency audit (per stack, and self-applied to the engine)

Walk the frozen `Artifacts/` folder (for a product stack) or the engine tree (for a Baseplate self-audit). Every box is a hard check:

- [ ] **Requirement ↔ verification bijection.** Every numbered requirement has a verification; every verification maps back. No orphans either direction. (For the engine: every `OFR`/`ONF` is traced in `_meta/TRACEABILITY.md`, and every `PF-n` in `_portfolio/failure-catalog.md` appears as a "failure prevented.")
- [ ] **Cross-references resolve.** Every internal reference (file, section, requirement/PF ID) points at something that exists. A reference to a file that isn't present is a defect — ship the file or delete the reference.
- [ ] **Precedence declared and extractable** (stacks: `BASEPLATE_Selection_Record` field, S-3).
- [ ] **No unresolved placeholders** (`[TBD]`, `[UNVERIFIED]`, `<...>`) outside a declared open-questions section.
- [ ] **Dependency log complete** — every named external has a `_dependency-log.md` entry with method + date (PF-1).
- [ ] **Anti-staleness marks present** — every imported fact carries source + verification date (PF-2).
- [ ] **Non-goals present** in every requirements-bearing document.
- [ ] **Version strings agree** — `CHANGELOG.md` top entry, `VERSION.md`, `README.md`, and `CONTRIBUTING.md` all state the same version (release-identity single-sourcing).
- [ ] **Content zones honored** — files land in the zone `CONTRIBUTING.md § Content zones` declares; the `_portfolio/` grows-through-use zone is NOT gitignored; shipped `Example-Product-*` cartridges are tracked in full (the Operator-Private ignores carve them out).

Any unchecked box: fix, or operator-waive with a written reason. Then re-run.

## Gate 2 — Stranger test (per stack — the ship gate)

- [ ] The cartridge's `Artifacts/` folder is frozen; the stranger receives *only* that folder.
- [ ] A **fresh** model instance (no conversation history; ideally a different model *family* than the drafter — P1) got the **verbatim** fixed prompt from `05-GATES.md` (never augmented with "classify/count/be adversarial" — see § Evaluator stance).
- [ ] **ST-1 Restatement fidelity** — the restatement matches operator intent.
- [ ] **ST-2 Plan plausibility** — the phase-one plan is consistent with the stack's own sequencing/gates.
- [ ] **ST-3 Question classification** — every question is `operator-only` or `stack-should-answer`, read **charitably**. **Any `stack-should-answer` question = ship block:** fix the stack, rerun with a fresh instance.
- [ ] The run is logged in a `BASEPLATE_Stranger_Test_Log` (stranger model + date, verbatim outputs, per-criterion results, question-classification table, rerun count).

## Overall

- [ ] Gate 1 clean (or every finding waived in writing).
- [ ] Gate 2 passed (no `stack-should-answer` questions) with a fresh instance.
- [ ] Any new failure mode surfaced is added to `_portfolio/failure-catalog.md` and traced in `_meta/TRACEABILITY.md` in the same change.
