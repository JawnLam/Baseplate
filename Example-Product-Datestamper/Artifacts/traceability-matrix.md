---
type: BASEPLATE_Verification_Document
Item_ID: datestamper-traceability-matrix
title: "Inbox-Datestamper — Traceability Matrix"
baseplate_Product_Slug: "DS"
baseplate_Doc_Class: traceability-matrix
baseplate_Layer: 4
baseplate_Document_Status: audited
baseplate_Precedence_Rank: 7
Date_Added: 2026-07-19
Date_Modified: 2026-07-19
Needs_Processing: false
---

# Inbox-Datestamper — Traceability Matrix

> Requirement ↔ verification bijection (D-7). No orphans in either direction — every requirement has a verification; every acceptance test maps back to a requirement.

| Requirement | Design element | Verification |
|-------------|----------------|--------------|
| DS-FR-1 | technical-design § 3.4, 3.5 | AT-1 |
| DS-FR-2 | technical-design § 3.5 (state machine) | AT-2 |
| DS-FR-3 | technical-design § 3.10 (atomic + mtime) | AT-3 |
| DS-FR-4 | technical-design § 3.5 case 2 | AT-4 |
| DS-FR-5 | technical-design § 3.7, 4; adr-001 | AT-5, AT-22 |
| DS-FR-6 | technical-design § 3.8; adr-002 | AT-6 |
| DS-FR-7 | technical-design § 3.2 (already-stamped regex) | AT-7 |
| DS-FR-8 | technical-design § 3.3 (exclusion regex) | AT-8 |
| DS-FR-9 | technical-design § 3.6 (coldness) | AT-9 |
| DS-FR-10 | technical-design § 3.11 (backstop) | AT-10 |
| DS-FR-11 | technical-design § 3.12 (collision) | AT-11 |
| DS-FR-12 | technical-design § 5 (CLI contract) | AT-12 |
| DS-FR-13 | technical-design § 3.9 (ordering) | AT-13 |
| DS-FR-14 | technical-design § 3.13 (logging) | AT-14 |
| DS-FR-15 | technical-design § 3.1, 3.4 (scope split) | AT-15, AT-23 |
| DS-NFR-1 | technical-design § 7 (no third-party deps) | AT-16 |
| DS-NFR-2 | technical-design § 1 (no network) | AT-17 |
| DS-NFR-3 | technical-design § 3.10 (atomic write) | AT-18 |
| DS-NFR-4 | technical-design § 6; adr-001/002 | AT-19 |
| DS-NFR-5 | adr-002; technical-design § 3.8 | AT-6, AT-20 |
| DS-NFR-6 | runbook § Cadence; technical-design § 6 | AT-21 |

## Orphan check

- **Requirements without a verification:** none — DS-FR-1..15 and DS-NFR-1..6 are each covered by at least one AT above.
- **Verifications without a requirement:** none — AT-1..AT-23 each map back to a requirement (AT-22 verifies the DS-FR-5 birth-time fallback; AT-23 verifies the DS-FR-15 `.md`-vs-`.markdown` scope asymmetry; AT-6 and AT-20 jointly cover DS-FR-6 and the DS-NFR-5 link-safety property; no AT tests a behavior no requirement asked for).
