---
Item_ID: "UUID-OR-SLUG"
type: BASEPLATE_Selection_Record
title: "<Product> — Selection Record"
baseplate_Product_Slug: ""
baseplate_ID_Scheme: ""          # e.g. ACME-FR-<n> — S-3, extractable
baseplate_Precedence_Declaration: ""   # ordered list of doc classes, lower index wins on conflict — S-3, extractable
Date_Added:
Date_Modified:
Needs_Processing: false
AI_Instructions: ""
---

# <Product> — Selection Record

> The locked output of `03-SELECTION.md`. Records the document set for this stack with **every inclusion AND every exclusion tied to its triggering interview answer** (OFR-4), plus the ID scheme and precedence declaration as extractable fields (S-3). Locked before any document is drafted (F8).

## Decisions

| Class | Include / Exclude | Triggering PI-answer | Structural Type |
|-------|-------------------|----------------------|-----------------|
|       |                   |                      |                 |

*An unexplained exclusion is a defect; an unexplained inclusion is gold-plating. Every row cites a PI-answer by ID.*

## ID scheme

`<PRODUCT>-FR-<n>` / `<PRODUCT>-NFR-<n>` / `<PRODUCT>-ADR-<n>` … — stable, never renumbered.

## Precedence declaration (S-3, extractable)

*Ordered list — the document that wins on conflict comes first. Within contracts: schema/config is law; code conforms.*

1. …

## Naming

- **Filename:** `_selection-record.md` in the cartridge (not inside `Artifacts/` unless the stranger should see it).
- **Type value:** `type: BASEPLATE_Selection_Record`.
