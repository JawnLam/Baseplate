---
Item_ID: gridlock-adr-004
type: BASEPLATE_Design_Document
title: "Gridlock — ADR-004: Role Structure Across a Match"
baseplate_Product_Slug: "GL"
baseplate_Doc_Class: adr
baseplate_Layer: 2
baseplate_Document_Status: shipped
baseplate_Precedence_Rank: 7
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
---

# GL-ADR-4 — Role Structure Across a Match

**Status:** Accepted — decided by the operator 2026-07-20 (options and recommendation presented by the drafting assistant; recommendation accepted).

## Context

*Android: Netrunner* locks each player into one asymmetric role for an entire game. Football alternates possession. Gridlock must choose whether a match is one permanent Offense versus one permanent Defense, or whether players swap sides as possession changes. The choice shapes the playbook concept, the match state machine, the win condition, and how balance is measured.

## Options

1. **Alternating possessions.** Players swap Offense/Defense roles when possession changes (any drive ender — score, turnover, punt, turnover on downs, safety, missed field goal). A playbook is a complete "team": offensive plays plus defensive schemes. The final score settles the match.
2. **Fixed roles per match.** One player is Offense for the whole match against a drive limit; the other is Defense. Sharper asymmetry, simpler state machine; delivers half the football experience per match and needs an artificial win yardstick (beat-the-clock, or home-and-away paired matches).

## Decision

**Option 1 — alternating possessions.**

## Consequences

- A **playbook is a two-sided team bundle** (offensive plays + defensive schemes) — this defines the playbook data entity in `data-dictionary.md` and the ready-made playbook requirement (PRD GL-FR-8).
- The match is structured as a **fixed, even number of possessions per player** (count set in balance data; `game-rules-spec.md` defines the structure and the tie-break), so both players get identical offensive opportunity and the score is a fair verdict.
- Both players experience both sides of the mind game every match — the full play-calling fantasy, and double the strategic surface each playbook must support.
- The engine models an explicit **possession state machine** whose transitions are exactly the drive enders PRD GL-FR-1 names and the rules specification owns (score, turnover, punt, turnover on downs, safety, missed field goal); balance is measured per-possession in the sim harness.
- Per-down asymmetry is preserved (Offense and Defense play by different rules within a down); only match-level identity is symmetric.

## Alternatives rejected

Option 2, as argued above — rejected primarily because each match would deliver only half the product's fantasy, and its win condition is artificial. If a future mode wants pure fixed-role play (e.g., a "two-minute drill" challenge mode), it can be built as a mode on the same engine without revisiting this record.
