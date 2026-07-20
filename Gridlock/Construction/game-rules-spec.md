---
Item_ID: gridlock-game-rules-spec
type: BASEPLATE_Requirements_Document
title: "Gridlock — Game Rules Specification"
baseplate_Product_Slug: "GL"
baseplate_Doc_Class: functional-spec
baseplate_Layer: 1
baseplate_ID_Scheme: "GL-RS-<n>"
baseplate_Document_Status: shipped
baseplate_Precedence_Rank: 2
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
---

# Gridlock — Game Rules Specification

> **This document is law for gameplay semantics.** It defines every observable game behavior as a numbered rule (`GL-RS-<n>`), precisely enough that two builders implement the identical game. It implements the five decision records in this folder (`adr-001` … `adr-005`): bounded-variance resolution, simultaneous reveal, football-first vocabulary with an enumerated borrowed economy, alternating possessions, and ready-made playbooks. Scope questions defer to `prd.md`; engine internals to `technical-design.md`. **Every number marked `⚙` is a tunable that lives in balance data (PRD GL-NFR-5); the values printed here are the canonical initial values** (design estimates of 2026-07-20, expected to change through playtesting).

## 1. Definitions (in addition to the PRD glossary)

| Term | Meaning |
|---|---|
| **LOS** | Line of scrimmage — the ball's field position at the start of a down, in yards 0–100 from the offense's own goal line (offense always drives toward 100). |
| **Distance** | Yards remaining to gain for a first down (or to the goal line if nearer). |
| **CP** | Coaching Points — the spendable resource (the borrowed action economy, `adr-003`). |
| **Category** | A card's tactical family. Offensive: `IR` inside run, `OR` outside run, `SP` short pass, `DP` deep pass, `SD` screen/draw, `PA` play-action. Defensive: `RC` run commit, `BAL` balanced, `CS` coverage shell, `BZ` blitz, `CON` contain/spy. These eleven categories are closed sets. |
| **Tier** | A card's power grade, an integer in {−1, 0, +1, +2}; shifts the outcome band (§8). |
| **Clause** | A card's conditional effect, drawn from the closed clause vocabulary (§7). |
| **Band** | The inclusive integer yardage range `[min, max]` a resolution can produce (`adr-001`). |
| **Event** | A discrete resolution outcome that preempts or modifies yardage: `INC`, `SACK`, `INT`, `FUM`, `BRK` (§8.3). |
| **Regulation** | The match's scheduled possessions, before any tie-break. |

## 2. Match structure

| ID | Rule | Verified by |
|----|------|-------------|
| GL-RS-1 | A match consists of alternating possessions in strict order (A, B, A, B, …), with ⚙ `possessions_per_side = 2` scheduled possessions per player in regulation. Possession never repeats a side out of order, regardless of how drives end. | GL-AT-23 |
| GL-RS-2 | Which player takes the first possession is decided by one draw from the match random stream at match start, recorded in the match log. | GL-AT-23 |
| GL-RS-3 | Each possession is exactly one drive: it starts at the field position given by §10 and ends on exactly one of: touchdown, successful or missed field goal, punt, turnover on downs, interception, fumble, or safety. | GL-AT-23 |
| GL-RS-4 | There is no game clock. Match length is governed solely by the possession count; no rule may reference elapsed wall-clock time except the shot clock (§4). | GL-AT-23 |
| GL-RS-5 | Scoring values: touchdown = 7 points (conversion attempts do not exist; the 7 is flat), field goal = 3, safety = 2 to the defending player. No other scoring exists. | GL-AT-23 |
| GL-RS-6 | If the score is tied after regulation, tie-break rounds begin: each round gives each player one additional possession (same order as GL-RS-1, starting position per §10). If the score differs at the end of a completed round, the match ends. After ⚙ `max_tiebreak_rounds = 3` rounds still tied, the match ends as a **tie** — a legal result. | GL-AT-29 |
| GL-RS-7 | A match may also end by **forfeit** (§12); the forfeiting player loses regardless of score. Match results are exactly: win/loss, tie, or win/loss-by-forfeit. | GL-AT-29 |

