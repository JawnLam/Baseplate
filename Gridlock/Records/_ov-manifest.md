---
type: Fleeting
timestamp: "2026-07-20T00:00:00Z"
Item_ID: gridlock-ov-manifest
title: "Gridlock — Product Cartridge Manifest"
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
doc_type: baseplate-cartridge-manifest
product_slug: "GL"
builder: "AI agent, operator-directed (non-technical operator supervising interactively)"
---

# Gridlock — Product Cartridge Manifest

> **The third cartridge; the first game.** A digital card game combining *Android: Netrunner*-style asymmetric hidden-information mechanics with real, named American football plays. The name "Gridlock" (football grid × Netrunner lockdown) was proposed by the assistant and ratified by the operator 2026-07-20.

## Product

A **digital card game** where the cards are real football plays: the offense calls plays to advance; the defense sets concealed schemes; the collision of the two resolves on a server-authoritative, deterministic, replay-driven rules engine. Mobile-first progressive web app, PvP matchmaking plus an AI opponent, headless simulation harness for engine verification. Prototype phase: **the rules engine and game interface are the product**; accounts, economy, and social features are explicitly deferred (see the deferred-scope register in `_product-interview.md`).

## Builder & operating posture

Built **entirely by an AI agent**; the operator directs, supervises, and ratifies but is **non-technical** and never touches a terminal. Consequences: maximum Layer-3 precision (the documents are the only channel), a runbook written so an agent or stranger can operate the deployment without operator technical input, and a standing rule that any decision that could reasonably go two ways surfaces as an ADR draft for operator sign-off — never a silent choice (PF-5 guard).

## Constraints that shape everything

- **IP-clean by design:** Netrunner is mechanical inspiration only — no names, art, or theme from it; generic football with no NFL team names, logos, or mascots (operator: team branding is "useless decoration" at this stage — PF-4 applied by the operator themself).
- **Playtest-driven churn expected (PI-9):** engine skeleton frozen (turn structure, reveal protocol, replay format, client-server contract); all balance data (play stats, costs, matchup tables, yardage curves) lives in data files, not code.
- **Prototype data posture (PI-6):** no accounts, no PII; anonymous device-generated IDs; persistence exists to serve the engine (complete replayable match logs).

## Cartridge contents

- `_product-interview.md` — the PI-1..PI-12 record + deferred-scope register.
- `_selection-record.md` — inclusions/exclusions with rationale, `GL-` ID scheme, precedence declaration (S-3). **Locked 2026-07-20.**
- `_dependency-log.md` — dependency verifications (grows during generation).
- `_design-state.md` — cartridge phase + open threads (multi-session engagement).
- `Artifacts/` — the founding-document stack (the only folder the stranger receives).
- `Sessions/` — session logs.
- `stranger-test-log.md` — the Gate 2 record (stub until ship).
