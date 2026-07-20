---
Item_ID: "<UUID>"
type: BASEPLATE_Operations_Document
title: "<Product> — <Runbook | SLO Definitions>"
baseplate_Product_Slug: ""
baseplate_Doc_Class: ""          # runbook | slo
baseplate_Layer: 5
baseplate_Document_Status: drafting
baseplate_Precedence_Rank:
Date_Added:
Date_Modified:
Needs_Processing: false
AI_Instructions: ""
---

# <Product> — <Operations Document>

> An L5 document: **how it runs.** Earned when the product runs continuously or on a schedule; a one-shot deliverable collapses L5 into the L4 acceptance procedure (record that exclusion).

## Required sections (by class)

- **runbook:** Setup; Credentials (referenced, never embedded); Cadence; Failure modes & responses; Monitoring.
- **slo:** Objectives; Indicators; Error budget.

## Naming

- **Filename:** `runbook.md`, `slo.md` in `Artifacts/`.
- **Type value:** `type: BASEPLATE_Operations_Document`.

## Relationships

- `BASEPLATE_Design_Document` — *operationalizes (the runbook runs what the design specifies)*.
