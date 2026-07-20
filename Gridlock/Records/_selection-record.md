---
type: BASEPLATE_Selection_Record
Item_ID: gridlock-selection-record
title: "Gridlock — Selection Record"
baseplate_Product_Slug: "GL"
baseplate_ID_Scheme: "GL-FR-<n> / GL-NFR-<n> / GL-RS-<n> / GL-ADR-<n>"
baseplate_Precedence_Declaration: "prd > game-rules-spec > architecture > adr-001 > adr-002 > adr-003 > adr-004 > adr-005 > technical-design > interface-contracts > data-dictionary > ux-spec > runbook > acceptance-test-plan"
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
---

# Gridlock — Selection Record

> Locked before drafting (F8), 2026-07-20. Every inclusion and exclusion cites its triggering PI-answer (OFR-4). An unexplained exclusion is a defect; an unexplained inclusion is gold-plating (PF-4).

## Decisions

| Class | Include / Exclude | Triggering PI-answer | Structural Type |
|-------|-------------------|----------------------|-----------------|
| product-brief (L0) | **Exclude** (collapse into PRD as a Why/Problem section) | PI-3 operator directs the builder personally — no one else to convince yet | — |
| BRD / MRD (L0) | **Exclude** | PI-3 no enterprise context; PI-5 no market case to argue at prototype stage | — |
| PR/FAQ (L0) | **Exclude** (deferred-scope register: revisit if the game moves toward shipping to strangers) | PI-5 public users are *eventual*, not prototype | — |
| PRD (L1) | **Include** (unconditional; absorbs L0 why/problem; carries the logic-&-rigor-vs-fun tension and the IP-clean constraint as requirements) | canon rule 1; PI-5, PI-9 | BASEPLATE_Requirements_Document |
| functional spec (L1) | **Include as `game-rules-spec.md`** — the exhaustive observable behavior of the game itself (turn structure, play resolution, hidden-info reveals, states, edge cases). Split from the PRD deliberately: the rules will churn through playtesting at a different speed than product scope — binding them into one file couples change rates (canon §1 consequence a) | PI-1 nontrivial game behavior; PI-9 playtest churn; PI-11 the contested decisions live here once decided | BASEPLATE_Requirements_Document |
| SRS (L1) | **Exclude** (PRD FR/NFR split + rules spec covers the union; no regulated-rigor driver) | PI-6 no regulated data | — |
| acceptance criteria, Gherkin (L1) | **Exclude** standalone (scenario-form criteria fold into the acceptance-test-plan) | canon merge rule; no explicit operator want | — |
| architecture (L2) | **Include** | PI-7 four components + persistent data (≥3-component trigger fires twice over) | BASEPLATE_Design_Document |
| ADR-001 resolution randomness (L2) | **Include** | PI-11(a) deterministic card math vs dice-like variance | BASEPLATE_Design_Document |
| ADR-002 turn structure (L2) | **Include** | PI-11(b) simultaneous-reveal vs alternating-with-reactions | BASEPLATE_Design_Document |
| ADR-003 Netrunner-mapping literalness (L2) | **Include** | PI-11(c) how literally drives/ice/runs translate | BASEPLATE_Design_Document |
| further ADRs | **Standing rule:** any two-way decision surfaced in generation becomes an ADR draft with a recommendation for operator sign-off | PI-11 operator delegation; PF-5 guard | BASEPLATE_Design_Document |
| ADR-004 role structure (L2) | **Include** *(added 2026-07-20 under the standing rule; operator decided: alternating possessions)* | PI-1 football framing vs Netrunner fixed roles — surfaced during PRD drafting | BASEPLATE_Design_Document |
| ADR-005 ready-made playbooks (L2) | **Include** *(added 2026-07-20 under the standing rule; operator decided: ready-made only for prototype)* | PI-5/PI-9 prototype-focus priority — surfaced during PRD drafting | BASEPLATE_Design_Document |
| threat model (L2) | **Exclude — with named revisit trigger** (deferred-scope register: arrives with real accounts) | PI-6 no accounts, no PII, nothing stored identifies a human | — |
| technical design (L3) | **Include** — engine internals: game-state machine, deterministic resolution pipeline, replay format, RNG policy (per ADR-001 outcome), AI-opponent interface, sim-harness contract. Maximum precision: frozen behavioral semantics per PF-6 | PI-2 agent builder (max L3); PI-6 deterministic replay-driven engine; PI-7 | BASEPLATE_Design_Document |
| interface contracts (L3) | **Include** as its own document — client↔server protocol (versioned, frozen) + the balance-data file schemas (the fluid zone's fixed container). "Schema is law; code conforms." | PI-2 + PI-7 two components/builders meet → mandatory; PI-4(d) internal versioned contract; PI-9 freeze line | BASEPLATE_Design_Document |
| data dictionary + ERD (L3) | **Include** — match log, game state, play-card/playbook, anonymous player_id entities | PI-6 persistent data model (match logs, playbooks); PI-7 shared across components | BASEPLATE_Design_Document |
| UX design spec (L3) | **Include** — PWA flows, states, components, the play-reveal moment, accessibility | PI-4(a) human interface exists | BASEPLATE_Design_Document |
| test plan (L4) | **Exclude** standalone (strategy section folds into acceptance-test-plan; the sim harness — the real test engine — is specified in technical-design) | canon merge rule; PF-4 | — |
| acceptance-test-plan (L4) | **Include** (unconditional; definition of done; includes sim-harness acceptance runs) | canon rule 1 | BASEPLATE_Verification_Document |
| traceability-matrix (L4) | **Include** (unconditional, D-7) | canon rule 1; D-7 | BASEPLATE_Verification_Document |
| stranger test (L4) | **Include** (the ship gate) | PI-2 agent-built | BASEPLATE_Stranger_Test_Log |
| runbook (L5) | **Include** — VPS deploy, health checks, failure responses, written for zero operator technical input; **absorbs deploy/rollback steps** | PI-8 continuous on Hostinger VPS; PI-3 non-technical operator | BASEPLATE_Operations_Document |
| SLO / SLI (L5) | **Exclude** (deferred-scope register: when strangers depend on the service) | PI-8 prototype, no availability commitment | — |
| deployment / rollback + migration (L5) | **Exclude** standalone (deploy/rollback fold into runbook; no schema migrations — prototype data is anonymous and resettable) | PI-8; PI-6 resettable data posture | — |
| license (L6) | **Include** (always) — defaulted private / all rights reserved, flagged as operator-flippable | canon rule 6; PI-12 default recorded | BASEPLATE_Contract_Document |
| SOW (L6) | **Exclude** | PI-12 no money/obligation crosses a boundary | — |
| WBS / plan (L6) | **Exclude** | PI-10 no hard dates; single workstream | — |

## Resulting stack (in `Artifacts/`, generation order = dependency-layer order)

1. `prd.md` (L0+L1)
2. `game-rules-spec.md` (L1)
3. `architecture.md` (L2)
4. `adr-001-resolution-randomness.md` (L2)
5. `adr-002-turn-structure.md` (L2)
6. `adr-003-netrunner-mapping.md` (L2)
7. `adr-004-role-structure.md` (L2) *(added 2026-07-20, standing rule)*
8. `adr-005-ready-made-playbooks.md` (L2) *(added 2026-07-20, standing rule)*
9. `technical-design.md` (L3)
10. `interface-contracts.md` (L3)
11. `data-dictionary.md` (L3)
12. `ux-spec.md` (L3)
13. `acceptance-test-plan.md` (L4)
14. `traceability-matrix.md` (L4)
15. `runbook.md` (L5)
16. `license.md` (L6)

## ID scheme

`GL-FR-<n>` (product functional requirements, PRD) / `GL-NFR-<n>` (non-functional, PRD) / `GL-RS-<n>` (rules-spec numbered behaviors) / `GL-ADR-<n>` (decisions). Stable, never renumbered.

## Precedence declaration (S-3, extractable)

1. `prd` — wins on **scope** (what is in/out of the prototype) and the non-goals.
2. `game-rules-spec` — **law for gameplay semantics** (what happens on the field); ADR outcomes are incorporated here once decided.
3. `architecture` > `adr-001` > `adr-002` > `adr-003` > `adr-004` > `adr-005` — structure and decision records.
4. `technical-design` — wins on engine internals (state machine, determinism, replay, RNG policy).
5. `interface-contracts` — **law for wire formats and data-file schemas**; schema is law, code conforms.
6. `data-dictionary` > `ux-spec`.
7. `runbook` — wins on **deployment facts** (VPS layout, processes, health checks) that no other document owns.
8. `acceptance-test-plan` — verifies, never redefines.

On any WHAT-vs-HOW conflict: the higher document wins on WHAT, the owning lower document wins on its declared HOW domain.
