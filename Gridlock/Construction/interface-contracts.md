---
Item_ID: gridlock-interface-contracts
type: BASEPLATE_Design_Document
title: "Gridlock — Interface Contracts"
baseplate_Product_Slug: "GL"
baseplate_Doc_Class: interface-contract
baseplate_Layer: 3
baseplate_Document_Status: shipped
baseplate_Precedence_Rank: 10
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
---

# Gridlock — Interface Contracts

> **The most frozen document in the stack. The schemas here are law; code conforms.** Three contracts: (1) the client↔server protocol, (2) the balance-data file formats, (3) the match-log format. All are runtime-validated with Zod schemas generated from these definitions (`packages/protocol` — `technical-design.md` §2). Field semantics beyond shape live in `data-dictionary.md`.

## 1. Contract versioning

- `PROTOCOL_VERSION = "1.0.0"` (semver). Every WebSocket message envelope carries `v` (major only, integer). The server rejects connections whose `hello.protocol_major` ≠ its own with error `VERSION_MISMATCH`. Minor/patch changes must be backward compatible (additive optional fields only). **Any change to a message's required fields is a major bump.**
- `BALANCE_SCHEMA_VERSION = "1.0.0"` — carried in every balance-data file; same rules.
- `LOG_SCHEMA_VERSION = "1.0.0"` — carried in every match-log header; logs are immutable once written, so replays pin the version they were recorded under.
- Forward-compatibility clause (from `adr-002` consequences): the reserved phase value `"reaction"` and the reserved message type `react` exist in the enums below, unused in 1.x, so the deferred audible/reaction module can arrive as a minor version.

## 2. Transport & envelope

WebSocket, path `/ws`, JSON text frames, UTF-8. Every message both directions:

```json
{ "v": 1, "seq": 41, "type": "<message-type>", "payload": { } }
```

`seq` is per-connection, monotonic from 1, independent per direction. Unknown *optional* payload fields must be ignored (forward compatibility); unknown message `type` → error `UNKNOWN_TYPE` (server) or client-side log-and-ignore.

## 3. Client → server messages

| type | payload | legal when |
|---|---|---|
| `hello` | `{protocol_major: 1, device_id: string(uuid), resume_token?: string, handle?: string(1..16)}` | first message on every connection |
| `set_handle` | `{handle: string(1..16, charset [A-Za-z0-9 _-])}` | outside a live match |
| `queue` | `{playbook_id: string}` | not queued, not in a match |
| `cancel_queue` | `{}` | while queued |
| `commit` | `{card_instance_id: string}` | commit phase, own commitment unlocked |
| `declare` | `{choice: "normal"\|"punt"\|"fg"}` | 4th-down declaration window (GL-RS-30) |
| `scout` | `{}` | commit phase, before own lock, once per down, CP ≥ scout cost (GL-RS-23) |
| `concede` | `{}` | any time in a live match (GL-RS-37) |
| `ping` | `{}` | any time |

