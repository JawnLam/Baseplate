---
type: BASEPLATE_Narrative_Document
Item_ID: gridlock-product-interview
title: "Gridlock — Product Interview"
baseplate_Product_Slug: "GL"
baseplate_Doc_Class: product-interview
baseplate_Layer: 0
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
---

# Gridlock — Product Interview (PI-1..PI-12)

*Conducted 2026-07-20, one question at a time (F1). Two answers (PI-4, PI-6) were **operator-delegated**: the assistant proposed a concrete answer and the operator ratified it explicitly — marked below. Nothing here is a silently absorbed assumption (PF-5).*

- **PI-1 · One-liner.** A digital **card game** with *Android: Netrunner*'s asymmetric two-role structure — the offense calling plays to advance versus the defense setting hidden schemes — where the cards are real, named American football plays (e.g., Counter Trey, Cover 2, play-action). Confirmed verbatim by the operator ("bingo"). Not a board/field sim; the plays ARE the cards/actions.

- **PI-2 · Builder.** **AI agent.** *(Selection consequence: maximum L3 precision — the documents are the agent's only channel; interface contracts mandatory; the stranger test is the natural ship gate.)*

- **PI-3 · Operator↔builder relationship.** The operator **directs, orchestrates, supervises, oversees, and possibly interactively collaborates** with the agent. Same-person direction, no handoff to others, no contract. The operator is **non-technical** (self-described, PI-8 exchange). *(L0 collapses into the PRD; no L6 obligations from relationship.)*

- **PI-4 · Interface surfaces.** *(Operator-delegated with "Marvel SNAP on iOS" offered as a suggestion — explicitly NOT a hardened anchor; assistant proposed, operator green-lit.)* **(a) Mobile-first progressive web app** — portrait, touch-first, one-thumb play, 3–6 minute matches, simultaneous play-reveal ("the snap") as the signature moment; web-first chosen because the builder is an AI agent (no store gatekeeping, instant browser-drivable end-to-end testing); store-wrapped native builds are a later, separate decision, not a founding constraint. **(b) Server-authoritative rules engine** — hidden defensive information means the client renders but never adjudicates. **(c) Networked PvP via matchmaking + an AI opponent** for solo play and backfill. **(d) No public/third-party API** — the client-server protocol is an internal, versioned, frozen contract. **(e) A headless CLI/simulation harness** for the rules engine — not player-facing; the agent's verification workhorse (scripted matches at scale).

- **PI-5 · External parties.** **Public end users eventually** — the game ships to strangers, but not in the prototype phase. **IP posture (operator-directed): stay clean of both** *Android: Netrunner* IP (mechanics-inspiration only; no names, art, or theme — mechanics are not copyrightable, expression is) and NFL trademarks (no licensed logos or mascots; **no team names** — operator called team branding "useless decoration… gold plating" at this stage). Priority signal, operator verbatim: *"The most important thing to develop now is the game interface and game mechanics"* — what drives the offense, what drives the defense, how the collision of the two is resolved, play schemes, diagrams, play-action logic.

- **PI-6 · Data sensitivity.** *(Operator-delegated; ratified conditional on the deferred-scope register below.)* **Persistent data yes, sensitive data no.** No accounts, no PII — by design, not omission: players are anonymous (device-generated ID + throwaway guest handle); nothing stored can identify a human, so the prototype has no GDPR/COPPA surface and earns no standalone threat model — with a **named revisit trigger** (the moment real accounts arrive, REVISE-STACK adds the threat model). What IS persisted serves the engine: **every match writes a complete, replayable match log** (play calls, hidden-info reveals, resolution outcomes) — the debugging, determinism-proof, and balance-analysis asset; the engine is **deterministic and replay-driven from day one**. Playbooks/decks stored server-side under the anonymous ID; no purchase/collection economy in the prototype. Future-proofing hook: all persistence keys off an opaque internal `player_id` so accounts later attach by mapping, never by migrating game data.

- **PI-7 · Components / integration.** **Four interacting components:** (1) the **rules engine** (the crown jewel — deterministic, replay-driven), (2) the **game server** wrapping it (matchmaking, sessions, authoritative state), (3) the **PWA client**, (4) the **AI opponent + simulation harness pair** riding on the engine. **External integrations: none** — no third-party services in the prototype. Confirmed by operator ("Good."). *(≥3 components + persistent data → standalone L2 architecture earned.)*

- **PI-8 · Runtime shape.** **Runs continuously on the operator's Hostinger VPS** (operator owns one; existence operator-attested 2026-07-20). The AI agent installs and maintains the deployment; the operator never touches a terminal. *(L5 runbook earned, written so the agent or a total stranger can operate it with zero technical input from the operator.)*

- **PI-9 · Lifespan / revision.** **Heavy playtest-driven revision expected.** Operator verbatim: *"play-testing is a huge part of the process. We do not know how well our rules will hold up in real play. We need to balance logic & rigor with fun & enjoyment."* Consequence — a deliberate **freeze line**: the engine skeleton is frozen (turn structure, hidden-info reveal protocol, replay format, client-server contract) while everything that tunes balance (play stats, costs, matchup tables, yardage curves) lives in **data files, not code**, so a balance pass is an edit, not a rebuild. "Logic & rigor vs. fun & enjoyment" is recorded as an explicit design tension the playtest loop exists to resolve.

- **PI-10 · Deadlines.** **None.** "It's done when it's good" — quality gates pace the work. *(No WBS/plan earned.)*

- **PI-11 · Contested decisions already visible.** Three named and confirmed: **(a) randomness in play resolution** (pure deterministic card math à la Netrunner vs. dice-like variance à la real football); **(b) turn structure** (simultaneous-reveal both-commit-then-snap vs. alternating-with-reactions à la Netrunner runs); **(c) literalness of the Netrunner mapping** (is a drive a "run"? are defenders "ice"?). Plus a **standing rule** (operator delegated further identification to the assistant): any decision surfaced during generation that could reasonably go two ways becomes an ADR draft with a recommendation for operator sign-off — never a silent choice (PF-5 guard).

- **PI-12 · Money / formal obligation.** **None** crosses a boundary. License **defaulted** (not operator-decided) to private / all rights reserved; flagged as flippable at any time. *(No SOW; L6 reduces to license.)*

---

## Deferred-scope register (operator-mandated: "make a note that we will eventually need all the other stuff")

| Deferred item | Revisit trigger (REVISE-STACK) |
|---|---|
| Real accounts / auth (email, OAuth) | The moment identity beyond anonymous device-ID is needed |
| PII handling + standalone threat model | Arrives WITH accounts — named trigger from PI-6 |
| Collection / economy / monetization | If/when the game moves past prototype toward shipping to strangers |
| Ratings / ranked matchmaking | When real player population exists |
| Social features | When real player population exists |
| Store-wrapped native iOS/Android builds | If the PWA earns it (PI-4 records this as a later, separate decision) |
| SLO / availability commitments | When strangers depend on the service |
| License decision (currently defaulted private/all-rights-reserved) | Whenever the operator chooses |

---

## Reflected picture (confirmed by operator 2026-07-20: "Gridlock works — confirmed, proceed")

A digital card game — Netrunner's asymmetric hidden-information mechanics reskinned as American football, offense calling plays against concealed defensive schemes — built entirely by an AI agent under direct, non-technical operator supervision. Ships as a mobile-first web app backed by a server-authoritative, deterministic, replay-driven rules engine, with PvP matchmaking, an AI opponent, and a headless simulation harness as the engine's proving ground. The prototype is anonymous and IP-clean, runs continuously on the operator's Hostinger VPS, and carries a deferred-scope register for accounts, threat model, and economy. Rules are expected to churn through playtesting — engine skeleton frozen, balance data fluid — with no hard dates, and three ADRs queued: randomness, turn structure, and mapping literalness.
