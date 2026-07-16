---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: baseplate-engine-bootstrap-new-stack
title: "Baseplate Engine — Bootstrap New Stack"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
doc_type: baseplate-engine
role: cartridge-bootstrapping-prompt
scope: subject-agnostic
---

# BOOTSTRAP A NEW STACK (open a product cartridge)

> **The operator wants a founding-document stack for a new product. This is your execution plan. Elicit before you generate. One question at a time.**

## Step 1 — Open the cartridge

Create `{ROOT}/<Product-Name>/` (Title-Case-Hyphenated), with:

```
<Product-Name>/
├── _ov-manifest.md       # product identity (from the interview)
├── _design-state.md      # cartridge phase + open threads
├── _design-decisions.md  # locked decisions incl. every selection inclusion/exclusion (OFR-4)
├── _schema-draft.md      # the stack's ID scheme + precedence declaration (S-3)
├── _product-interview.md # the PI-1..PI-12 record (OFR-1/2)
├── _dependency-log.md    # dependency verifications (OFR-6)
├── Sessions/
└── Artifacts/            # THE STACK — the only folder the stranger receives
```

## Step 2 — Interview (`02-ELICITATION.md`)

Run the product interview one question at a time. Capture PI-1..PI-12 in `_product-interview.md`. Do not select or draft mid-interview. Reflect the picture back and confirm before proceeding.

## Step 3 — Select (`03-SELECTION.md`)

Walk the class registry against the interview answers. Record every inclusion and exclusion with its triggering PI-answer in a `BASEPLATE_Selection_Record`. Lock the ID scheme and precedence declaration. **Selection locks before any drafting (F8).**

## Step 4 — Generate (`04-GENERATION-STANDARDS.md`)

Draft each selected document from its structural Type and required sections, in dependency-layer order. Verify every named dependency as you write it (log each). End each document with its internal consistency pass; advance to `internally-consistent`.

## Step 5 — Gate (`05-GATES.md`)

Run the consistency audit, then the stranger test with a fresh instance. Any `stack-should-answer` question blocks ship — fix and rerun. Log the stranger test in a `BASEPLATE_Stranger_Test_Log`.

## Step 6 — Close-out

State write; append any new failure mode to `_portfolio/failure-catalog.md`; mark the stack `shipped`.

## Quality gates before the stack ships

- [ ] Interview PI-1..PI-12 captured (one at a time)
- [ ] Selection locked with inclusions AND exclusions recorded
- [ ] Every selected document `internally-consistent`
- [ ] Consistency audit clean (or every finding waived in writing)
- [ ] Stranger test passed (no `stack-should-answer` questions) with a fresh instance
- [ ] Dependency log complete; anti-staleness marks present
- [ ] Portfolio catalog updated at close

## Failure modes to avoid (load the full catalog at session start)

Dependency fabrication; stale-context carryover; orphan requirements; gold-plating; silent assumption absorption. See `_portfolio/failure-catalog.md`.
