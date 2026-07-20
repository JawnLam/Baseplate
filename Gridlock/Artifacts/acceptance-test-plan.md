---
Item_ID: gridlock-acceptance-test-plan
type: BASEPLATE_Verification_Document
title: "Gridlock — Acceptance Test Plan"
baseplate_Product_Slug: "GL"
baseplate_Doc_Class: acceptance-test-plan
baseplate_Layer: 4
baseplate_Document_Status: shipped
baseplate_Precedence_Rank: 14
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
---

# Gridlock — Acceptance Test Plan

> **The definition of done.** Defines every verification `GL-AT-1..30`; the requirement↔verification bijection lives in `traceability-matrix.md`. This document verifies WHAT (`prd.md`, `game-rules-spec.md`) — never the HOW documents; a test that would pin an implementation detail beyond the L1 documents' claims is out of bounds. This plan absorbs the test strategy (no separate test-plan document exists in this stack).

## 1. Test strategy (four rings)

| Ring | Tooling | What it proves |
|---|---|---|
| R1 — Engine unit & property tests | Vitest against the pure engine | Each GL-RS rule in isolation; property tests for invariants (state ranges, draw ordering) |
| R2 — Simulation batches | Sim harness CLI (AI vs AI, scripted scenarios) | Statistical and structural claims at scale: determinism, balance bands, pacing arithmetic |
| R3 — Integration/protocol tests | Vitest driving a real server over WebSocket | Contract conformance, hidden-info invariant, session/resume behavior |
| R4 — Manual playtest gates | Two humans + checklist; operator verdict | Fun, feel, pacing in reality; the PRD's success metrics |

Environments: R1/R2 anywhere Node runs; R3 against a locally launched server; R4 on the deployed VPS build with real phones (one iOS Safari, one Android Chrome).

**Scripted-scenario capability (used throughout):** the sim harness accepts a fixed seed + a scripted input sequence per side, so any rules situation can be constructed deterministically. "Construct X; assert Y" below always means this mechanism.

## 2. Verifications GL-AT-1..22 (PRD requirements)

| ID | Verifies | Ring | Procedure → pass criteria |
|----|----------|------|---------------------------|
| GL-AT-1 | GL-FR-1 | R2 | Run 100 sim matches; assert every match has exactly 2 players' role states, roles swap on every possession transition, and transitions occur only on the five legal enders (GL-RS-3). |
| GL-AT-2 | GL-FR-2 | R1+inspection | Automated: every pool card name matches the allowlist in the balance set. Manual: reviewer confirms all names are generic football vocabulary; zero team/league/player references. |
| GL-AT-3 | GL-FR-3 | R3 | Protocol trace of 20 downs: no message reveals either commitment before both are locked; `reveal` arrives to both clients carrying both cards. |
| GL-AT-4 | GL-FR-4 | R1 | For every matchup cell: constructed resolutions land inside the modified band; turnover/breakaway events occur **only** in cells (post-clause) with nonzero probabilities for them. |
| GL-AT-5 | GL-FR-5 | R2 | 1,000-match sim: downs/distance/possessions/scoring/end conditions conform to GL-RS structural rules; zero illegal states in any log. |
| GL-AT-6 | GL-FR-6 | R3 | Hidden-info sweep: capture every server→client frame across full matches; assert no frame ever contains opponent hand, deck, discard, unrevealed commitment, commitment status, or scout results (contract invariant, `interface-contracts.md` §4). |
| GL-AT-7 | GL-FR-7 | R1 | Economy scenarios: CP income/floor/cap arithmetic exact (GL-RS-21); unaffordable cards rejected (GL-RS-9); scout charges exactly its cost (GL-RS-23). |
| GL-AT-8 | GL-FR-8 | R1 | Load shipped balance set: exactly 4 playbooks, each passing GL-RS-17/38 invariants; server offers all 4; no builder UI route exists in the client. |
| GL-AT-9 | GL-FR-9 | R4 | A human completes a full solo match vs AI on a phone, start → result screen, no errors. |
| GL-AT-10 | GL-FR-10 | R3+R4 | R3: two test clients queue and get matched; a lone client gets an AI match after the backfill window. R4: two humans on separate phones complete a matchmade match. |
| GL-AT-11 | GL-FR-11 | R4 | On iOS Safari and Android Chrome: install to home screen; complete a match portrait one-thumbed; desktop browser completes the same flows. |
| GL-AT-12 | GL-FR-12 | R2+R1 | Every sim and integration match produces a log passing schema validation with a terminal `end` event (void-crash exception per `technical-design.md` §6); DB row ↔ log file invariant holds (data-dictionary §5.3). |
| GL-AT-13 | GL-FR-13 | R3 | Fresh client: `hello` with new device_id yields player_id + generated handle; no credential prompt exists anywhere in the client. |
| GL-AT-14 | GL-FR-14 | R2 | `sim run --matches 1000` completes headless, emits 1,000 valid logs + an aggregate stats report (win rates, event rates, duration distribution). |
| GL-AT-15 | GL-NFR-1 | R2 | Replay all 1,000 logs from GL-AT-14: `replay()` reproduces every draw and outcome exactly; zero divergences. |
| GL-AT-16 | GL-NFR-2 | R3+R1 | Same capture as GL-AT-6 plus source check: client bundle contains no engine import (`technical-design.md` §2 lint rule) and no code path adjudicates outcomes. |
| GL-AT-17 | GL-NFR-3 | R1+inspection | Schema audit: every stored column/key matches `data-dictionary.md`; grep-audit confirms no PII-shaped fields; client storage limited to the four documented keys. |
| GL-AT-18 | GL-NFR-4 | inspection | Reviewer sweep of product strings, card pool, art assets, and stack gameplay vocabulary: zero *Netrunner* terms-of-expression, zero NFL/NCAA/team/player references. |
| GL-AT-19 | GL-NFR-5 | R1 | Change a tunable, a matchup cell, and a playbook count in a copy of the balance set; reload; observe changed behavior with **zero code rebuild**. Invalid set → rejected whole, previous set stays live (GL-RS-38). |
| GL-AT-20 | GL-NFR-6 | R2+R4 | R2: sim duration model (downs × decision windows) predicts 3–6 min medians. R4: ≥ 10 real matches timed; median inside 3–6 min (else tune per RS-A-2 and re-run). |
| GL-AT-21 | GL-NFR-7 | R4 | A fresh operator-agent session executes `runbook.md` §2–§6 verbatim on the VPS: deploy, health check, restart, backup, rollback — each succeeds using only the runbook. |
| GL-AT-22 | GL-NFR-8 | R4 | Full match on current iOS Safari + Android Chrome + one evergreen desktop browser; no layout breakage in portrait; all controls reachable. |

