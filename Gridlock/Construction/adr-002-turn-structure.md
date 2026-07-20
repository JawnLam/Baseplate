---
Item_ID: gridlock-adr-002
type: BASEPLATE_Design_Document
title: "Gridlock — ADR-002: Turn Structure"
baseplate_Product_Slug: "GL"
baseplate_Doc_Class: adr
baseplate_Layer: 2
baseplate_Document_Status: shipped
baseplate_Precedence_Rank: 5
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
---

# GL-ADR-2 — Turn Structure (how a down plays out)

**Status:** Accepted — decided by the operator 2026-07-20 (options and recommendation presented by the drafting assistant; recommendation accepted).

## Context

The down is Gridlock's atomic beat; its structure sets the game's fundamental rhythm, the shape of the client-server protocol, and the pacing budget (PRD GL-NFR-6 targets 3–6 minute matches). *Android: Netrunner*'s signature rhythm is an alternating, reactive sequence (the attacker initiates; the defender responds step by step). Football's signature moment is the snap: both sides commit before the ball moves.

## Options

1. **Simultaneous reveal.** Each down, Offense commits a play and Defense commits a scheme, both face-down; when both are locked, "the snap" reveals them together and the down resolves. Fast, dramatic, protocol-simple.
2. **Alternating with reactions.** Offense initiates; Defense responds at decision points as the play unfolds (blitz now? commit the safety?). Deepest interaction; roughly doubles rules surface and match length; fights the mobile quick-match format.
3. **Commit plus one reaction each.** Both commit face-down; after the reveal, each side gets exactly one bounded adjustment (one audible per drive, one shift) before resolution. A middle ground at the cost of prototype complexity.

## Decision

**Option 1 — simultaneous reveal.** The snap is the game's signature dramatic moment and the pacing backbone.

## Consequences

- The protocol is **commit-then-reveal**: the server accepts both concealed commitments, locks them, then reveals both simultaneously. No client ever holds the opponent's unrevealed commitment (PRD GL-NFR-2).
- Each down is one round-trip of player decisions — the structure that makes a 3–6 minute match arithmetic work (`game-rules-spec.md` sets downs-per-match expectations; the shot clock bounds each decision).
- The user experience invests its signature animation/haptic beat in the reveal (`ux-spec.md` owns this moment).
- Mid-play decision depth is deliberately absent from the prototype skeleton. Option 3's bounded reaction step is recorded as a **playtest-addable module** (PRD assumption A-5): adding it later changes rules data and one protocol message family, not the skeleton — this forward-compatibility requirement binds `technical-design.md` and `interface-contracts.md`.

## Alternatives rejected

Options 2 and 3, as argued above. Option 2 is permanently rejected for the mobile format; Option 3 is deferred, not rejected — its revisit trigger is playtest evidence that downs feel too static.
