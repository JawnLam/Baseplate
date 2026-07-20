---
type: BASEPLATE_Selection_Record
Item_ID: datestamper-selection-record
title: "Inbox-Datestamper — Selection Record"
baseplate_Product_Slug: "DS"
baseplate_ID_Scheme: "DS-FR-<n> / DS-NFR-<n> / DS-ADR-<n>"
baseplate_Precedence_Declaration: "prd > technical-design > adr-001 > adr-002 > runbook > acceptance-test-plan"
Date_Added: 2026-07-19
Date_Modified: 2026-07-19
Needs_Processing: false
---

# Inbox-Datestamper — Selection Record

> Locked before drafting (F8). Every inclusion and exclusion cites its triggering PI-answer (OFR-4). An unexplained exclusion is a defect; an unexplained inclusion is gold-plating (PF-4).

## Decisions

| Class | Include / Exclude | Triggering PI-answer | Structural Type |
|-------|-------------------|----------------------|-----------------|
| product-brief (L0) | **Exclude** (collapse into PRD) | PI-3 solo operator — no one to convince | — |
| BRD / MRD / PR-FAQ (L0) | **Exclude** | PI-5 no external parties, no market case | — |
| PRD (L1) | **Include** (unconditional; absorbs the L0 problem/why and the functional behaviors) | canon rule 1 | BASEPLATE_Requirements_Document |
| functional spec (L1) | **Exclude** (merge behaviors into the PRD as numbered FRs; small product) | PI-1 bounded behavior; canon merge rule | — |
| SRS (L1) | **Exclude** (PRD's FR/NFR split is sufficient rigor) | PI-6 no regulated data | — |
| architecture (L2) | **Exclude** (collapse runtime topology into L5 runbook + L3) | PI-7 one process, <3 components | — |
| ADR-001 birth-time-vs-mtime (L2) | **Include** | PI-11 contested decision (a) | BASEPLATE_Design_Document |
| ADR-002 baseline / new-files-only (L2) | **Include** | PI-11 contested decision (b) | BASEPLATE_Design_Document |
| threat model (L2) | **Exclude** | PI-4 no inbound interface; PI-5 no external parties; PI-6 no exfil surface | — |
| technical-design + interface-contract (L3) | **Include** (merged: CLI surface + algorithms + **frozen behavioral semantics**) | PI-2 cold maintainer (max L3 precision); PI-4 CLI is the interface | BASEPLATE_Design_Document |
| data dictionary + ERD (L3) | **Exclude** (the only state is a flat filename list; described in the runbook) | PI-6 no persistent data model | — |
| UX spec (L3) | **Exclude** | PI-4 no human GUI | — |
| test plan (L4) | **Exclude** (merge strategy into the acceptance-test-plan; small product) | PI-1 bounded behavior | — |
| acceptance-test-plan (L4) | **Include** (unconditional; definition of done) | canon rule 1 | BASEPLATE_Verification_Document |
| traceability-matrix (L4) | **Include** (unconditional, D-7) | canon rule 1; D-7 | BASEPLATE_Verification_Document |
| stranger test (L4) | **Include** (the ship gate) | PI-2 cold maintainer/rebuilder | BASEPLATE_Stranger_Test_Log |
| runbook (L5) | **Include** — **the differentiator** | PI-8 runs continuously on a schedule | BASEPLATE_Operations_Document |
| SLO / SLI (L5) | **Exclude** | PI-8 no availability SLA on a personal bot | — |
| deployment / rollback + migration (L5) | **Exclude** (fold install/remove into the runbook; no data-schema migration) | PI-8 stateless-ish; PI-9 infrequent revision | — |
| license (L6) | **Include** (always) | canon rule 6 | BASEPLATE_Contract_Document |
| SOW (L6) | **Exclude** | PI-12 no money/obligation crosses a boundary | — |
| WBS / plan (L6) | **Exclude** | PI-10 single workstream, no hard deadline | — |

## Resulting stack (in `Artifacts/`)

`prd.md`, `technical-design.md`, `adr-001-birth-time-vs-mtime.md`, `adr-002-baseline-new-files-only.md`, `runbook.md`, `acceptance-test-plan.md`, `traceability-matrix.md`, `license.md`.

## ID scheme

`DS-FR-<n>` (functional), `DS-NFR-<n>` (non-functional), `DS-ADR-<n>` (decisions).

## Precedence declaration (S-3)

`prd` > `technical-design` > `adr-001` > `adr-002` > `runbook` > `acceptance-test-plan`. On a WHAT-vs-HOW conflict the PRD wins on scope; the technical-design wins on the frozen behavioral semantics it owns (resolution rules, regexes, ordering). The runbook wins on *deployment* facts (cadence, wrapper, lock, state location) that no other document owns.
