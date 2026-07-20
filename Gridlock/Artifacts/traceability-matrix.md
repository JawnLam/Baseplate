---
Item_ID: gridlock-traceability-matrix
type: BASEPLATE_Verification_Document
title: "Gridlock — Traceability Matrix"
baseplate_Product_Slug: "GL"
baseplate_Doc_Class: traceability-matrix
baseplate_Layer: 4
baseplate_Document_Status: internally-consistent
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
---

# Gridlock — Traceability Matrix

> **The requirement ↔ verification bijection.** Every numbered requirement (`prd.md`: GL-FR-1..14, GL-NFR-1..8) and every numbered rule (`game-rules-spec.md`: GL-RS-1..38) maps to a design element and a verification (`acceptance-test-plan.md`: GL-AT-1..30). Every verification maps back. **Orphan check: 60 requirements/rules, 30 verifications, zero unmapped in either direction** (verified by inspection 2026-07-20; re-verify at every stack revision).

## 1. PRD requirements → design → verification

| Req | Realized by (design element) | Verified by |
|---|---|---|
| GL-FR-1 | `game-rules-spec.md` §2 (GL-RS-1..3); engine possession state machine (`technical-design.md` §3.4) | GL-AT-1 |
| GL-FR-2 | Play pool (`game-rules-spec.md` App. B); pool.json schema (`interface-contracts.md` §6) | GL-AT-2 |
| GL-FR-3 | Down cycle (GL-RS-8); commit-then-reveal protocol (`interface-contracts.md` §3–4) | GL-AT-3 |
| GL-FR-4 | Resolution stages (GL-RS-26..28); matchup matrix (App. A) | GL-AT-4 |
| GL-FR-5 | `game-rules-spec.md` §2, §9–§11 | GL-AT-5 |
| GL-FR-6 | GL-RS-11; `PlayerView` projection (`technical-design.md` §3.1); contract invariant (`interface-contracts.md` §4) | GL-AT-6 |
| GL-FR-7 | CP economy (GL-RS-21..23) | GL-AT-7 |
| GL-FR-8 | Playbooks (GL-RS-17; App. C); `adr-005` | GL-AT-8 |
| GL-FR-9 | AI policy module (`technical-design.md` §5); solo flow (`ux-spec.md` §1) | GL-AT-9 |
| GL-FR-10 | Matchmaking + backfill (`technical-design.md` §4) | GL-AT-10 |
| GL-FR-11 | PWA client (`architecture.md` C-3; `ux-spec.md` §2, §6) | GL-AT-11 |
| GL-FR-12 | Match-log format (`interface-contracts.md` §7); write-ahead flow (`architecture.md` §2) | GL-AT-12 |
| GL-FR-13 | Identity model (`data-dictionary.md` §2.1, §4) | GL-AT-13 |
| GL-FR-14 | Sim harness (`architecture.md` C-4; `technical-design.md` §2 `packages/sim`) | GL-AT-14 |
| GL-NFR-1 | Determinism contract + PCG32 spec (`technical-design.md` §3.2–3.3); `replay()` (`interface-contracts.md` §7) | GL-AT-15 |
| GL-NFR-2 | Server authority (`architecture.md` C-2/C-3 boundary; `technical-design.md` §2 import ban, §4) | GL-AT-16 |
| GL-NFR-3 | `data-dictionary.md` §2, §4, §5.1 | GL-AT-17 |
| GL-NFR-4 | Vocabulary rule (`adr-003`); pool naming; `ux-spec.md` visual language | GL-AT-18 |
| GL-NFR-5 | Balance-data externalization (⚙ regime, `game-rules-spec.md` App. D; `interface-contracts.md` §6; load rules `technical-design.md` §6) | GL-AT-19 |
| GL-NFR-6 | Possession/shot-clock arithmetic (GL-RS-1, GL-RS-12; RS-A-2 lever) | GL-AT-20 |
| GL-NFR-7 | Deployment topology (`architecture.md` §3); `runbook.md` (all sections) | GL-AT-21 |
| GL-NFR-8 | `ux-spec.md` §2, §5, §6 | GL-AT-22 |

