---
Item_ID: gridlock-data-dictionary
type: BASEPLATE_Design_Document
title: "Gridlock — Data Dictionary & ERD"
baseplate_Product_Slug: "GL"
baseplate_Doc_Class: data-dictionary
baseplate_Layer: 3
baseplate_Document_Status: shipped
baseplate_Precedence_Rank: 11
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
---

# Gridlock — Data Dictionary & ERD

> **Every persistent field, its type, constraints, and provenance.** Persistence is one SQLite database plus one append-only match-log file per match (`architecture.md` §3). Wire shapes are owned by `interface-contracts.md`; this document owns what is *stored*. Governing constraint: **no PII anywhere** (PRD GL-NFR-3) — every identifier is opaque, every field below is checked against that rule.

## 1. Entities & relationships (ERD)

```
players 1 ──── * matches (as player_a or player_b)
matches 1 ──── 1 match-log file (by match_id path convention)
matches * ──── 1 balance_sets (by set hash active at match start)
players * ──── 1 playbooks (per match, by playbook_id in balance data — not a DB table)
```

Playbooks, cards, matchup cells, and tunables are **balance data** (files, `interface-contracts.md` §6), not database rows — they version as a set and are referenced by hash.

## 2. SQLite schema (database file: `gridlock.db`)

### 2.1 `players`

| Column | Type | Constraints | Semantics / provenance |
|---|---|---|---|
| `player_id` | TEXT | PK, UUIDv4 | Server-issued at first `hello`; the only key any other record uses. **The future-accounts hook: real identity, if it ever arrives, attaches to this ID by mapping — game data never migrates** (PRD §4 non-goal 1). |
| `device_id` | TEXT | UNIQUE, NOT NULL, UUIDv4 | Client-generated at first launch, stored client-side; opaque, no device fingerprint. |
| `handle` | TEXT | NOT NULL, 1–16 chars `[A-Za-z0-9 _-]` | Guest display name; auto-generated football-flavored default (e.g., "Coach Falcon 12") until `set_handle`. Not unique — it is decoration, not identity. |
| `resume_token_hash` | TEXT | NOT NULL | SHA-256 of the current resume token; token itself never stored. |
| `created_at`, `last_seen_at` | TEXT | ISO-8601 UTC | Operational only. |

*PII check: no name, email, phone, IP, or location column exists. Server access logs (Caddy) rotate per runbook and are not application data.*

### 2.2 `matches`

| Column | Type | Constraints | Semantics |
|---|---|---|---|
| `match_id` | TEXT | PK, UUIDv4 | |
| `player_a`, `player_b` | TEXT | FK → players; `player_b` NULL when AI | AI side stored as NULL + `ai_side` flag, so AI never needs a synthetic player row. |
| `ai_side` | TEXT | NULL \| 'a' \| 'b' | |
| `playbook_a`, `playbook_b` | TEXT | NOT NULL | `playbook_id` from the balance set. |
| `balance_hash` | TEXT | NOT NULL | SHA-256 of the balance set (matches log header). |
| `seed` | TEXT | NOT NULL | u64 decimal string (`interface-contracts.md` §7). |
| `started_at`, `ended_at` | TEXT | ISO-8601 UTC; `ended_at` NULL while live | |
| `result` | TEXT | 'a_win' \| 'b_win' \| 'tie' \| 'void' \| NULL | `void` = crash policy (`technical-design.md` §6). |
| `by_forfeit` | INTEGER | 0/1 | |
| `score_a`, `score_b` | INTEGER | ≥ 0 | |
| `log_path` | TEXT | NOT NULL | Relative path per §3. |

### 2.3 `balance_sets`

| Column | Type | Constraints | Semantics |
|---|---|---|---|
| `hash` | TEXT | PK | SHA-256 over the four files, concatenated in fixed order (tunables, matchup, pool, playbooks). |
| `set_name` | TEXT | NOT NULL | From the files' common header. |
| `loaded_at` | TEXT | ISO-8601 | First time this set became active. |

## 3. Match-log files

- **Location:** `logs/<yyyy>/<mm>/<match_id>.jsonl` under the server's data directory (absolute paths: `runbook.md` §2).
- **Format:** owned by `interface-contracts.md` §7. Append-only; never edited; fsync on possession boundaries and match end.
- **Privacy class: server-private** (contains concealed commitments and scout results); never served to clients in 1.x.
- **Retention ⚙:** keep everything during the prototype (the logs ARE the balance-analysis dataset — PRD §1); revisit at public ship.

## 4. Client-side storage (browser)

| Key | Store | Content | PII check |
|---|---|---|---|
| `gl.device_id` | localStorage | UUIDv4, generated once | Opaque |
| `gl.resume_token` | localStorage | Current token | Opaque, rotates each connection |
| `gl.handle` | localStorage | Display handle cache | Player-chosen decoration |
| `gl.settings` | localStorage | Reduced-motion / haptics toggles (`ux-spec.md`) | Preferences only |

No cookies, no third-party storage, no analytics identifiers (no analytics exist — PRD §4 non-goal list; external integrations are zero per `architecture.md` §3).

## 5. Field-level invariants (enforced in code, audited by tests)

1. Every stored identifier is a UUID or hash — grep-auditable (GL-AT-17).
2. `matches.result` transitions only NULL → terminal value, exactly once.
3. A `matches` row exists before its log file's header line is written (write order), and every non-void `matches` row has a log ending in an `end` event (GL-AT-12).
4. LOS, down, distance, CP, and score values inside logs always satisfy the ranges implied by `game-rules-spec.md` (LOS 1–99 between downs; down 1–4; CP 0–`cp_cap`; distance 1–99).

## 6. Assumptions & open questions

| # | Item | Owner | Status |
|---|---|---|---|
| DD-A-1 | Prototype disk budget assumes match logs ≈ 20–60 KB each; thousands of matches fit in tens of MB. If VPS disk proves small (PRD A-1), add log compression to the runbook rather than changing retention. | Runbook / operator VPS answer | Open until A-1 resolves |
