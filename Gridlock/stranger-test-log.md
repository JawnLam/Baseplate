---
type: BASEPLATE_Stranger_Test_Log
Item_ID: gridlock-stranger-test-log
title: "Gridlock — Stranger Test Log"
baseplate_Product_Slug: "GL"
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
---

# Gridlock — Stranger Test Log (Gate 2)

**Rerun count: 1 run, 0 reruns. Result: SHIP on run 1.**

## Gate 1 record (consistency audit, run first — 2026-07-20)

Mechanical audit over the frozen `Artifacts/` (16 documents), scripted (ID extraction + cross-checks):

- ✅ Requirement↔verification bijection: GL-FR-1..14, GL-NFR-1..8, GL-RS-1..38 (no gaps) ↔ GL-AT-1..30 (no gaps); forward refs all defined; reverse map covers all 38 RS rules; zero orphans either direction.
- ✅ Cross-references resolve within `Artifacts/` (every `*.md` reference exists in-folder); PF-7 grep sweep clean (no references to cartridge-level files).
- ✅ Precedence declared and extractable (`baseplate_Precedence_Rank` in every ranked document; declaration in the selection record).
- ✅ No unresolved placeholders (runbook angle-bracket tokens are declared deploy-time parameters tied to open questions R-1/R-2).
- ✅ Dependency log complete: 14 entries, each with method-of-check and date (registry queries 2026-07-20; Caddy via Ubuntu 24.04 apt archive after caddyserver.com proved proxy-blocked; Hostinger by operator attestation).
- ✅ Anti-staleness marks present (verification dates on stack versions and decisions).
- ✅ Non-goals present in both requirements-bearing documents.

No waivers. All documents advanced to `audited`.

## Run 1 — 2026-07-20

- **Stranger:** fresh Claude Opus instance (different model family than the drafting model — substrate-agnosticism check). No conversation history; received only the `Artifacts/` folder path and the fixed prompt, verbatim, unaugmented.
- **Evaluator:** independent fresh instance; read the stack and the stranger's output, not the drafting conversation; applied the charitable classification stance from the gate protocol verbatim.
- **Verbatim outputs:** archived in full in §Archive below.

### Per-criterion results

- **ST-1 Restatement fidelity: PASS.** Every load-bearing claim traced to a document; no misreadings. (Nit: "byte-exact replayable log" compresses "byte-for-byte on outcomes" — same intent.)
- **ST-2 Plan plausibility: PASS.** Engine-first, schemas-first, replay-alongside plan; consistent with the pure-engine boundary, "schema is law," GL-RS-38 load validation, and the machine/device/ops gate split. Phase-1 exit = R1+R2 green + 1,000-match replay.
- **ST-3 Question classification: 12 questions, 0 `stack-should-answer`.**

### Question-classification table (evaluator's)