## 2. Rules-spec rules → verification (design element: the engine, `technical-design.md` §3, implements every GL-RS rule; rule-specific design citations below only where another document co-owns)

| Rules | Verified by | Co-owning design element |
|---|---|---|
| GL-RS-1..5 | GL-AT-23 | Possession state machine (`technical-design.md` §3.4) |
| GL-RS-6, 7 | GL-AT-29 | — |
| GL-RS-8, 9, 11 | GL-AT-24 | Protocol phase/commit messages (`interface-contracts.md` §3–4) |
| GL-RS-10 | GL-AT-24 | Redraw log event (`interface-contracts.md` §7) |
| GL-RS-12 | GL-AT-24 | Server shot-clock timers (`technical-design.md` §4) |
| GL-RS-13 | GL-AT-27 | — |
| GL-RS-14 | GL-AT-29 | — |
| GL-RS-15, 16 | GL-AT-30 | pool.json (`interface-contracts.md` §6) |
| GL-RS-17 | GL-AT-30 | playbooks.json (`interface-contracts.md` §6) |
| GL-RS-18..20 | GL-AT-24 | Deck/hand state (`technical-design.md` §3.1); shuffle draws (§3.3) |
| GL-RS-21, 22 | GL-AT-25 | — |
| GL-RS-23 | GL-AT-25 | `scout`/`scout_result` messages (`interface-contracts.md` §3–4) |
| GL-RS-24, 25 | GL-AT-26 | Clause schema (`interface-contracts.md` §6 pool.json) |
| GL-RS-26, 27 | GL-AT-26 | matchup.json (`interface-contracts.md` §6) |
| GL-RS-28 | GL-AT-26 | Draw mechanics + basis points (`technical-design.md` §3.3) |
| GL-RS-29 | GL-AT-26 | — |
| GL-RS-30 | GL-AT-27 | `declare` message + DeclarationSheet (`interface-contracts.md` §3; `ux-spec.md` §2) |
| GL-RS-31, 32 | GL-AT-27 | — |
| GL-RS-33, 34 | GL-AT-28 | `possession_change` message (`interface-contracts.md` §4) |
| GL-RS-35 | GL-AT-28 | — |
| GL-RS-36 | GL-AT-29 | Resume window (`technical-design.md` §6) |
| GL-RS-37 | GL-AT-29 | `concede` message (`interface-contracts.md` §3) |
| GL-RS-38 | GL-AT-30 | `validateBalanceData` (`technical-design.md` §3.2); set-load rules (`interface-contracts.md` §6) |

## 3. Reverse map (verification → requirements; completeness check)

| Verification | Maps back to |
|---|---|
| GL-AT-1..14 | GL-FR-1..14 (1:1, same index) |
| GL-AT-15..22 | GL-NFR-1..8 (1:1, index − 14) |
| GL-AT-23 | GL-RS-1, 2, 3, 4, 5 |
| GL-AT-24 | GL-RS-8, 9, 10, 11, 12, 18, 19, 20 |
| GL-AT-25 | GL-RS-21, 22, 23 |
| GL-AT-26 | GL-RS-24, 25, 26, 27, 28, 29 |
| GL-AT-27 | GL-RS-13, 30, 31, 32 |
| GL-AT-28 | GL-RS-33, 34, 35 |
| GL-AT-29 | GL-RS-6, 7, 14, 36, 37 |
| GL-AT-30 | GL-RS-15, 16, 17, 38 |

Counting check: AT-23..30 cover 5+8+3+6+4+3+5+4 = 38 = all GL-RS rules; AT-1..22 cover all 22 PRD requirements. No verification exists without a requirement; no requirement or rule lacks a verification.