## 3. The down cycle (the atomic beat — `adr-002`)

| ID | Rule | Verified by |
|----|------|-------------|
| GL-RS-8 | Every non-declared down (see GL-RS-30 for declared downs) proceeds in exactly four phases, in order: **(1) upkeep** (CP income §6, card draw §5.4, clock/state display), **(2) commit** (both players secretly and independently choose), **(3) the snap** (simultaneous reveal of both commitments), **(4) resolution** (§8). No information about one player's pending commitment reaches the other player in any form — including whether they have already committed — until both are locked. | GL-AT-24 |
| GL-RS-9 | During commit, the offense selects exactly one offensive play card from hand and pays its CP cost; the defense selects exactly one defensive scheme card from hand and pays its CP cost. A card whose cost exceeds the player's current CP is not selectable. | GL-AT-24 |
| GL-RS-10 | **No-legal-card redraw.** If, at the start of a commit phase, a player's active hand contains no card they can afford, the engine automatically discards that hand and redraws `hand_size` cards (reshuffling per GL-RS-20 as needed), repeating until the hand contains an affordable card. Termination is guaranteed: every deck contains ≥ 4 zero-cost cards (GL-RS-38). Each redraw is recorded in the match log and announced to both players (a public "shuffle" beat — it reveals no card identities). | GL-AT-24 |
| GL-RS-11 | The offense's down-and-distance state (down number, distance, LOS) and both players' CP totals and hand *counts* are public. Publicly displayed hand counts and CP totals update only at phase boundaries (start of commit, and at the snap) — never mid-commit — so that neither commitment status (GL-RS-8) nor scouting/spending activity is derivable from them during the commit phase. Hand *contents* and committed-but-unrevealed cards are private (PRD GL-FR-6). | GL-AT-24 |

## 4. Shot clock and auto-commit

| ID | Rule | Verified by |
|----|------|-------------|
| GL-RS-12 | Each commit phase runs a per-player shot clock of ⚙ `shot_clock_seconds = 20`. If a player has not committed when their clock expires, the engine auto-commits for them: the legal card with the lowest CP cost; ties broken by the earliest position in the player's current hand order (hand order is defined by draw sequence — deterministic). | GL-AT-24 |
| GL-RS-13 | For declared downs (GL-RS-30), clock expiry auto-selects the "normal play" declaration, then GL-RS-12 applies to the card choice. | GL-AT-27 |
| GL-RS-14 | Auto-commits are flagged in the match log. ⚙ `forfeit_after_consecutive_autocommits = 3` consecutive auto-commits by the same human player ends the match as a forfeit loss for that player (§12). AI players never time out. | GL-AT-29 |

## 5. Cards, decks, and hands

### 5.1 Card anatomy

| ID | Rule | Verified by |
|----|------|-------------|
| GL-RS-15 | Every card has exactly: a football-native display name, a side (offense/defense), a category (§1), a tier in {−1, 0, +1, +2}, a CP cost in {0, 1, 2, 3}, and zero or one clause (§7). Costs are fixed by tier: tier −1 → 0 CP, tier 0 → 1 CP, tier +1 → 2 CP, tier +2 → 3 CP ⚙. | GL-AT-30 |

### 5.2 The play pool

| ID | Rule | Verified by |
|----|------|-------------|
| GL-RS-16 | All cards in the game come from the shared play pool defined in Appendix B (⚙ — the pool is balance data). Playbooks select from this pool only. | GL-AT-30 |

### 5.3 Playbooks (`adr-004`, `adr-005`)

