---
Item_ID: gridlock-adr-001
type: BASEPLATE_Design_Document
title: "Gridlock — ADR-001: Randomness in Play Resolution"
baseplate_Product_Slug: "GL"
baseplate_Doc_Class: adr
baseplate_Layer: 2
baseplate_Document_Status: shipped
baseplate_Precedence_Rank: 4
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
---

# GL-ADR-1 — Randomness in Play Resolution

**Status:** Accepted — decided by the operator 2026-07-20 (options and recommendation presented by the drafting assistant; recommendation accepted).

## Context

Gridlock's inspiration lineage pulls in opposite directions. Hidden-information card games in the *Android: Netrunner* tradition are fully deterministic: identical decisions produce identical results, and all uncertainty comes from what the opponent has concealed. Real football is the opposite: a perfectly called play can still fail on execution. The choice governs skill expression, balance measurability, and the emotional texture of every down. It also constrains the engine: whatever is chosen must preserve exact replayability of match logs (PRD GL-NFR-1).

## Options

1. **No randomness.** Pure deterministic matchup math; all uncertainty from concealment and bluffing. Most skill-forward, cleanest to balance; risks feeling like "chess in helmets" — no broken tackles, ever.
2. **Bounded variance.** The deterministic matchup of the two revealed cards is computed first and defines a bounded outcome range (e.g., a beaten defense concedes 6–12 yards, never 2); a seeded random draw selects within that range. Extreme events (turnovers, breakaways) exist only where the matchup opens them.
3. **High variance.** Dice-like rolls dominate; play calls shift odds rather than determine structure. Maximal drama, casual-friendly; undermines the mind-game core and makes balance reads noisy.

## Decision

**Option 2 — bounded variance.** Matchup first, luck second, and luck only within the window the matchup defines.

## Consequences

- The rules engine computes resolution in two stages: a deterministic **matchup stage** (produces an outcome band and event set) and a **draw stage** (a seeded random selection within that band). `game-rules-spec.md` owns the band semantics; `technical-design.md` owns the random-stream mechanics.
- Every match uses a recorded seed; the match log records every draw, so replays are exact (GL-NFR-1 is unaffected by this decision).
- All variance bands are balance data (GL-NFR-5): playtesting can dial luck up or down — including to zero, which recovers Option 1 — without touching code.
- Turnovers and breakaways are structurally gated: no coin-flip disasters from a neutral matchup. This is the "feel-bad" guardrail.
- The sim harness must report outcome *distributions*, not just averages, since balance now has spread.

## Alternatives rejected

Options 1 and 3, as argued above. Option 1 remains recoverable at any time by zeroing the variance bands in balance data — a deliberately cheap escape hatch if playtesting finds the luck unfun.