## 3. Verifications GL-AT-23..30 (rules-spec suites)

Each suite is a named Vitest/sim group whose cases cite their GL-RS IDs; a suite passes when every cited rule's cases pass.

| ID | Covers | Ring | Suite content (constructed-scenario examples) |
|----|--------|------|------------------------------------------------|
| GL-AT-23 | GL-RS-1..5 | R1+R2 | Strict alternation regardless of drive endings; logged coin flip decides first possession; exactly one drive per possession; no clock anywhere; scoring values 7/3/2 only. |
| GL-AT-24 | GL-RS-8..12, 18..20 | R1 | Four-phase ordering; commit legality vs CP; redraw procedure (construct all-unaffordable hand → assert redraw loop + logging); phase-boundary count updates; auto-commit picks lowest-cost-then-hand-order; hand persistence across downs, off-side hand set-aside; refill to 4; seeded reshuffle when deck empties. |
| GL-AT-25 | GL-RS-21..23 | R1 | Possession floor raise (from below and not from above); +1 income and cap; CP persistence; scout: costs 2, once per down, excludes committed card, buyer-only visibility, logged draw. |
| GL-AT-26 | GL-RS-24..29 | R1 | Clause conditions ×4 and modifiers ×2, each in isolation and combined; the exact modifier order (offense tier → defense tier → offense clause → defense clause); min>max collapse; event clamp/rescale at 95%; draw order INC-SACK-INT-FUM-BRK; band substitution for SACK/BRK; yardage clamps at both goal lines; first-down/goal-to-go arithmetic; 4th-down failure → turnover on downs at final LOS. |
| GL-AT-27 | GL-RS-13, 30..32 | R1 | Declaration only on 4th down and openly visible; declared downs consume no cards/CP beyond upkeep; punt spot math incl. touchback; FG range gate, distance table lookup, made/missed spot outcomes; clock-expiry on a declaration window selects `normal`. |
| GL-AT-28 | GL-RS-33..35 | R1 | Every possession-start position case incl. field-position carry; turnover mirror spot with clamp-to-1 and the LOS ≥ 90 touchback; safety scoring + next-possession spot. |
| GL-AT-29 | GL-RS-6, 7, 14, 36, 37 | R1+R3 | Tiebreak rounds produce win or (after 3) tie; result-type completeness; 3 consecutive auto-commits → forfeit; disconnection behaves exactly as clock expiry (R3: kill a client mid-match, watch forfeit arrive); concede at arbitrary points. |
| GL-AT-30 | GL-RS-15..17, 38 | R1 | Card anatomy validation (tier↔cost derivation, zero-or-one clause); pool-only membership; deck sizes 20/16; ≥ 4 zero-cost per deck; engine refuses invalid sets whole. |

## 4. Pass/fail & sign-off

- **Machine gate:** GL-AT-1..8, 12..17, 19, 23..30 green in CI-style run (R1+R2+R3) on the release commit.
- **Device gate:** GL-AT-9, 10, 11, 20, 22 executed on real devices against the deployed VPS build; results recorded.
- **Operations gate:** GL-AT-21 executed on the real VPS.
- **Operator sign-off:** the PRD §5 fun metric — the operator plays ≥ 5 matches and records the verdict. The prototype is *accepted* only with all four gates green; the fun verdict additionally decides whether post-prototype scope (PRD §4 ⏳ items) opens.

Failures: fix and re-run the failed gate. A test found wrong (testing more than its requirement claims) is corrected against the L1 documents — the requirement, not the test, is the authority.
