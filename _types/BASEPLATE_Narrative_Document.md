---
Item_ID: "<UUID>"
type: BASEPLATE_Narrative_Document
title: "<Product> — <Brief | BRD | PR-FAQ | Product Interview>"
baseplate_Product_Slug: ""
baseplate_Doc_Class: ""          # product-brief | brd | pr-faq | product-interview
baseplate_Layer: 0               # 0 for L0 narrative; product-interview is the cartridge's elicitation record
baseplate_Document_Status: drafting
baseplate_Precedence_Rank:
Date_Added:
Date_Modified:
Needs_Processing: false
AI_Instructions: ""
---

# <Product> — <Narrative Document>

> An L0 document (or the cartridge's product-interview record): **why build it, for whom, why now.** Standalone only when someone other than the operator must be convinced or aligned; for a solo operator, L0 collapses into a section of the L1 document.

## Required sections (by class)

- **product-brief / one-pager:** Problem; Audience; Why-now; Success definition.
- **BRD:** Business objectives; Stakeholders; Constraints; Success metrics.
- **PR-FAQ:** Press release (the finished-product announcement); Anticipated FAQs.
- **product-interview:** the PI-1..PI-12 record — each answer a labeled, citable line the selection record references (OFR-1/2).

## Naming

- **Filename:** `<doc-class>.md` (e.g. `brief.md`, `_product-interview.md`).
- **Type value:** `type: BASEPLATE_Narrative_Document`.

## Relationships

- `BASEPLATE_Selection_Record` — *the product-interview's PI-answers are cited by every selection decision*.
