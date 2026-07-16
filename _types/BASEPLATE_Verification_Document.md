---
Item_ID: "UUID-OR-SLUG"
type: BASEPLATE_Verification_Document
title: "<Product> — <Test Plan | Acceptance Test Plan | Traceability Matrix>"
baseplate_Product_Slug: ""
baseplate_Doc_Class: ""          # test-plan | acceptance-test-plan | traceability-matrix
baseplate_Layer: 4
baseplate_Document_Status: drafting
baseplate_Precedence_Rank:
Date_Added:
Date_Modified:
Needs_Processing: false
AI_Instructions: ""
---

# <Product> — <Verification Document>

> An L4 document: **how we know it works.** L4 verifies L1, never L3 — acceptance written against implementation cannot catch design errors. The traceability matrix is unconditional (D-7): it holds the requirement↔verification bijection the consistency audit checks.

## Traceability matrix (required for the traceability-matrix class)

*No orphans in either direction — every requirement has a verification; every verification maps back to a requirement.*

| Requirement ID | Design element (if any) | Verification | Result |
|----------------|-------------------------|--------------|--------|
|                |                         |              |        |

## Acceptance criteria (acceptance-test-plan class)

*The definition of done — pass/fail conditions and sign-off. Gherkin Given/When/Then where it bridges from L1 acceptance criteria.*

## Test strategy (test-plan class)

*Coverage, environments, approach.*

## Naming

- **Filename:** `traceability-matrix.md`, `acceptance-test-plan.md`, `test-plan.md` in `Artifacts/`.
- **Type value:** `type: BASEPLATE_Verification_Document`.

## Relationships

- `BASEPLATE_Requirements_Document` — *verifies (every requirement ID resolves here)*.
