---
Item_ID: gridlock-architecture
type: BASEPLATE_Design_Document
title: "Gridlock — Architecture"
baseplate_Product_Slug: "GL"
baseplate_Doc_Class: architecture
baseplate_Layer: 2
baseplate_Document_Status: shipped
baseplate_Precedence_Rank: 3
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
---

# Gridlock — Architecture

> **What shape the system is.** Four components, one load-bearing boundary: the rules engine is a pure, deterministic library with no I/O, and everything else orbits it. Requirements live in `prd.md`; gameplay semantics in `game-rules-spec.md`; component internals in `technical-design.md`; wire formats in `interface-contracts.md`; operations in `runbook.md`.

## 1. Components & boundaries

```
                    ┌──────────────────────── VPS ───────────────────────┐
 ┌──────────┐  WSS  │  ┌──────────────┐        ┌──────────────────────┐  │
 │ PWA      │◄─────►│  │ Game server  │ calls  │ Rules engine         │  │
 │ client   │ HTTPS │  │ (Node)       │───────►│ (pure TS library)    │  │
 │ (browser)│◄─────►│  │ sessions ·   │        │ state machine ·      │  │
 └──────────┘       │  │ matchmaking ·│        │ resolution · RNG     │  │
      ▲             │  │ shot clock · │        └──────────────────────┘  │
      │static+TLS   │  │ AI opponent  │             ▲                    │
 ┌────┴─────┐       │  └──────┬───────┘             │ same library,     │
 │ Caddy    │       │         │ persists            │ no server needed  │
 │ (reverse │       │  ┌──────▼───────┐        ┌────┴─────────────────┐  │
 │  proxy)  │       │  │ SQLite +     │        │ Sim harness (CLI)    │  │
 └──────────┘       │  │ match-log    │        │ batch matches ·      │  │
                    │  │ files        │        │ replay · statistics  │  │
                    │  └──────────────┘        └──────────────────────┘  │
                    └────────────────────────────────────────────────────┘
```

| # | Component | Responsibility | Explicitly NOT its job |
|---|---|---|---|
| C-1 | **Rules engine** (pure TypeScript library) | All gameplay adjudication per `game-rules-spec.md`: state machine, matchup resolution, RNG streams, match-log event emission, balance-data validation (GL-RS-38). Deterministic: same seed + same inputs → same outputs (PRD GL-NFR-1). | No network, no disk, no clocks, no timers, no global state. The shot clock lives in the server; the engine only receives "commit X" or "auto-commit" as inputs. |
| C-2 | **Game server** (Node process) | WebSocket sessions, anonymous identity, matchmaking queue + AI backfill (PRD GL-FR-10), shot-clock timers, driving the engine, persisting matches and logs, serving the hidden-information boundary (PRD GL-NFR-2). Hosts the AI opponent as an in-process policy module. | No gameplay decisions of its own — every game outcome comes from the engine. |
| C-3 | **PWA client** (browser) | Rendering, input, the reveal moment, reconnection. Holds only what the server sends it. | Never adjudicates; never holds concealed opponent information (structurally enforced: the server never sends it — `interface-contracts.md`). |
| C-4 | **AI opponent + sim harness** | The AI is a deterministic policy module over engine-visible state (one side's legal view). The sim harness is a CLI that runs AI-vs-AI or scripted matches directly against the engine library — no server, no network — emitting the same match logs plus aggregate statistics (PRD GL-FR-14). | The harness never talks to production; the AI never sees concealed opponent state (same information rules as a human). |

**The load-bearing boundary:** C-1 is imported by both C-2 (production) and C-4 (verification). Because the engine is pure and the sim harness exercises it directly, thousands-of-matches verification never needs the network stack — this is what makes an agent-built game testable (the PRD's sim-harness requirement exists for exactly this reason).

## 2. Data flow (one down, human vs human)

1. Server starts the down: sends each client its **own** legal view (hand, CP, public state) — `phase` message.
2. Each client sends `commit` (or `declare` on 4th down, or `scout`). Server validates legality against the engine's legal-move query; illegal → `error`, state unchanged.
3. When both commitments are locked (or shot clock fires auto-commit, GL-RS-12), the server calls the engine's `resolveDown`.
4. Engine returns: reveal payload, ordered draw records, resolution outcome, next state, and match-log events. Server appends events to the match log (write-ahead: log before broadcast), persists, then broadcasts `reveal` + `resolution` to both clients simultaneously.
5. On possession/match end, the server records the result row (SQLite) and closes or advances.

Solo play is the identical flow with the AI policy module supplying one side's commitments in-process. Simulation is the identical engine call sequence driven by the harness loop.

## 3. Deployment topology

One VPS, three OS-level pieces (details and procedures: `runbook.md`):

- **Caddy** (system service): terminates TLS, serves the built client's static files, reverse-proxies `/ws` (WebSocket) and `/healthz` to the game server on localhost.
- **Game server** (systemd service `gridlock.service`): single Node process, localhost-only port. One process is sufficient for prototype scale; the match-log write-ahead discipline (§2.4) bounds crash damage (crash policy: `technical-design.md` §6).
- **State on disk**: one SQLite database file + one append-only match-log file per match (paths and schemas: `data-dictionary.md`).

No other infrastructure exists: no container runtime, no external database, no message queue, no third-party service (PRD non-goal: no external integrations). This is deliberate — the fewer moving parts, the shorter the runbook a stranger must execute.

## 4. Views (C4-style, brief)

- **Context:** two anonymous players (or one player + built-in AI) ↔ Gridlock system; no external systems.
- **Containers:** browser PWA · Caddy · Node game server · SQLite/log files · sim-harness CLI (operator/agent-side, not player-facing).
- **Components (server):** session registry · matchmaking queue · match orchestrator (one per live match: owns shot clocks, engine instance, log writer) · AI policy · persistence adapter.
- **Code:** `technical-design.md` owns module-level structure.

## 5. Alternatives rejected

| Alternative | Why rejected |
|---|---|
| Engine as a separate service (network-called) | Adds a hop and a failure mode for zero prototype benefit; a library import gives the sim harness free direct access. |
| PostgreSQL / external DB | Operationally heavier on a single VPS; SQLite is sufficient for anonymous prototype data and one-file backup (`runbook.md`). |
| Client-side adjudication with server checksums | Violates PRD GL-NFR-2 structurally — hidden information must never reach the client. |
| Native/Unity client | Rejected at requirements level (PRD GL-FR-11: web-first; store builds deferred). |
| Horizontal scaling provisions (multi-process, Redis pub/sub) | Prototype explicitly serves a playtest population; scaling machinery is gold-plating until a real population exists (PRD non-goals 2–4). |
