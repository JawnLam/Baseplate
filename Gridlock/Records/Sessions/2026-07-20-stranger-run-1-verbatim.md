# Stranger Test Run 1 — verbatim output
# Stranger: fresh Claude Opus instance (different model family than the drafting model), 2026-07-20
# Fixed prompt used verbatim; folder: Gridlock/Artifacts/ (16 documents)

## (a) One-page restatement: what is being built, and why

**Gridlock** is a fast, two-player, mobile-first web card game that turns American football's play-calling duel into a hidden-information mind game. It is a **prototype** whose entire purpose is to test one hypothesis: *the down-by-down play-calling duel — concealed commitments, a dramatic simultaneous reveal ("the snap"), resolved down by down — is fun enough that people ask for a rematch.* Everything in the stack is scoped to prove or disprove that cheaply and credibly.

**The design lineage** is *Android: Netrunner* (asymmetric roles, hidden information, a resource economy, press-your-luck tension), re-expressed entirely in football-native terms for a mainstream US audience. The IP posture is deliberate: mechanics are borrowed at the *system* level; all names, art, and vocabulary are original football language, with zero Netrunner terminology and zero NFL/NCAA/team/player branding (enforced, not merely intended).

**How a game plays.** A match is a fixed, even set of possessions (default 2 per side, plus tie-break rounds). Players **alternate** offense and defense as possession changes — a playbook is a two-sided "team" (20-card offense deck + 16-card defense deck), and the prototype ships **four ready-made playbooks** with distinct personalities (Ground & Pound, Air Raid, Blitz City, Bend Don't Break). No deck-building. Each down runs four phases: upkeep → secret commit → simultaneous snap → resolution. Resolution is **bounded variance** (ADR-001): the matchup of the two revealed cards defines an outcome band and event probabilities deterministically; then a seeded random draw picks within it. Turnovers and breakaways are only reachable when the matchup opens them — no coin-flip disasters. A **Coaching Points** economy funds card costs and buying "scouts" (peeks at an opponent's hand card), so calling power is always a real choice. A 20-second shot clock with safe auto-commit keeps matches at a target 3–6 minutes.

**Architecture.** Four components around one load-bearing boundary: a **pure, deterministic TypeScript rules engine** (no I/O, no clock, all randomness from a recorded PCG32 seed) imported by both the **Node game server** (WebSocket sessions, matchmaking with AI backfill, shot clock, server-authoritative hidden-information enforcement, SQLite + append-only match logs) and a **headless sim harness** (batch AI-vs-AI matches for determinism/balance verification). A thin **Preact PWA** renders only what the server sends and never adjudicates. Players are anonymous (device-ID + guest handle, no PII, no login). Every match produces a byte-exact replayable log. Every tunable number lives in balance-data JSON, so balancing is a data edit, not a rebuild. It all runs on one commodity VPS behind Caddy, operable end-to-end from the runbook by a stranger or an agent.

**Definition of done:** four green gates — machine (engine/sim/protocol tests), device (real iOS Safari + Android Chrome), operations (runbook executed on the real VPS), and the operator's fun verdict over ≥5 matches. 60 requirements/rules map 1:1 to 30 acceptance tests with zero orphans.

## (b) Plan for the first phase of work

The architecture makes the sequencing obvious: **the engine is the keystone** — the server, the sim harness, and every determinism/balance test all depend on it, and it has zero runtime dependencies. Phase 1 should be "**engine + sim harness green**," delivering a fully playable, verifiable game *before* any network or UI code exists. This is also the fastest path to the fun hypothesis, since the operator can play the AI via the harness/CLI.

**Step 0 — Repo & contracts skeleton.** Stand up the npm-workspaces monorepo (`engine`, `protocol`, `server`, `client`, `sim`, `data`) with pinned stack versions (Node 24 LTS, TS 7 with the approved TS-5 fallback, Vitest, Zod). Encode the three contracts and balance-data schemas in `packages/protocol` as Zod schemas first — schema is law, everything conforms to it. Add the lint rule banning `client → engine` imports on day one.

**Step 1 — Balance data as data.** Transcribe game-rules-spec Appendices A–D into the four JSON files (`tunables`, `matchup` [30 cells], `pool`, `playbooks`) and implement `validateBalanceData` with the GL-RS-38 invariants (deck sizes 20/16, ≥4 zero-cost per deck, pool membership, cost-from-tier derivation). The engine refuses invalid sets whole.

