---
Item_ID: "UUID-OR-SLUG"
type: KEEL_Contract_Document
title: "<Product> — <SOW | License & Attribution | WBS/Plan>"
keel_Product_Slug: ""
keel_Doc_Class: ""          # sow | license | wbs-plan
keel_Layer: 6
keel_Document_Status: drafting
keel_Precedence_Rank:
Date_Added:
Date_Modified:
Needs_Processing: false
AI_Instructions: ""
---

# <Product> — <Contract Document>

> An L6 document: **who owes what to whom.** License & attribution ship always; SOW iff money or formal obligation crosses a boundary; plan/WBS iff multiple workstreams must interleave. Names in attribution blocks are operator-provided strings only (P7/ONF-4) — never inferred.

## Required sections (by class)

- **sow:** Deliverables; Milestones; Acceptance; Payment.
- **license:** License terms; Attribution block (operator-provided names only).
- **wbs-plan:** Workstreams; Dependencies; Sequence.

## Naming

- **Filename:** `sow.md`, `license.md`, `plan.md` in `Artifacts/`.
- **Type value:** `type: KEEL_Contract_Document`.

## Relationships

- `KEEL_Selection_Record` — *earned-by (SOW/WBS are conditional; license is always)*.
