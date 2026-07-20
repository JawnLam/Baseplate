---
Item_ID: gridlock-adr-003
type: BASEPLATE_Design_Document
title: "Gridlock — ADR-003: Literalness of the Inspiration Mapping"
baseplate_Product_Slug: "GL"
baseplate_Doc_Class: adr
baseplate_Layer: 2
baseplate_Document_Status: shipped
baseplate_Precedence_Rank: 6
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
---

# GL-ADR-3 — Literalness of the Inspiration Mapping

**Status:** Accepted — decided by the operator 2026-07-20 (options and recommendation presented by the drafting assistant; recommendation accepted).

## Context

Gridlock is mechanically inspired by *Android: Netrunner* (asymmetric roles, hidden information, resource economy, press-your-luck attacks). How literally to borrow is both a design and an intellectual-property posture question. The product must stay IP-clean (PRD GL-NFR-4): game mechanics as such are not protected, but names, art, setting, and distinctive expression are. The football theme must also actually fit — borrowed machinery that fights the metaphor produces a worse game, not a safer one.

## Options

1. **Football-first with borrowed economy.** The core loop is pure football (downs, yards, drives, scoring). Specific proven subsystems are borrowed and re-expressed natively: a tight action/resource economy; face-down defensive schemes that trigger like traps when the right play hits them; press-your-luck drive tension. The inspiration's engine in football's body.
2. **Deep-structure inspiration only.** Borrow only the abstract skeleton (asymmetry, hidden info, risk-reward); invent all concrete mechanics natively. Safest creative distance; discards proven machinery; more design iterations to find the fun.
3. **Literal translation.** One-to-one concept mapping (a drive as a "run", defenders as cost-gated barriers). Fastest to spec; strains the football theme, inherits complexity tuned for long tabletop sessions, and hugs the inspiration uncomfortably closely.

## Decision

**Option 1 — football-first with borrowed economy.**

## Consequences

- **The borrowed subsystems, enumerated** (this list is the boundary of borrowing; `game-rules-spec.md` implements them in football-native form):
  1. a per-down **action/resource economy** (limited coaching resources spent on drawing plays, scouting, and powering stronger calls);
  2. **face-down trigger schemes** — defensive cards set concealed, revealed only when their trigger condition is met by the offense's play;
  3. **press-your-luck drive tension** — the escalating risk decision of continuing a drive versus banking field position (most sharply at fourth down);
  4. **information-asymmetry tooling** — scout/peek effects that sell information as a purchasable resource.
- **Vocabulary rule:** every player-facing and document-facing game term is football-native. No *Netrunner* terminology (no "ice", "rez", "run", "corp", "runner" as game terms) appears anywhere in the product, its data files, or this stack's gameplay vocabulary. This is mechanically checkable and is part of GL-AT-18.
- Design work not covered by a borrowed subsystem (e.g., yardage modeling, scoring cadence) is invented football-first in `game-rules-spec.md`.

## Alternatives rejected

Options 2 and 3, as argued above. Option 3 is additionally rejected on IP posture: mechanical borrowing at the *system* level with original expression is the deliberate ceiling.