Server responses to illegal-but-well-formed inputs: error `ILLEGAL_INPUT` with `reason` (the engine's typed rejection — `technical-design.md` §3.4); state is never changed by an illegal input.

## 4. Server → client messages

| type | payload (abridged to required fields) | notes |
|---|---|---|
| `welcome` | `{player_id, resume_token, handle, protocol_version, in_match: bool}` | answers `hello`; `resume_token` rotates every connection |
| `queue_status` | `{position: int, backfill_deadline: iso8601}` | |
| `match_start` | `{match_id, opponent_handle, your_playbook_id, opponent_playbook_id, first_possession: "you"\|"opponent", possessions_per_side: int}` | playbook selections are public (GL-RS-17) |
| `phase` | `{phase: "upkeep"\|"declare"\|"commit"\|"reaction"(reserved), possession: int, side: "offense"\|"defense", down: int, distance: int, los: int, score: {you, opponent}, cp: {you, opponent}, hand: CardInstance[], hand_counts: {you, opponent}, shot_clock_deadline: iso8601, declaration?: "punt"\|"fg"\|"normal", redraw_count?: int}` | `hand` is always and only **your** hand (PRD GL-NFR-2); counts follow GL-RS-11 phase-boundary rule; `declaration` present when the offense declared openly (GL-RS-30) |
| `scout_result` | `{card: CardRef}` | to the buyer only (GL-RS-23) |
| `reveal` | `{your_card: CardRef, opponent_card: CardRef, clause_fired: {you: bool, opponent: bool}}` | the snap — both cards, simultaneously, to both players |
| `resolution` | `{event: "none"\|"INC"\|"SACK"\|"INT"\|"FUM"\|"BRK", yards: int, outcome: "gain"\|"first_down"\|"touchdown"\|"safety"\|"turnover"\|"turnover_on_downs"\|"punt"\|"fg_good"\|"fg_miss", new_los: int, new_down: int, new_distance: int, score: {you, opponent}}` | |
| `possession_change` | `{possession: int, you_have_ball: bool, start_los: int, reason: "score"\|"turnover"\|"punt"\|"downs"\|"safety"\|"fg_miss"\|"schedule"}` | |
| `match_end` | `{result: "win"\|"loss"\|"tie", by_forfeit: bool, score: {you, opponent}, match_id}` | |
| `error` | `{code: ErrorCode, reason?: string}` | |
| `pong` | `{}` | |

`CardRef = {card_id, name, category, tier, cost, clause?}` (pool definition). `CardInstance = CardRef + {card_instance_id}` (a specific copy in hand).

**Invariant (restating PRD GL-NFR-2 as a contract rule):** no server→client message type carries, in any field, the opponent's hand contents, deck order, discard order, unrevealed commitment, commitment status, or scout results. `reveal` is the only message that ever names an opponent card, and only post-snap.

## 5. Error codes (closed set, 1.x)

`VERSION_MISMATCH · UNKNOWN_TYPE · MALFORMED · ILLEGAL_INPUT · NOT_IN_MATCH · ALREADY_QUEUED · UNKNOWN_PLAYBOOK · HANDLE_INVALID · RESUME_EXPIRED · SERVER_FULL · INTERNAL`

Each error is terminal for the triggering message only; the connection stays open except after `VERSION_MISMATCH` (server closes).

## 6. Balance-data files (directory `data/`, JSON, UTF-8)

Four files; canonical initial content is `game-rules-spec.md` Appendices A–D. Common header on each: `{balance_schema_version: "1.0.0", set_name: string, created: iso8601-date}`.

- **`tunables.json`** — every ⚙ value from Appendix D plus `matchmaking_backfill_seconds`, `resume_window_seconds`, AI tunables (`technical-design.md` §5): flat map whose values are exactly the shapes Appendix D uses — `number` | `[int, int]` pair (bands) | small keyed table of numbers (`tier_cost_map`, `fg_table`; keys serialized as strings in JSON). Unknown keys → load rejection (typo protection).
- **`matchup.json`** — `{cells: {"<OCAT>x<DCAT>": {band: [int,int], events: {INC?: bp, SACK?: bp, INT?: bp, FUM?: bp, BRK?: bp}}}}` — exactly 30 cells (6 offensive × 5 defensive categories, GL-RS-26); probabilities in basis points (integers, 100 bp = 1%).
- **`pool.json`** — `{cards: [{card_id: string(slug), name, side: "O"|"D", category, tier: -1|0|1|2, clause?: {cond: {kind: "VS_CAT"|"PREV"|"DIST"|"ZONE", ...kind-specific fields}, mod: {kind: "BAND_SHIFT"|"EVENT_DELTA", ...}}}]}` — clause kinds exactly per GL-RS-24; cost is derived from tier via `tunables.tier_cost_map`, never stored (single source of truth).
- **`playbooks.json`** — `{playbooks: [{playbook_id, name, offense: [{card_id, count}], defense: [{card_id, count}]}]}` — sums and zero-cost minimums enforced at load (GL-RS-38).

Load procedure: all four files parse + cross-validate as a **set** (every referenced card exists; every cell present; every tunable known) or the set is rejected whole (`technical-design.md` §6).

## 7. Match-log format (one file per match, JSON Lines, append-only)

Line 1 header: `{log_schema_version: "1.0.0", match_id, seed: string(u64-decimal), balance_set: {set_name, hash: sha256-hex-of-concatenated-files}, playbooks: {a, b}, players: {a: player_id, b: player_id}, started: iso8601}`.

Then events, in engine-emission order (each `{i: int, t: "<event>", ...}`, `i` monotonic from 1):

`coin_flip {winner}` · `possession_start {n, offense, los}` · `upkeep {cp: {a,b}, draws: {a: int, b: int}}` · `redraw {player, count}` (GL-RS-10) · `declare {choice}` · `scout {player}` + `scout_shown {card_id}` · `commit {player, card_instance_id, card_id, auto: bool}` · `reveal {a_card, b_card, clauses_fired}` · `draw {purpose: "coin"|"event"|"yardage"|"shuffle"|"scout"|"punt"|"fg"|"ai_noise", raw: u32, result}` · `resolution {event, yards, outcome, los, down, distance}` · `score {player, points, kind}` · `possession_end {reason}` · `tiebreak_round {n}` · `concede {player}` · `end {result, score, forfeit: bool, ended: iso8601}`.

**Replay contract:** `replay(config, log)` re-executes commits/declares/scouts and verifies every `draw.raw` and every `resolution` against recomputation; the first mismatch is reported by event index (PRD GL-NFR-1, GL-AT-15). Commit events record concealed choices — the log is therefore **server-private**; it is never sent to clients in 1.x.

## 8. Assumptions & open questions

| # | Item | Owner | Status |
|---|---|---|---|
| IC-A-1 | `SERVER_FULL` threshold (max concurrent matches) is an operational tunable set in the runbook's service configuration, not balance data. | Runbook | Settled by design |
| IC-A-2 | A future replay-viewer or spectator feature will need a *redacted* log projection; deliberately unspecified in 1.x (PRD non-goal 8). | Future revision | Deferred |
