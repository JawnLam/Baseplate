---
type: BASEPLATE_Selection_Record
Item_ID: linkrot-selection-record
title: "Linkrot — Selection Record"
baseplate_Product_Slug: "LR"
baseplate_ID_Scheme: "LR-FR-<n> / LR-NFR-<n> / LR-ADR-<n>"
baseplate_Precedence_Declaration: "prd > technical-design > adr-001 > acceptance-test-plan"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
---

# Linkrot — Selection Record

> Locked before drafting (F8). Every inclusion and exclusion cites its triggering PI-answer (OFR-4).

## Decisions

| Class | Include / Exclude | Triggering PI-answer | Structural Type |
|-------|-------------------|----------------------|-----------------|
| product-brief (L0) | **Exclude** (collapse into PRD) | PI-3 solo operator — no one to convince | — |
| PRD (L1) | **Include** (unconditional; absorbs the L0 problem/why) | canon rule 1 | BASEPLATE_Requirements_Document |
| functional spec (L1) | **Exclude** (merge behaviors into the PRD; small product) | PI-1 bounded behavior; canon rule 8 merge | — |
| technical-design + interface-contract (L3) | **Include** (merged: CLI surface + algorithm + failure modes) | PI-2 agent-built (max L3 precision); PI-4 CLI is the interface | BASEPLATE_Design_Document |
| ADR (L2) | **Include** (adr-001: external-URL checking) | PI-11 contested decision | BASEPLATE_Design_Document |
| architecture (L2) | **Exclude** (collapse into L3) | PI-7 <3 components, single process | — |
| threat model (L2) | **Exclude** | PI-4 no inbound interface; PI-6 no stored/sensitive data | — |
| data dictionary (L3) | **Exclude** | PI-6 no persistent data model | — |
| UX spec (L3) | **Exclude** (CLI output covered in the interface contract) | PI-4 no human GUI | — |
| acceptance-test-plan (L4) | **Include** (unconditional; absorbs the one-shot run procedure) | canon rule 1; PI-8 one-shot | BASEPLATE_Verification_Document |
| traceability-matrix (L4) | **Include** (unconditional, D-7) | canon rule 1; D-7 | BASEPLATE_Verification_Document |
| stranger test (L4) | **Include** (the ship gate) | PI-2 agent-built | BASEPLATE_Stranger_Test_Log |
| runbook (L5) | **Exclude** (collapse into L4 acceptance) | PI-8 one-shot deliverable | — |
| SLO (L5) | **Exclude** | PI-8 no availability requirement | — |
| license (L6) | **Include** (always) | canon rule 6 | BASEPLATE_Contract_Document |
| SOW (L6) | **Exclude** | PI-12 no money/obligation crosses a boundary | — |
| WBS/plan (L6) | **Exclude** | PI-10 single workstream, no hard deadline | — |

## Resulting stack (in `Artifacts/`)

`prd.md`, `technical-design.md`, `adr-001-external-url-checking.md`, `acceptance-test-plan.md`, `traceability-matrix.md`, `license.md`.

## ID scheme

`LR-FR-<n>` (functional), `LR-NFR-<n>` (non-functional), `LR-ADR-<n>` (decisions).

## Precedence declaration (S-3)

`prd` > `technical-design` > `adr-001` > `acceptance-test-plan`. On a WHAT-vs-HOW conflict the PRD wins on scope; the technical-design wins on the CLI surface details it owns.
