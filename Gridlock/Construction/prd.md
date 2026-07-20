---
Item_ID: gridlock-prd
type: BASEPLATE_Requirements_Document
title: "Gridlock — PRD"
baseplate_Product_Slug: "GL"
baseplate_Doc_Class: prd
baseplate_Layer: 1
baseplate_ID_Scheme: "GL-FR-<n> / GL-NFR-<n>"
baseplate_Document_Status: shipped
baseplate_Precedence_Rank: 1
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
---

# Gridlock — Product Requirements Document

> **What Gridlock is and what it must do — stated how-agnostically.** This document wins on questions of scope. Gameplay semantics are owned by `game-rules-spec.md`; engineering by the Layer-3 documents; verification by `acceptance-test-plan.md` and `traceability-matrix.md`. Precedence for the full stack is declared in each document's `baseplate_Precedence_Rank` field (1 = this document, lower rank wins on conflict).

## 1. Problem & why (Layer 0, absorbed)

Competitive card games built on hidden information and bluffing (the *Android: Netrunner* lineage) deliver the deepest two-player mind games in the genre, but their cyberpunk framing is opaque to a mainstream audience. American football already IS a hidden-information duel — an offense calling plays against a defense concealing its scheme — and its play vocabulary (Cover 2, play-action, Counter Trey) is broadly recognizable in the United States. No shipped digital game occupies this intersection: **the play-calling mind game as a fast, asymmetric card game.**

Gridlock is a prototype to test one hypothesis: *the down-by-down play-calling duel, expressed as a card game with concealed commitments and a dramatic simultaneous reveal, is fun enough to rematch.* Everything in this stack serves getting that hypothesis tested quickly and credibly.

**Audience (prototype phase):** the operator and invited playtesters. **Audience (eventual):** public players, reached only after the prototype proves the core loop.

## 2. Glossary (defined for the stranger; used consistently across the stack)

