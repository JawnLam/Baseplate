---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: baseplate-meta-traceability
title: "Baseplate — Traceability Matrix"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
doc_type: baseplate-meta
role: traceability-matrix
scope: subject-agnostic
---

# Baseplate — Traceability Matrix

> **Walk this, not memory, when auditing Baseplate itself (ONF-3, inherited from OVE's Convention 13). Each row traces one Baseplate requirement through the engine mechanism that enforces it, the gate/verification that checks it, and the failure mode it prevents. When you add or amend an OFR/ONF requirement, a gate, a Type, or a PF failure mode, update this file in the same change.**

## Requirements → enforcement → verification → failure prevented

| Requirement | Enforcement (engine) | Verification | Failure prevented |
|---|---|---|---|
| OFR-1 interview one-at-a-time | `02-ELICITATION.md` | golden-session Baseplate criterion (elicit-before-generate); shakedown | F1; PF-2 (carryover) |
| OFR-2 interview record cited | `02-ELICITATION.md`; `BASEPLATE_Narrative_Document` (product-interview) | consistency audit (selection cites PI-answers) | PF-5 |
| OFR-3 apply selection protocol | `03-SELECTION.md` registry | consistency audit; stranger test | PF-4 |
| OFR-4 record inclusions + exclusions | `03-SELECTION.md`; `BASEPLATE_Selection_Record` | consistency audit | PF-4 (gold-plating) |
| OFR-5 precedence declared | `BASEPLATE_Selection_Record` (extractable field, S-3) | consistency audit (precedence present) | — |
| OFR-6 generation standards; dependency verified | `04-GENERATION-STANDARDS.md`; `_dependency-log.md` | consistency audit (dependency log complete) | PF-1 (fabrication); PF-3 (orphans) |
| OFR-7 write for the stranger | `04-GENERATION-STANDARDS.md` std 8 | stranger test (ST-1/ST-3) | PF-5 |
| OFR-8 sequence (F8) | `00-START-HERE.md`; `BOOTSTRAP-NEW-STACK.md` | golden session (E-2 lock order) | — |
| OFR-9 consistency audit | `05-GATES.md` Gate 1 | run per cartridge | PF-1/3/5 |
| OFR-10 stranger test = ship gate | `05-GATES.md` Gate 2 | run per cartridge; `BASEPLATE_Stranger_Test_Log` | — |
| OFR-11 triage + catalog-feed | `05-GATES.md`; `_portfolio/failure-catalog.md` | close-out check | — |
| OFR-12 portfolio failure catalog | `_portfolio/failure-catalog.md` (Grows-Through-Use Zone, D-5) | E-5 (seeded); loaded at session start | all PF |
| OFR-13 persist per-cartridge state (P2) | backbone state files; Q11 contract | close-out state write | PF-2 |
| OFR-14 revision mode | `05-GATES.md § Revision` | shakedown revision pass | — |
| OFR-15 behavioral completeness — define every parsing/counting equivalence class | `04-GENERATION-STANDARDS.md` std 10 | consistency audit; stranger test (ST-3) | PF-6 (under-specified semantics) |
| ONF-1 markdown-only, manual gates | whole engine; `_baseplate-engine/_meta/VALIDATION-CHECKLIST.md` (manual gate walkthrough) | `_baseplate-engine/_meta/VALIDATION-CHECKLIST.md` | — |
| ONF-2 context-budget / tiered reads | `00-START-HERE.md` tiers | read-tier compliance (golden session U-b) | — |
| ONF-3 Baseplate traces in its own matrix | this file | audit walks this matrix | orphan requirement/check |
| ONF-4 P7 throughout | `AI-BOOTSTRAP.md`; Type attribution rules | scrub (C3/C4-equivalent) | identity inference |
| ONF-5 complete cartridge self-contained | `05-GATES.md` (Artifacts-only) | stranger test (folder frozen) | — |

## Failure modes (portfolio catalog)

Every `PF-n` in `_portfolio/failure-catalog.md` (PF-1..PF-5 seeded; **PF-6 added from the Linkrot shakedown**) appears above as a "failure prevented." New PF entries added at cartridge close-out must be traced here in the same change.

## Orphans

Goal: empty. Disposition each: `gap — needs enforcement`, `gap — needs verification`, or `intentional`.

- **O-1 · REQ-M1 flywheel is committed but literally empty at v0.1.** The moat mechanism (`_portfolio/`) exists and is seeded, but its compounding value is unrealized until real cartridges accumulate. Disposition: **intentional (v0.x)** — re-assess after the shakedown + first products; tracked in `_meta/vetting-rubric-filled.md`.
- Gap count: **0** structural gaps (O-1 is intentional/maturity, not a missing chain).
