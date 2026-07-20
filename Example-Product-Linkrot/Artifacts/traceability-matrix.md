---
type: BASEPLATE_Verification_Document
Item_ID: 0D8A2C34-C977-406F-9AE1-6209EB26B2A8
title: "Linkrot — Traceability Matrix"
baseplate_Product_Slug: "LR"
baseplate_Doc_Class: traceability-matrix
baseplate_Layer: 4
baseplate_Document_Status: audited
baseplate_Precedence_Rank: 4
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
---

# Linkrot — Traceability Matrix

> Requirement ↔ verification bijection (D-7). No orphans in either direction — every requirement has a verification; every acceptance test maps back to a requirement.

| Requirement | Design element | Verification |
|-------------|----------------|--------------|
| LR-FR-1 | technical-design § Algorithm 1–2 | AT-1 |
| LR-FR-2 | technical-design § Algorithm 4 | AT-2 |
| LR-FR-3 | technical-design § Algorithm 2,4 | AT-3 |
| LR-FR-4 | technical-design § Report format | AT-4 |
| LR-FR-5 | adr-001; CLI contract `--check-external` | AT-5 |
| LR-FR-6 | technical-design § Algorithm 5 | AT-6 |
| LR-FR-7 | technical-design § Algorithm 5 (dedupe) | AT-7 |
| LR-FR-8 | CLI contract § Exit codes | AT-8 |
| LR-FR-9 | CLI contract § report/JSON format | AT-9 |
| LR-NFR-1 | technical-design (stdlib only) | AT-10 |
| LR-NFR-2 | CLI contract `--timeout`/`--workers` | AT-11 |
| LR-NFR-3 | technical-design (read-only) | AT-12 |
| LR-FR-2/3 | technical-design § Resolution semantics (basename, case, dot-dirs) | AT-13, AT-14, AT-17 |
| LR-FR-1/3 | technical-design § Resolution semantics (code/embeds excluded) | AT-15, AT-16 |
| LR-FR-9 | technical-design § Output semantics (clean output, JSON purity) | AT-18 |
| LR-FR-4/9 | technical-design § Counting (occurrence findings; M = files-with-findings) | AT-19 |
| LR-FR-7 | technical-design § External dedup equivalence (fragment-stripped) | AT-20 |
| LR-FR-1 | PRD non-goals + technical-design (unsupported Markdown forms silently skipped) | AT-21 |

## Orphan check

- Requirements without a verification: **none** (LR-FR-1..9, LR-NFR-1..3 all covered).
- Verifications (AT-1..AT-21) without a requirement: **none** — AT-13..21 verify the frozen resolution/extraction/output/counting semantics under LR-FR-1/2/3/4/7/9 (elaborated in technical-design.md, no new requirement IDs).