| Term | Meaning |
|---|---|
| **Match** | One complete game between two sides, ending in a result (win/loss, tie broken per rules spec). |
| **Side** | Offense or Defense — the per-down role. Players alternate sides with possession. |
| **Down** | One commit–reveal–resolve cycle (Gridlock's atomic game beat). |
| **Drive** | A sequence of downs by one possession, ending in a score, turnover, punt, or turnover on downs. |
| **Possession** | One team's turn holding the ball (one drive per possession in the prototype structure). |
| **Play card** | A card naming a generic American football play (offensive) or scheme (defensive), carrying the stats the rules engine resolves. |
| **Playbook** | A ready-made "team": a fixed set of offensive play cards plus defensive scheme cards, selected before a match. |
| **The snap** | The simultaneous reveal of both sides' committed cards for a down. |
| **Match log** | The complete, replayable record of a match: every commitment, reveal, random draw, and resolution outcome. |
| **Sim harness** | The headless (no user interface) tool that runs batch matches against the rules engine for testing and balance analysis. |

## 3. Numbered requirements

*Each requirement is a single testable statement of WHAT, never HOW. Verification IDs (`GL-AT-<n>`) are defined in `acceptance-test-plan.md`; the bijection is maintained in `traceability-matrix.md`. IDs are stable — never reused or renumbered.*

### Functional

| ID | Requirement (what, not how) | Verified by |
|----|------------------------------|-------------|
| GL-FR-1 | A match is played by exactly two players in asymmetric per-down roles — one side on Offense, one on Defense — and the roles alternate with possession, whenever a drive ends (score, turnover, punt, turnover on downs, safety, or missed field goal — the full ender set is owned by `game-rules-spec.md` GL-RS-3). | GL-AT-1 |
| GL-FR-2 | The playable cards are named after real, generic American football plays and schemes (e.g., "Counter Trey", "Cover 2", "Play-Action Deep Shot"), with no team, league, player, or licensed-property branding. | GL-AT-2 |
| GL-FR-3 | Each down, both players commit their card selections secretly; neither commitment is revealed to the opponent until both are locked, at which point both are revealed simultaneously ("the snap") and the down resolves. | GL-AT-3 |
| GL-FR-4 | A down's outcome is determined first by the deterministic matchup of the two revealed commitments, which defines a bounded outcome range; a random draw then selects within that range. Extreme outcomes (turnovers, breakaway gains) are only reachable when the matchup itself opens them. | GL-AT-4 |
| GL-FR-5 | Matches follow recognizable football structure: downs and distance-to-gain, drives, alternating possessions, football scoring values, and a defined match-end condition with a tie-break, as specified in `game-rules-spec.md`. | GL-AT-5 |
| GL-FR-6 | Hidden information is preserved throughout: each player's hand and each committed-but-unrevealed card are concealed from the opponent, and any card designated face-down by the rules stays concealed until its rules-defined reveal condition occurs. | GL-AT-6 |
| GL-FR-7 | Players manage a limited coaching-resource economy: resources are earned and spent (e.g., to draw plays, scout the opponent, or power stronger calls) under the budget rules in `game-rules-spec.md`, so that calling power is always a meaningful choice. | GL-AT-7 |
| GL-FR-8 | The prototype offers at least four ready-made playbooks with distinct strategic personalities; a player selects one before the match. There is no custom playbook construction. | GL-AT-8 |
| GL-FR-9 | A player can play a complete match solo against an AI opponent. | GL-AT-9 |
| GL-FR-10 | A player can play a complete match against another human over the network via matchmaking; if no human opponent is found within a configured wait time, the AI opponent fills in. | GL-AT-10 |
| GL-FR-11 | The game is playable in a current mobile browser as a portrait, one-thumb, installable web app, and remains playable in current desktop browsers. | GL-AT-11 |
| GL-FR-12 | Every match — human or simulated — produces a complete match log from which the entire match can be replayed exactly; match logs are retained server-side. | GL-AT-12 |
| GL-FR-13 | Players are anonymous: identity is an opaque device-generated identifier plus a guest display handle; no login, no email, no password. | GL-AT-13 |
| GL-FR-14 | A headless simulation harness can run batches of complete matches (AI vs AI, or scripted call sequences) with no user interface, emitting the same match logs plus aggregate statistics. | GL-AT-14 |

### Non-functional

| ID | Requirement | Verified by |
|----|-------------|-------------|
| GL-NFR-1 | **Determinism.** Given identical initial state, random seed, and player inputs, the rules engine produces identical outcomes; replaying any match log reproduces the recorded match exactly, byte-for-byte on outcomes. | GL-AT-15 |
| GL-NFR-2 | **Server authority.** All game adjudication happens server-side. A client is never sent concealed opponent information (hand contents, unrevealed commitments, face-down cards) before its legitimate reveal — cheating by client inspection is structurally impossible, not merely discouraged. | GL-AT-16 |
| GL-NFR-3 | **No PII.** No stored field contains a real name, email address, phone number, or account credential; player identifiers are opaque and carry no personal meaning. | GL-AT-17 |
| GL-NFR-4 | **IP cleanliness.** No *Android: Netrunner* names, art, or setting terminology appears anywhere in the product or its documents' product-facing vocabulary; no NFL/NCAA/team names, logos, mascots, or player likenesses appear. Mechanical inspiration is expressed entirely in original, football-native terms. | GL-AT-18 |
| GL-NFR-5 | **Balance externalization.** Every tunable gameplay number — play stats, matchup outcome ranges, resource costs and incomes, possession counts, timers — lives in data files, not code; a balance change is a data edit that takes effect without rebuilding the product. | GL-AT-19 |
| GL-NFR-6 | **Pacing.** A typical human-vs-human match completes in 3–6 minutes, supported by a per-decision time limit (a "shot clock") whose value is a tunable data value, not a hard-coded constant. | GL-AT-20 |
| GL-NFR-7 | **Operability.** The deployed game runs continuously on a single commodity virtual private server, and every operational procedure (deploy, restart, health check, recovery, rollback) is executable by an AI agent or a stranger from `runbook.md` alone, with no technical action required from the product's owner. | GL-AT-21 |
| GL-NFR-8 | **Compatibility.** The client functions on current iOS Safari and current Android Chrome (and evergreen desktop browsers), portrait-first responsive. | GL-AT-22 |

## 4. Non-goals (prototype scope — both edges of scope are explicit)

The following are **out of scope for the prototype**. Items marked ⏳ are recorded as *deferred with a named revisit trigger*, not rejected.

1. ⏳ **Accounts, authentication, or any PII** (trigger: the moment identity beyond anonymous device-ID is needed — arrives together with a threat model).
2. ⏳ **Monetization, card collection, or economy** (trigger: moving past prototype toward public shipping).
3. ⏳ **Ranked play, ratings, or ladders** (trigger: a real player population exists).
4. ⏳ **Social features** — chat, friends, invitations (trigger: a real player population exists).
5. ⏳ **Custom playbook construction** (trigger: the core loop is proven fun in playtests).
6. ⏳ **Native app-store builds** (trigger: the web app earns it).
7. **A public or third-party API.** The client-server protocol is internal.
8. **Spectator mode or a replay-viewer UI.** Match logs make one possible later; none is built now.
9. **Full football simulation fidelity.** No penalties, injuries, weather, substitutions, or realistic clock management; special-teams plays appear only in the simplified forms defined in `game-rules-spec.md`. Gridlock is a card game about play-calling, not a football simulator.
10. **Licensed content of any kind.**
11. **Localization.** English-only prototype.

## 5. Success metrics

| Metric | Target | Measured how |
|---|---|---|
| **Fun (the hypothesis)** | Playtesters ask for a rematch in the majority of sessions; the operator declares the core loop "fun" before any scope beyond the prototype is considered | Playtest sessions; operator verdict recorded per session |
| **Pacing** | Median human match duration inside 3–6 minutes | Match-log timestamps across playtest matches |
| **Determinism** | 100% of replayed match logs reproduce recorded outcomes, across at least 1,000 simulated matches | Sim harness replay run (GL-AT-15) |
| **Competitive balance** | Neither side's per-possession scoring rate drifts outside the band set in the balance data; no single playbook wins more than the data-defined ceiling across large AI-vs-AI samples | Sim harness aggregate statistics |
| **Data hygiene** | Zero PII fields in any stored record | Storage schema inspection (GL-AT-17) |

## 6. Assumptions & open questions (first-class, with owners)

| # | Item | Owner | Status |
|---|---|---|---|
| A-1 | The deployment VPS's specifications (plan tier, memory, operating system) are not yet known; the runbook cannot be finalized without them. | Operator | Open — needed before `runbook.md` is finalized |
| A-2 | The starter play vocabulary (which real, generic plays appear in the four playbooks) is a content-design task delegated to the rules spec and balance data. | Rules spec author | Open — resolved by `game-rules-spec.md` |
| A-3 | Default values for the shot clock, possession count, and variance bands are design estimates until playtesting tunes them; all live in balance data per GL-NFR-5. | Rules spec author, then playtests | Open — initial values set in `game-rules-spec.md` |
| A-4 | The license defaults to private/all-rights-reserved as a recorded default, not an operator decision; the operator may flip it at any time. | Operator | Open — default in force (recorded 2026-07-20) |
| A-5 | A bounded post-snap "audible/reaction" step is a candidate playtest module, deliberately excluded from the prototype skeleton (decision record `adr-002-turn-structure.md`). | Operator, after playtests | Deferred |
| A-6 | Assumption: recognizable generic play names (e.g., "Cover 2") are common football vocabulary, not protectable marks of any league or team (assessed 2026-07-20; revisit before any public ship). | Operator (with counsel if the game ships publicly) | Assumed for prototype |

## 7. Decision records

Five decisions were contested, argued, and signed off by the operator on 2026-07-20; each is recorded in this folder as `adr-001` through `adr-005` (resolution randomness; turn structure; inspiration-mapping; role structure; ready-made playbooks). Their outcomes are binding on `game-rules-spec.md` and everything downstream.
