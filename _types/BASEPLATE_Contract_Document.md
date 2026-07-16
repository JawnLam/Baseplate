---
Item_ID: "UUID-OR-SLUG"
type: BASEPLATE_Contract_Document
title: "<Product> — <SOW | License & Attribution | WBS/Plan>"
baseplate_Product_Slug: ""
baseplate_Doc_Class: ""          # sow | license | wbs-plan
baseplate_Layer: 6
baseplate_Document_Status: drafting
baseplate_Precedence_Rank:
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
- **Type value:** `type: BASEPLATE_Contract_Document`.

## Relationships

- `BASEPLATE_Selection_Record` — *earned-by (SOW/WBS are conditional; license is always)*.
