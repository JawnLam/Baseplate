---
type: Fleeting
timestamp: "2026-07-20T00:00:00Z"
Item_ID: gridlock-design-state
title: "Gridlock — Design State"
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
doc_type: baseplate-cartridge-design-state
baseplate_Cartridge_Phase: "selection-locked"
---

# Gridlock — Design State

> Read at session start, written at session end (P2). Multi-session engagement.

## Current phase

**`selection-locked`** (as of 2026-07-20, session 01). Interview PI-1..PI-12 captured and operator-confirmed; selection record locked with 14-document stack, `GL-` ID scheme, and precedence declaration. **Next step: generation (Step 4 of BOOTSTRAP-NEW-STACK), in dependency-layer order, starting with `Artifacts/prd.md`.** Load `04-GENERATION-STANDARDS.md` + the relevant `_types/*` before drafting each document.

## Open threads

1. **ADR-001/002/003 are undecided** — randomness, turn structure, mapping literalness. Generation of the game-rules-spec BLOCKS on these three decisions: draft each ADR with options + a recommendation, get operator sign-off, then freeze the outcome into the rules spec. Do not draft rules prose that silently assumes an ADR outcome (PF-5).
2. **Technology stack unchosen.** Every technology named in the technical design requires a prior `_dependency-log.md` entry (PF-1). Candidate constraints already fixed: agent-buildable, browser-testable, deployable to a Hostinger VPS by an agent, deterministic engine testable headlessly.
3. **Hostinger VPS specs unknown** (plan tier, RAM, OS). Needed for the runbook. Operator-only question — ask when drafting L5.
4. **Play vocabulary source.** "Real, named American football plays" — generation needs a concrete starter set (e.g., Counter Trey, Cover 2, play-action) chosen for recognizability without any team/league branding (PI-5). This is content design inside the rules spec / balance data, not a dependency.
5. **Standing ADR rule** (PI-11): new two-way decisions surfaced in generation → ADR draft + recommendation → operator sign-off.

## Operator directives to honor every session

- Non-technical operator: translate jargon; never require terminal work.
- Deferred-scope register (in `_product-interview.md`) is a promise — carry it into the PRD's non-goals/assumptions sections explicitly.
- Priority: game mechanics + interface above all else.
- IP-clean: no Netrunner names/art/theme; no NFL team names/logos/mascots.

## Session log index

- `Sessions/2026-07-20-session-01.md` — interview + selection (this session).
