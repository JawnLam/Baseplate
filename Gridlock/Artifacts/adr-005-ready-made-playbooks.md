---
Item_ID: gridlock-adr-005
type: BASEPLATE_Design_Document
title: "Gridlock — ADR-005: Ready-Made Playbooks for the Prototype"
baseplate_Product_Slug: "GL"
baseplate_Doc_Class: adr
baseplate_Layer: 2
baseplate_Document_Status: internally-consistent
baseplate_Precedence_Rank: 8
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
---

# GL-ADR-5 — Ready-Made Playbooks for the Prototype

**Status:** Accepted — decided by the operator 2026-07-20 (options and recommendation presented by the drafting assistant; recommendation accepted).

## Context

Deck construction is a defining pleasure of the card-game genre Gridlock draws on — and a whole second game system: a builder interface, legality rules, and a combinatorial balance surface. The prototype's single job (PRD §1) is to test whether the down-by-down play-calling duel is fun. Every system that isn't the match engine competes with that test for effort and confounds its balance reads.

## Options

1. **Ready-made playbooks only.** The prototype ships a small set of pre-built two-sided playbooks (per GL-ADR-4) with distinct strategic personalities; players pick one before a match.
2. **Custom playbook builder now.** Players assemble playbooks from the full play pool before matches. More depth and personalization from day one; adds a UI surface plus legality and balance rules before the core loop has proven itself.

## Decision

**Option 1 — ready-made playbooks only.** At least four, per PRD GL-FR-8.

## Consequences

- `game-rules-spec.md` and the balance data define the starter playbooks; the working archetype set is: **ground-and-pound** (run-heavy, clock-squeezing), **air raid** (pass-heavy, boom-or-bust), **blitz-happy** (defense gambles for disruption), and **bend-don't-break** (defense concedes yards, protects the end zone). Final names and contents are content design owned by the rules spec.
- The balance surface is bounded: a known, finite set of playbook pairings, exhaustively coverable by the sim harness.
- No builder UI, no collection storage, no legality validator is designed anywhere in this stack.
- **Custom playbook construction is deferred, not rejected** — revisit trigger: the core loop is proven fun in playtests (PRD non-goal 5). When it arrives, playbook legality rules become new requirements in a stack revision.

## Alternatives rejected

Option 2, as argued above — deferred on prototype-focus grounds, not on the merits of deck-building itself.
