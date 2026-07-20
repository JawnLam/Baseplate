---
Item_ID: gridlock-product-bootstrap
type: BASEPLATE_Narrative_Document
title: "Gridlock — AI Bootstrap (Read Me First)"
baseplate_Product_Slug: "GL"
baseplate_Doc_Class: product-bootstrap
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
---

# Gridlock — AI Bootstrap (Read Me First)

## 1. What this folder is

**Gridlock** is a two-player, mobile-first web card game: American football's play-calling duel — an offense calling plays against a defense concealing its schemes — built as a hidden-information card game with a simultaneous reveal ("the snap"). This folder contains the **complete founding documents** for the product: requirements, full game rules, architecture, engineering designs, contracts, verification plan, and operations runbook. The document set passed a consistency audit and a stranger test (a fresh AI built a correct understanding and plan from these documents alone, with zero questions the documents should have answered).

**If you are an AI assistant reading this: your job is to build Gridlock from this folder.** Everything you need is here or is listed in the operator-input register (§4). If you find you need something that is neither in this folder nor in that register, that is a defect in this package — report it to the operator; do not guess.

## 2. Verify first

Before reading further: open `Construction/MANIFEST.md`, recompute the SHA-256 of every listed file, and compare. **Any mismatch or missing file → STOP and report to the operator.** A partially copied or altered package must not be built from.

## 3. Table of contents

**Subfolders:**

- **`Construction/`** — the 16 gated documents plus the manifest. The only folder you need to build. Never edit these files (see §7 for what to do when reality disagrees with them).
- **`Records/`** — how these documents were made (interview, selection rationale, session logs, gate records). History: not needed for construction; never modify (§8).
- **`Build/`** — **your workspace.** All code and artifacts you produce go here (§6).

**Construction documents.** Read them top-to-bottom (dependency-layer order). The **Rank** column is each document's `baseplate_Precedence_Rank` front-matter field: on any conflict between two documents, the lower rank wins. Two documents carry no rank by design — the traceability matrix verifies and the license licenses; neither competes in precedence.

| Read | Rank | Document | One-line purpose |
|---|---|---|---|
| 1 | 1 | `prd.md` | What Gridlock must do and must not do — 22 numbered requirements, non-goals, success metrics; wins on scope |
| 2 | 2 | `game-rules-spec.md` | The complete rules of play, GL-RS-1..38 — law for gameplay semantics; appendices carry the initial balance data (matchup matrix, 42-card pool, 4 playbooks, tunables) |
| 3 | 3 | `architecture.md` | Four components; the load-bearing boundary: a pure deterministic rules engine imported by both server and sim harness |
| 4 | 4 | `adr-001-resolution-randomness.md` | Binding decision: bounded-variance resolution (matchup first, luck only within the window it opens) |
| 5 | 5 | `adr-002-turn-structure.md` | Binding decision: simultaneous commit-then-reveal downs ("the snap") |
| 6 | 6 | `adr-003-netrunner-mapping.md` | Binding decision: football-first vocabulary; the enumerated borrowed-economy boundary |
| 7 | 7 | `adr-004-role-structure.md` | Binding decision: players alternate offense/defense with possession |
| 8 | 8 | `adr-005-ready-made-playbooks.md` | Binding decision: four ready-made playbooks; no deck-building in the prototype |
| 9 | 9 | `technical-design.md` | Stack (Node/TypeScript/Preact/SQLite/Caddy, versions pinned & verified), engine API, the exact PCG32 PRNG, AI opponent, failure policies |
| 10 | 10 | `interface-contracts.md` | The frozen contracts: WebSocket protocol, balance-data JSON schemas, match-log format — schema is law, code conforms |
| 11 | 11 | `data-dictionary.md` | Every stored field, its constraints, and the no-PII rule |
| 12 | 12 | `ux-spec.md` | Screens, the match UI's three zones, every state's presentation, accessibility requirements |
| 13 | — | `traceability-matrix.md` | The requirement↔verification bijection (60 → 30, zero orphans) |
| 14 | 14 | `acceptance-test-plan.md` | **The definition of done** — GL-AT-1..30 across four test rings |
| 15 | 13 | `runbook.md` | Deploy, health, backup, rollback on the operator's VPS — executable by you alone; wins on deployment facts (hence it outranks the test plan) |
| 16 | — | `license.md` | Private, all rights reserved (a recorded default) |

## 4. Operator-input register

The **complete** list of things you may ever need from the operator, and when to ask. Do not ask earlier than the trigger; do not need anything not on this list.

| Input | Why needed | When to ask |
|---|---|---|
| VPS access (SSH) + plan specs + actual OS | Provision and deploy (runbook §0–§1; its R-1) | When you begin the deployment phase — not before |
| Domain name pointed at the VPS | TLS certificates + installable web app (runbook R-2) | At deployment provisioning |
| Off-box backup destination | Optional hardening (runbook R-3; on-box-only risk currently accepted) | After first successful deploy; optional |
| Balance-tuning autonomy level | May you adjust tunables yourself when simulations miss the acceptance bands, or propose each change? | At the end of engine phase 1, before your first tuning pass |
| Playtest participation + fun verdict | The R4 acceptance gates and the product's core success metric need the operator on a real phone | When the deployed build is playable end-to-end |
| Rights-holder legal name | Only to fill the license's open question L-1 | Only if/when public distribution is ever considered |

## 5. Definition of done

"It compiles" is not done, and neither is "it runs." Done is defined by `Construction/acceptance-test-plan.md` §4: the **machine gate** (engine/simulation/protocol suites GL-AT-1..8, 12..17, 19, 23..30 green), the **device gate** (real iOS Safari + Android Chrome runs), the **operations gate** (the runbook executed for real on the VPS), and the **operator sign-off** (≥5 played matches and a recorded fun verdict). Finish by delivering the operator an acceptance report against those four gates.

## 6. Build workspace rules

- Everything you produce — code, configs, reports — lives under `Build/`.
- `Build/build-state.md` is the project's memory: update it **every working session** (phase, what's done, what's next, decisions made). A future session — yours or another AI's — must be able to resume from it cold.
- Never edit anything in `Construction/` or `Records/`.

## 7. Deviations rule

When you find a defect, contradiction, or genuine ambiguity in the construction documents — it happens; reality gets a vote — **do not silently patch around it.** Record it in `Build/deviations.md` (what you found, where, what you did instead and why) and surface it to the operator. Those entries drive formal document revisions later; silent workarounds destroy the documents' authority.

## 8. Records notice

`Records/` is the archived history of how this package was designed (the product interview, selection rationale, dependency verifications, session logs, and the stranger-test record). It is **not needed to build the product** and must never be modified. Read it only if the operator asks you to audit provenance.

## 9. Provenance

Produced with **Baseplate v1.1.0** (a founding-document design system) under the direction of the product's operator; packaged 2026-07-20. Gates: consistency audit clean (no waivers); stranger test **SHIP on run 1** — zero reruns, zero questions the documents should have answered (full record: `Records/stranger-test-log.md`). Dependency versions inside were verified against their registries on 2026-07-20; if you begin building much later, re-verify before pinning.