| # | Label | Classification |
|---|---|---|
| 1 | VPS specs / OS image | operator-only (stack's own R-1) |
| 2 | Domain name | operator-only (stack's own R-2) |
| 3 | Off-box backup acceptance | operator-only-confirmation (R-3 answers) |
| 4 | TS 7 vs TS 5 fallback | operator-only-confirmation (TD-A-2 answers) |
| 5 | Balance-tuning ownership | operator-only (process/authority) |
| 6 | Tuning autonomy after sims | operator-only (authorization) |
| 7 | AI strength target | operator-only-confirmation (TD §5/§7 answer) |
| 8 | "At least four" playbooks | operator-only-confirmation (GL-RS-17 answers: exactly four) |
| 9 | ADRs binding | operator-only-confirmation (PRD §7 answers) |
| 10 | Precedence literal | operator-only-confirmation (rank fields answer) |
| 11 | License/public-scope | operator-only-confirmation (L-1 answers) |
| 12 | UI art responsibility | operator-only-confirmation (UX-A-1 owner column answers: Builder) |

### Evaluator's non-blocking observation → fixed same day

PRD §5 referenced a balance band and win-rate ceiling that no data schema named. Fixed post-verdict: Appendix D of `game-rules-spec.md` now defines `balance_possession_score_band [30,60]` and `balance_playbook_winrate_ceiling 60` as acceptance thresholds (sim-report inputs, not engine inputs). No rule IDs or semantics changed; Gate-1 checks re-run clean on the edit.

### Verdict

**SHIP.** Stack advanced to `shipped`. Deployment remains gated only on the operator answers R-1 (VPS specs) and R-2 (domain) — registered in the runbook, confirmed by the stranger as non-blocking for build phase 1.

---

## Archive — Run 1 verbatim outputs

### Stranger output (verbatim)

> **(a) One-page restatement.** Gridlock is a fast, two-player, mobile-first web card game that turns American football's play-calling duel into a hidden-information mind game. It is a prototype whose entire purpose is to test one hypothesis: the down-by-down play-calling duel — concealed commitments, a dramatic simultaneous reveal ("the snap"), resolved down by down — is fun enough that people ask for a rematch. […] The design lineage is *Android: Netrunner* (asymmetric roles, hidden information, a resource economy, press-your-luck tension), re-expressed entirely in football-native terms […] mechanics are borrowed at the system level; all names, art, and vocabulary are original football language […] A match is a fixed, even set of possessions (default 2 per side, plus tie-break rounds). Players alternate offense and defense […] four ready-made playbooks (Ground & Pound, Air Raid, Blitz City, Bend Don't Break). No deck-building. Each down runs four phases: upkeep → secret commit → simultaneous snap → resolution. Resolution is bounded variance: the matchup of the two revealed cards defines an outcome band and event probabilities deterministically; then a seeded random draw picks within it. […] A Coaching Points economy funds card costs and buying "scouts" […] a 20-second shot clock with safe auto-commit keeps matches at a target 3–6 minutes. **Architecture:** four components around one load-bearing boundary: a pure, deterministic TypeScript rules engine (no I/O, no clock, PCG32 seed) imported by both the Node game server (WebSocket, matchmaking with AI backfill, server-authoritative hidden-information enforcement, SQLite + append-only match logs) and a headless sim harness. A thin Preact PWA renders only what the server sends and never adjudicates. Players are anonymous. Every match produces a replayable log. Every tunable lives in balance-data JSON. One commodity VPS behind Caddy, operable from the runbook by a stranger. **Definition of done:** four green gates — machine, device, operations, and the operator's fun verdict over ≥5 matches; 60 requirements/rules map to 30 acceptance tests with zero orphans.
>
> **(b) First-phase plan.** Phase 1 = "engine + sim harness green" — a fully playable, verifiable game before any network or UI code. Step 0: npm-workspaces monorepo, pinned stack, Zod schemas first (schema is law), day-one lint ban on client→engine imports. Step 1: transcribe rules-spec Appendices A–D into the four balance JSON files + `validateBalanceData` (GL-RS-38). Step 2: the pure engine in dependency order — PCG32 exactly as specified; core types and pure API; the full down cycle, economy, scouting, three-stage resolution, declared downs, transitions, tie-breaks, forfeit; `replay()` built alongside, not after. Step 3: Vitest suites GL-AT-23..30, the EV-policy AI, then `sim run --matches 1000`, replay all logs (zero divergences), aggregate stats. Phase 2: server/protocol; Phase 3: PWA + snap + device gates + deployment. Rationale: front-load the load-bearing, hardest-to-change artifact; make the fun hypothesis testable earliest; defer I/O and UI risk.
>
> **(c) Questions.** (1) VPS specs/OS [R-1] — blocks deployment only. (2) Domain name [R-2] — blocks deployment only. (3) Off-box backup acceptance stands? [R-3]. (4) TS 7 vs TS 5 fallback commitment at first build [TD-A-2]. (5) Who owns balance tuning during Phase 1? (6) Am I authorized to tune autonomously if first sims miss the bands (starting with possessions_per_side per RS-A-2)? (7) AI strength target — is "strong enough to playtest against" the only bar? (8) Is exactly four playbooks the intended content, or anticipate more? (9) Confirm the five ADRs are binding. (10) Confirm precedence ranks are literal. (11) Confirm no public-distribution step in prototype scope. (Flagged gap:) who produces the initial UI art — builder or supplied? None of these block starting Phase 1 today.

*(Full unabridged text: the run-1 archive file kept with the cartridge session records; the abridgments above elide only repeated document content, never questions or claims.)*

### Evaluator output (verbatim conclusion)

> **Stack-should-answer count: 0.** ST-1: PASS. ST-2: PASS. **Overall verdict: SHIP.** Items 1–2 are the stack's own registered owner-owned open questions (correctly fenced as blocking only provisioning); items 3–4 and 7–12 are confirmations of things the documents already state; items 5–6 are authority/process questions a competent builder takes to the operator regardless of documentation quality. One non-blocking observation: PRD §5's balance metric references a band and ceiling that Appendix D doesn't include as keys — two builders would still build the identical game; adding those two keys would close the loop. *(Addressed same day — see above.)*
