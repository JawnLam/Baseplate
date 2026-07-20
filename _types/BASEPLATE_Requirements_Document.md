---
Item_ID: "<UUID>"
type: BASEPLATE_Requirements_Document
title: "<Product> — <PRD | Functional Spec | SRS | Acceptance Criteria>"
baseplate_Product_Slug: ""
baseplate_Doc_Class: ""          # prd | functional-spec | srs | acceptance-criteria
baseplate_Layer: 1
baseplate_ID_Scheme: ""          # e.g. ACME-FR-<n>, ACME-NFR-<n>
baseplate_Document_Status: drafting   # drafting | internally-consistent | audited | stranger-passed | shipped
baseplate_Precedence_Rank:       # integer; lower wins on conflict per the stack precedence declaration
Date_Added:
Date_Modified:
Needs_Processing: false
AI_Instructions: ""
---

# <Product> — Requirements

> An L1 document: **what the product must do**, stated how-agnostically. Requirements state *what*, never *how* (a requirement that names an implementation belongs in L3). Every requirement carries a stable namespaced ID and maps to a verification in the stack's traceability document (D-7). Scope is bounded by both edges — the non-goals section is required.

## Numbered requirements

*Each requirement: `<PRODUCT>-FR-<n>` (functional) or `<PRODUCT>-NFR-<n>` (non-functional), a single testable statement of behavior, and its verification ID. IDs are stable — never reused or renumbered.*

| ID | Requirement (what, not how) | Verified by |
|----|------------------------------|-------------|
|    |                              |             |

## Non-goals

*Explicit out-of-scope statements. A scope with only an inside is not bounded.*

## Success metrics

*Observable measures of "the product does what it must."*

## Assumptions & open questions

*First-class, with owners. An unknown the interview did not settle lives here — never absorbed into confident prose.*

## Naming

- **Filename:** `<doc-class>.md` (e.g. `prd.md`) in the cartridge `Artifacts/`.
- **Type value:** `type: BASEPLATE_Requirements_Document`.

## Relationships

- `BASEPLATE_Verification_Document` — *verified-by (each requirement ID maps to a verification)*.
- `BASEPLATE_Selection_Record` — *earned-by (the interview answer that included this document)*.