| ID | Rule | Verified by |
|----|------|-------------|
| GL-RS-17 | A playbook is a two-sided bundle: an offensive deck of exactly ⚙ 20 cards and a defensive deck of exactly ⚙ 16 cards, chosen from the pool with duplicates allowed. The prototype ships exactly the four playbooks of Appendix C; players select one (both players may select the same one) before the match, and the selection is public. | GL-AT-30 |

### 5.4 Hands, drawing, recycling

| ID | Rule | Verified by |
|----|------|-------------|
| GL-RS-18 | At the start of each of their possessions **and** each opponent possession, a player's offensive or defensive hand (respectively, per their current side) is drawn up to ⚙ `hand_size = 4` from the corresponding deck. Hands persist across downs within a possession; the off-side hand is set aside unchanged and resumes when that side next applies. | GL-AT-24 |
| GL-RS-19 | A committed card is discarded (to its deck's discard pile) after resolution. At the end of each down, the player draws from their active deck until the active hand holds `hand_size` cards. | GL-AT-24 |
| GL-RS-20 | When a deck is empty and a draw is required, the discard pile is reshuffled into the deck using the match random stream (one recorded shuffle), and drawing continues. Cards never move between the offensive and defensive decks. | GL-AT-24 |

## 6. Coaching Points (the borrowed economy — `adr-003`)

| ID | Rule | Verified by |
|----|------|-------------|
| GL-RS-21 | Each player has a single CP pool spanning both sides of the ball. At the start of each possession, each player's CP is set to at least ⚙ `cp_possession_floor = 3` (raised to the floor if below; never reduced). During upkeep of every down, each player gains ⚙ `cp_income_per_down = 1`, capped at ⚙ `cp_cap = 6`. | GL-AT-25 |
| GL-RS-22 | CP is spent only on: committing a card (its cost, GL-RS-9) and scouting (GL-RS-23). Unspent CP persists across downs and possessions (subject to the possession floor raise). | GL-AT-25 |

### 6.1 Scouting (information as a purchasable resource)

| ID | Rule | Verified by |
|----|------|-------------|
| GL-RS-23 | During the commit phase, before locking their own commitment, a player may buy at most one **scout** per down for ⚙ `scout_cost = 2` CP: the server reveals to them one uniformly-drawn card (a recorded draw) from the opponent's *current active hand, excluding the opponent's committed card if already committed*. The scouted card's identity is shown only to the buyer and logged. Scouting never reveals the committed card. | GL-AT-25 |

## 7. Clause vocabulary (closed set)

| ID | Rule | Verified by |
|----|------|-------------|
| GL-RS-24 | A clause is a condition→modifier pair. Exactly four condition types exist: **VS-CAT(c)** — the opposing revealed card's category is `c`; **PREV(cat, n)** — the offense's previous resolved down this drive was category `cat` and gained ≥ `n` yards; **DIST(≥ n)** — current distance ≥ `n`; **ZONE(red\|deep)** — LOS ≥ 80 (`red`) or LOS ≤ 10 (`deep`). Exactly two modifier types exist: **band-shift(±n)** — add `n` to both band edges after tier shifts; **event-delta(e, ±p)** — add `p` percentage points to event `e`'s probability in the active cell (floored at 0). A clause evaluates after the snap, during resolution stage 2 (§8.2); an unmet condition does nothing. No other clause semantics exist. | GL-AT-26 |
| GL-RS-25 | Defensive clauses are the **trap idiom** (`adr-003`): a defensive card's VS-CAT clause firing is announced in the reveal presentation (the trap "springs"). Clause effects are otherwise identical in kind for both sides. | GL-AT-26 |

## 8. Resolution semantics (frozen — `adr-001`)

### 8.1 Stage 1 — matchup lookup

| ID | Rule | Verified by |
|----|------|-------------|
| GL-RS-26 | Resolution begins from the **matchup matrix** (Appendix A ⚙): the cell at (offensive category × defensive category) supplies the base band `[min, max]` and the base event probability set. | GL-AT-26 |

### 8.2 Stage 2 — deterministic modifiers, in fixed order

| ID | Rule | Verified by |
|----|------|-------------|
| GL-RS-27 | Modifiers apply in exactly this order: (1) offensive card tier shifts both band edges by tier; (2) defensive card tier shifts both band edges by −tier; (3) offensive clause, if its condition is met; (4) defensive clause, if met. After all shifts, if `min > max`, set `min = max`. Event probabilities are then clamped to [0, 95%] each, and if their sum exceeds 95%, all are scaled proportionally so the sum is 95%. | GL-AT-26 |

### 8.3 Stage 3 — draws, in fixed order

| ID | Rule | Verified by |
|----|------|-------------|
| GL-RS-28 | Exactly two draws may occur, in order, from the match random stream: **(1) the event draw** — one uniform draw in [0,1) tested against cumulative event probabilities in the fixed order `INC, SACK, INT, FUM, BRK`; **(2) the yardage draw** — one uniform integer draw over the applicable band. The applicable band and whether the yardage draw runs are set by the event outcome: no event → the modified cell band; `BRK` → ⚙ `breakaway_band = [15, 40]`; `SACK` → ⚙ `sack_band = [−8, −4]`; `INC` → no yardage draw, 0 yards, down consumed; `INT`/`FUM` → no yardage draw, possession ends, ball spot per GL-RS-34. Every draw is recorded in the match log in sequence (PRD GL-NFR-1). | GL-AT-26 |
| GL-RS-29 | Yardage application: new LOS = old LOS + drawn yards, then clamped — if ≥ 100 the result is a touchdown (excess ignored); if ≤ 0 the result is a safety (§11). Gain ≥ distance → new first down (down = 1, distance = min(10, 100 − LOS)). Otherwise down increments; a 4th-down resolution that does not reach the line to gain is a turnover on downs at the final LOS. There is no 5th down. | GL-AT-26 |

## 9. Declared downs: punts and field goals (press-your-luck — `adr-003`)

| ID | Rule | Verified by |
|----|------|-------------|
| GL-RS-30 | On 4th down only, the offense's commit phase begins with an **open declaration**, visible to the defense immediately: `normal play`, `punt`, or `field goal` (the last selectable only if in range, GL-RS-32). Choosing `normal play` proceeds as a standard down (§3). Punts and field goals are **open downs**: neither player commits a card, no CP is spent or earned beyond normal upkeep, and no defensive scheme applies. Fake punts and fake field goals do not exist. | GL-AT-27 |
| GL-RS-31 | **Punt:** net distance is one uniform integer draw from ⚙ `punt_band = [30, 45]`. If `LOS + draw ≥ 100`, the punt is a touchback and the opponent takes over at ⚙ `touchback_spot = 25` (position 25 for them as the new offense). Otherwise the opponent's starting position is exactly `100 − (LOS + draw)` — which may be deep in their own territory; no minimum spot exists other than the touchback rule. The possession ends. | GL-AT-27 |
| GL-RS-32 | **Field goal:** attempt distance = `(100 − LOS) + 17` yards. Attempts are allowed only when attempt distance ≤ ⚙ `fg_max_distance = 60`. Success probability comes from the ⚙ distance table: ≤ 30 → 95%, 31–40 → 85%, 41–50 → 70%, 51–55 → 45%, 56–60 → 20%. One uniform draw decides. Success → 3 points; the possession ends; opponent starts per §10. Miss → possession ends; opponent takes over at `max(100 − LOS, touchback_spot)`. | GL-AT-27 |

## 10. Possession start positions

| ID | Rule | Verified by |
|----|------|-------------|
| GL-RS-33 | A possession following a touchdown, field goal (made), safety, or at the start of regulation and each tie-break round, begins at the new offense's own ⚙ `drive_start = 25` (position 25). A possession following a punt, missed field goal, turnover on downs, interception, or fumble begins at the position those rules define (GL-RS-31, GL-RS-32, GL-RS-29, GL-RS-34) — field position carries. | GL-AT-28 |
| GL-RS-34 | **Turnover spot (interception or fumble):** the new offense starts at `100 − LOS` (the mirror of the line of scrimmage at the time of the turnover). Return yardage does not exist. If `100 − LOS < 1`, the spot is clamped to 1; if the turnover occurs with LOS ≥ 90, the new offense starts at `touchback_spot` (end-zone interceptions are touchbacks). | GL-AT-28 |

## 11. Safeties

| ID | Rule | Verified by |
|----|------|-------------|
| GL-RS-35 | If a resolution's yardage application takes LOS to ≤ 0 (GL-RS-29), the defending player scores 2 points, the possession ends, and the next scheduled possession begins at `drive_start` (there is no free-kick mechanic). | GL-AT-28 |

## 12. Timeouts, disconnects, forfeits

| ID | Rule | Verified by |
|----|------|-------------|
| GL-RS-36 | A disconnected human player's commits are handled by the auto-commit rule (GL-RS-12) exactly as a shot-clock expiry — disconnection is not a distinct game state. The forfeit threshold (GL-RS-14) therefore ends abandoned matches within 3 downs. A forfeited match records a final score and the forfeit flag. | GL-AT-29 |
| GL-RS-37 | A player may concede at any time; concession is a forfeit loss for the conceding player, effective immediately. | GL-AT-29 |

## 13. Deck-construction invariants (enforced on balance data at load)

| ID | Rule | Verified by |
|----|------|-------------|
| GL-RS-38 | Every playbook's offensive deck contains ≥ ⚙ 4 zero-cost offensive cards; every defensive deck contains ≥ ⚙ 4 zero-cost defensive cards. Every card in a playbook exists in the pool with matching attributes. The engine refuses to load balance data violating these invariants. | GL-AT-30 |

## Non-goals (rules-level; both edges of scope)

No game clock, quarters, halves, or clock management. No kickoffs or return plays (all post-score starts are touchbacks). No conversion attempts after touchdowns. No penalties, injuries, weather, fatigue, or substitutions. No fake punts/field goals. No kneel-downs or spikes. No onside kicks. No in-match communication between players. No pausing. Trick plays are expressed within the six offensive categories via tiers and clauses, not as a separate mechanic. (Product-level non-goals: `prd.md` §4.)

## Assumptions & open questions

| # | Item | Owner | Status |
|---|---|---|---|
| RS-A-1 | All ⚙ values are 2026-07-20 design estimates; the pacing target (PRD GL-NFR-6) and balance metrics (PRD §5) are the acceptance yardstick, and playtesting + sim runs are expected to change these numbers without changing any rule ID. | Playtest loop | Standing |
| RS-A-2 | `possessions_per_side = 2` is the sharpest pacing lever; if sims show median match length outside 3–6 minutes, tune this first. | Sim harness runs | Open |
| RS-A-3 | The post-snap "audible/reaction" module (PRD A-5) would insert a phase between snap and resolution; the four-phase structure of GL-RS-8 was written so this insertion changes no existing rule ID. | Operator, post-playtest | Deferred |

---

## Appendix A — Matchup matrix (canonical initial values ⚙)

Cell format: `band [min,max]` + event probabilities in percentage points. Omitted events are 0%. (Events: INC incomplete, SACK, INT interception, FUM fumble, BRK breakaway. Order of evaluation is fixed by GL-RS-28.)

| O ↓ / D → | RC run commit | BAL balanced | CS coverage shell | BZ blitz | CON contain/spy |
|---|---|---|---|---|---|
| **IR** inside run | [−2,2] | [1,5] | [3,8] | [−1,6] FUM 5 | [0,4] |
| **OR** outside run | [−3,4] | [0,6] | [2,9] | [−2,8] FUM 5, BRK 5 | [−1,3] |
| **SP** short pass | [4,9] INC 15 | [2,6] INC 25 | [1,5] INC 30 | [0,7] INC 20, SACK 15 | [2,6] INC 25 |
| **DP** deep pass | [10,25] INC 25, BRK 15 | [0,18] INC 40, INT 8, SACK 8 | [12,30] INC 55, INT 15, SACK 10 | [15,35] INC 30, SACK 25, INT 10 | [12,25] INC 45, INT 8, SACK 10 |
| **SD** screen/draw | [3,10] | [1,7] | [2,7] | [5,15] BRK 10 | [−2,3] |
| **PA** play-action | [10,25] INC 15 | [8,20] INC 30, INT 5, SACK 10 | [10,22] INC 45, INT 10, SACK 12 | [12,30] INC 25, SACK 30, INT 8 | [8,18] INC 40, SACK 8 |

Global bands ⚙: `sack_band [−8,−4]`, `breakaway_band [15,40]`.

*Design intent, for tuners:* run commits crush runs but are lit up by play-action and deep shots; shells squeeze passing but concede the ground game; blitzes are boom-or-bust against everything and uniquely vulnerable to screens; contain/spy is the anti-deception answer that concedes honest short passing.

## Appendix B — Play pool (canonical initial values ⚙)

Offensive pool (name · category · tier). Cost follows tier (GL-RS-15). Clauses noted.

| Card | Cat | Tier | Clause |
|---|---|---|---|
| QB Sneak | IR | −1 | — |
| Inside Zone | IR | 0 | — |
| Power O | IR | +1 | PREV(IR, 3) → band-shift +2 |
| Counter Trey | IR | +1 | VS-CAT(BZ) → band-shift +3 |
| Outside Zone | OR | 0 | — |
| Stretch Run | OR | 0 | — |
| Jet Sweep | OR | +1 | VS-CAT(RC) → band-shift +2 |
| Toss Crack | OR | +1 | ZONE(deep) → band-shift +2 |
| Quick Slant | SP | −1 | — |
| Curl-Flat | SP | 0 | — |
| Mesh Crossers | SP | +1 | VS-CAT(BZ) → band-shift +2 |
| Stick Concept | SP | 0 | DIST(≥7) → band-shift +1 |
| Four Verticals | DP | +1 | VS-CAT(RC) → band-shift +3 |
| Post-Corner Shot | DP | +1 | ZONE(red) → band-shift −2 |
| Deep Dig | DP | 0 | — |
| Hail Mary | DP | +2 | DIST(≥15) → band-shift +4 |
| RB Screen | SD | 0 | — |
| WR Bubble Screen | SD | −1 | — |
| Draw Play | SD | 0 | DIST(≥8) → band-shift +2 |
| Flea Flicker | SD | +2 | VS-CAT(RC) → band-shift +6 |
| PA Boot Leg | PA | 0 | PREV(IR, 3) → band-shift +2 |
| PA Deep Cross | PA | +1 | PREV(IR, 3) → band-shift +3 |
| PA Deep Shot | PA | +2 | PREV(IR, 4) → band-shift +4 |
| Waggle Flood | PA | 0 | PREV(OR, 3) → band-shift +2 |

Defensive pool:

| Card | Cat | Tier | Clause |
|---|---|---|---|
| Base 4-3 | BAL | −1 | — |
| Base Nickel | BAL | −1 | — |
| Cover 1 Robber | BAL | 0 | VS-CAT(SP) → event-delta(INT, +5) |
| Cover 3 Match | BAL | +1 | VS-CAT(DP) → event-delta(INT, +5) |
| 46 Bear Front | RC | +1 | VS-CAT(IR) → band-shift −2 |
| Goal Line Stack | RC | 0 | ZONE(red) → band-shift −2 |
| Run Blitz | RC | 0 | VS-CAT(OR) → band-shift −2 |
| Cover 2 Shell | CS | 0 | VS-CAT(DP) → event-delta(INT, +5) |
| Tampa 2 | CS | +1 | VS-CAT(SP) → band-shift −2 |
| Quarters Match | CS | 0 | — |
| Prevent Umbrella | CS | −1 | VS-CAT(DP) → band-shift −4 |
| Cover 0 All-Out | BZ | +2 | VS-CAT(DP) → event-delta(SACK, +10) |
| Fire Zone Blitz | BZ | +1 | VS-CAT(SP) → event-delta(SACK, +5) |
| Double A-Gap | BZ | 0 | VS-CAT(IR) → band-shift −3 |
| QB Spy | CON | 0 | VS-CAT(SD) → band-shift −2 |
| Edge Contain | CON | 0 | VS-CAT(OR) → band-shift −2 |
| Disguised Shell | CON | +1 | VS-CAT(PA) → event-delta(INT, +5) |
| Zone Dog | CON | −1 | — |

## Appendix C — The four playbooks (canonical initial values ⚙)

Composition as card × count; offensive decks sum to 20, defensive to 16; zero-cost minimums per GL-RS-38 (tier −1 = cost 0).

**Ground & Pound** — O: QB Sneak×4, Inside Zone×3, Power O×3, Counter Trey×2, Outside Zone×3, Stretch Run×2, PA Boot Leg×2, PA Deep Shot×1 · D: Base 4-3×4, 46 Bear Front×3, Goal Line Stack×2, Run Blitz×3, Cover 2 Shell×2, Edge Contain×2.

**Air Raid** — O: Quick Slant×4, Curl-Flat×3, Mesh Crossers×3, Stick Concept×2, Four Verticals×3, Deep Dig×2, Post-Corner Shot×2, Hail Mary×1 · D: Base Nickel×4, Cover 3 Match×2, Cover 2 Shell×3, Quarters Match×3, Prevent Umbrella×2, Fire Zone Blitz×2.

**Blitz City** — O: Quick Slant×3, WR Bubble Screen×3, Inside Zone×3, RB Screen×3, Mesh Crossers×2, Draw Play×2, PA Deep Cross×2, Jet Sweep×2 · D: Zone Dog×4, Double A-Gap×3, Fire Zone Blitz×3, Cover 0 All-Out×2, Run Blitz×2, Base 4-3×2.

**Bend Don't Break** — O: Quick Slant×4, Inside Zone×3, Stick Concept×2, RB Screen×2, Outside Zone×2, Deep Dig×2, Waggle Flood×3, Flea Flicker×2 · D: Prevent Umbrella×3, Cover 2 Shell×3, Tampa 2×2, Quarters Match×3, Base Nickel×3, Disguised Shell×2.

## Appendix D — Global tunables summary ⚙

`possessions_per_side 2 · max_tiebreak_rounds 3 · shot_clock_seconds 20 · forfeit_after_consecutive_autocommits 3 · hand_size 4 · cp_possession_floor 3 · cp_income_per_down 1 · cp_cap 6 · scout_cost 2 · tier_cost_map {−1:0, 0:1, +1:2, +2:3} · sack_band [−8,−4] · breakaway_band [15,40] · punt_band [30,45] · touchback_spot 25 · drive_start 25 · fg_max_distance 60 · fg_table {30:95, 40:85, 50:70, 55:45, 60:20} · offensive_deck_size 20 · defensive_deck_size 16 · min_zero_cost_cards 4 · balance_possession_score_band [30,60] (acceptance band: % of possessions ending in any score, across large sim samples) · balance_playbook_winrate_ceiling 60 (acceptance ceiling: max % win rate of any single playbook across all pairings)`

*(The two `balance_*` entries are acceptance thresholds, not gameplay inputs: the engine never reads them; the sim harness's aggregate report and the balance success metric in `prd.md` §5 do.)*
