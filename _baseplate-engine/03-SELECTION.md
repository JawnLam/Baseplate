---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: baseplate-engine-03-selection
title: "Baseplate Engine — 03 Selection"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
doc_type: baseplate-engine
role: selection-protocol
scope: subject-agnostic
---

# 03 — SELECTION (choose the document set)

> **Run after the interview (`02-ELICITATION.md`), before any generation (F8). Map the product's shape to a document set using the registry below. Record every inclusion AND every exclusion with the interview answer that triggered it (OFR-4) — an unexplained exclusion is a defect; an unexplained inclusion is gold-plating. Lock the selection before drafting a line of document prose.**

## Decision procedure

1. **Include L1 and L4 unconditionally.** Every stack gets a requirements-bearing document and a verification document. No product shape exempts these.
2. **Walk each other layer against its earning condition** in the registry, citing the interview answer (PI-n) that fires or fails it.
3. **For each class, write one line in the `BASEPLATE_Selection_Record`:** `class | include/exclude | triggering PI-answer | structural Type`.
4. **Set the ID scheme** — namespace = product slug (e.g., `ACME-FR-7`) — and the **precedence declaration** (which document wins on conflict). These are extractable fields (S-3), locked now, not prose later.
5. **Apply merges, not splits.** A small product may merge L0+L1 or L2+L3 into one file, provided the file keeps internally separated per-layer sections and precedence still resolves. Never merge two layers' *questions* into one undifferentiated document.

## The class registry (machine-readable)

Each row: canon class → layer, earning condition (the product-shape signal), structural Type, and the required sections the generated document must contain. This registry is the operational form of the canon's menu (`01-THE-CANON.md` §2) and satisfies S-1 without one Type file per class.

| Class | Layer | Earning condition (cite the PI-answer) | Structural Type | Required sections |
|---|---|---|---|---|
| product-brief / one-pager | 0 | Standalone iff anyone but the operator must be convinced (PI-3, PI-5); else a section of the L1 doc | `BASEPLATE_Narrative_Document` | Problem; Audience; Why-now; Success definition |
| BRD | 0 | Enterprise/consulting context with business objectives to align (PI-3, PI-5, PI-12) | `BASEPLATE_Narrative_Document` | Business objectives; Stakeholders; Constraints; Success metrics |
| PR/FAQ | 0 | Market-facing product needing a crisp external framing (PI-5) | `BASEPLATE_Narrative_Document` | Press release; Anticipated FAQs |
| PRD | 1 | **Unconditional** (requirements-bearing) | `BASEPLATE_Requirements_Document` | Numbered requirements; Non-goals; Success metrics; Assumptions/Open-questions; Verification map ref |
| functional spec | 1 | Observable behavior is nontrivial: states, errors, edge cases (PI-1, PI-4) | `BASEPLATE_Requirements_Document` | Numbered behaviors; Inputs/Outputs; Error/edge cases; Non-goals |
| SRS | 1 | Functional + non-functional union must be numbered and testable (PI-6, high rigor) | `BASEPLATE_Requirements_Document` | Functional reqs; Non-functional reqs; Non-goals; Verification map ref |
| acceptance criteria (Gherkin) | 1 | Bridge to L4 is wanted explicitly | `BASEPLATE_Requirements_Document` | Given/When/Then scenarios; mapping to requirement IDs |
| architecture document | 2 | ≥3 interacting components, OR persistent data, OR external integration (PI-7, PI-6) | `BASEPLATE_Design_Document` | Components & boundaries; Data flow; Deployment topology; Views (C4/4+1) |
| ADR | 2 | Any genuinely contested decision (PI-11) — cheap; include liberally | `BASEPLATE_Design_Document` | Context; Options; Decision; Consequences |
| threat model | 2 | Meaningful attack surface (PI-4 external interface + PI-6 sensitive data) | `BASEPLATE_Design_Document` | Assets; Trust boundaries; Threats; Mitigations |
| technical design / RFC | 3 | Any component whose build is nontrivial; **most precision when builder = agent** (PI-2) | `BASEPLATE_Design_Document` | Data models; Algorithms; Failure modes; Alternatives rejected |
| interface contract | 3 | Mandatory the moment two components or two builders meet (PI-7, PI-2) | `BASEPLATE_Design_Document` | Endpoints/messages; Schemas; Versioning; Error contract |
| data dictionary | 3 | Persistent or shared data model (PI-6, PI-7) | `BASEPLATE_Design_Document` | Entities; Fields; Types; Constraints |
| UX design spec | 3 | Iff a human interface exists (PI-4) | `BASEPLATE_Design_Document` | Flows; States; Components; Accessibility |
| test plan | 4 | Include with L1 (verification) | `BASEPLATE_Verification_Document` | Test strategy; Coverage; Environments |
| acceptance test plan | 4 | **Unconditional** (definition of done) | `BASEPLATE_Verification_Document` | Acceptance criteria; Pass/fail; Sign-off |
| traceability matrix | 4 | **Unconditional per D-7** — the requirement↔verification map | `BASEPLATE_Verification_Document` | Requirement→design→verification rows; orphan check |
| stranger test | 4 | Agent-built products (PI-2) — and always as the ship gate | `BASEPLATE_Stranger_Test_Log` | Criteria table; question classification; rerun log |
| runbook | 5 | Product runs continuously or on a schedule (PI-8) | `BASEPLATE_Operations_Document` | Setup; Credentials; Cadence; Failure modes & responses; Monitoring |
| SLO definitions | 5 | Availability matters (PI-8) | `BASEPLATE_Operations_Document` | Objectives; Indicators; Error budget |
| SOW | 6 | Money or formal obligation crosses a boundary (PI-12) | `BASEPLATE_Contract_Document` | Deliverables; Milestones; Acceptance; Payment |
| license & attribution | 6 | **Always** | `BASEPLATE_Contract_Document` | License; Attribution block |
| WBS / plan | 6 | Multiple workstreams must interleave (PI-10) | `BASEPLATE_Contract_Document` | Workstreams; Dependencies; Sequence |

## Output

- A locked `BASEPLATE_Selection_Record` in the cartridge (inclusions + exclusions + triggering PI-answers + precedence declaration + ID scheme).
- The document set to generate, each mapped to its structural Type and required sections.

## Do not

- Do not skip the exclusion record. "We didn't write a threat model" needs the reason ("PI-4: no external interface; PI-6: no sensitive data").
- Do not include a document because it is customary. Customary-but-unearned is gold-plating — a named failure mode.