**Step 2 — The pure engine (C-1).** Build in dependency order:
- PCG32 PRNG exactly as specified (state constant, XSH-RR, rejection-sampled `uniformInt`, Fisher–Yates), with per-draw purpose-tagged log events.
- Core types (`MatchConfig`, immutable `MatchState`, `PlayerView`, `DownInput`, `EngineEvent`) and the pure API (`createMatch`, `legalInputs`, `applyInput`, `resolveDownIfReady`, `view`, `replay`).
- The state machine and the full down cycle: CP economy, hand/deck/draw/reshuffle, no-legal-card redraw, scouting, the three-stage resolution (matchup lookup → fixed-order tier/clause modifiers → fixed-order event+yardage draws), declared 4th downs (punt/FG), possession transitions, safeties, tie-breaks, forfeit/concede.
- **`replay()` from the start** — determinism is a design constraint, not a later test; build it alongside emission so divergence is caught immediately.

**Step 3 — Rings R1 + R2 green.** Write the Vitest suites for GL-AT-23..30 (every GL-RS rule, using the scripted-seed + scripted-input mechanism) and the AI policy module (EV over a belief distribution). Then run the sim harness: `sim run --matches 1000`, replay all logs (GL-AT-15, target zero divergences), and produce the aggregate stats report (win rates, event rates, duration distribution). This simultaneously validates determinism, the balance bands, and the pacing arithmetic — and gives the operator a first, UI-free "is this fun?" read via scripted or AI play.

**Deliverable at end of Phase 1:** a data-driven, deterministic, fully rules-complete game engine with an AI opponent, provably replayable across 1,000 matches, with the machine-gate rules suites green. Phase 2 (server + protocol + hidden-info enforcement + matchmaking) and Phase 3 (PWA + the snap + device gates + deployment) build on this without ever revisiting gameplay semantics.

**Why this order:** it front-loads the load-bearing, hardest-to-change, most-tested artifact; it makes the fun hypothesis testable at the earliest possible moment; and it defers all I/O, network, and UI risk behind a component that can be exercised thousands of times without any of them.

## (c) Questions I would need answered before starting

**Hard blockers (from the docs' own open-questions registers):**
1. **VPS specs / OS image** (PRD A-1, Runbook R-1) — RAM, disk, actual OS. Blocks first provisioning and finalizing the runbook. *Only blocks deployment, not engine work* — so Phase 1 can start immediately, but this must be resolved before Phase 3.
2. **Domain name pointed at the VPS** (Runbook R-2) — required for TLS issuance and PWA installability. Same: blocks deployment only.
3. **Off-box backup destination** (Runbook R-3) — currently on-box only, risk accepted; confirm that acceptance stands.

**Questions I'd want answered to avoid rework, that the docs leave genuinely open:**
4. **TypeScript 7 vs 5 fallback (TD-A-2):** which do we commit to at first build? I'd want to know before pinning `package.json`, since several libraries (Preact, ws, better-sqlite3, Zod, Vitest) need TS 7 support verified at build time. Is the operator comfortable defaulting to TS 5.x-latest if any lags?
5. **Balance-data ownership handoff:** the ⚙ values are all 2026-07-20 design estimates. Who owns tuning during Phase 1 — do I run sims and propose changes, or is there a "rules spec author" I should route balance findings to before editing the JSON?
6. **Initial balance acceptance:** the four playbooks have never been sim-tested. If the first 1,000-match run shows a playbook exceeding the win-rate ceiling or the median match falling outside 3–6 minutes, am I authorized to tune tunables (starting with `possessions_per_side`, per RS-A-2) autonomously, or does each balance change need operator sign-off?
7. **AI strength target:** v1 is a deterministic EV policy with `ai_noise` default 0.15. Is "strong enough to playtest against" the only bar, or is there a desired difficulty curve / multiple difficulty presets expected in the prototype?
8. **"At least four" playbooks (GL-FR-8):** is exactly four the intended prototype content, or should the pool/architecture anticipate more being added during playtesting?

**Things I would confirm but expect are settled (to surface assumptions, not to reopen):**
9. The five ADRs are binding and not up for revisiting in the prototype (bounded variance, simultaneous reveal, football-first borrowing, alternating possessions, ready-made playbooks) — confirm none are still in flux.
10. Precedence order under conflict is PRD > game-rules-spec > architecture/ADRs > L3 design docs > verification > ops > license, per the `baseplate_Precedence_Rank` fields — confirm I should treat that literally when two documents disagree.
11. The license is a recorded private/all-rights-reserved default and blocks nothing; the owner's legal name is intentionally absent (L-1). Confirm no public-distribution step is in Phase-1/prototype scope that would trigger the dependency-license re-audit.

**One gap I noticed worth flagging:** the docs specify the reserved forward-compat hooks for the deferred audible/reaction module ("reaction" phase, react message) but there is no explicit answer to *who* generates the initial UI art (category colorways, icon set, chalk artwork) — UX-A-1 marks it "build-time art task, bounded, open." Before Phase 3 I'd need to know whether that art is my responsibility to produce within the triple-coding/contrast constraints, or supplied.

None of these block starting Phase 1 (the engine and sim harness) today; items 1–3 must be resolved before deployment, and 4–8 are best answered before I pin dependencies and begin balance runs.
