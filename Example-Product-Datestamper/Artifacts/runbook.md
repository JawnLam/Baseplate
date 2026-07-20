---
type: BASEPLATE_Operations_Document
Item_ID: datestamper-runbook
title: "Inbox-Datestamper — Runbook"
baseplate_Product_Slug: "DS"
baseplate_Doc_Class: runbook
baseplate_Layer: 5
baseplate_Document_Status: audited
baseplate_Precedence_Rank: 5
Date_Added: 2026-07-19
Date_Modified: 2026-07-19
Needs_Processing: false
---

# Inbox-Datestamper — Runbook

> The operations document for a bot that **runs continuously** (cron, every minute, indefinitely). Owns the deployment facts no other document owns: cadence, the wrapper, the lock, where state lives, and how to monitor/disable it. Absolute paths are shown as placeholders (`<VAULT_ROOT>`, `<STATE_DIR>`, `<BOT_DIR>`, `<WRAPPER_PATH>`) — substitute your deployment's real paths.

## Setup / installation

1. **Place the bot.** Put `datestamper.py` at `<BOT_DIR>/datestamper.py` (in this vault: a service folder under `_INFRA/_services/`).
2. **Install the cron wrapper** at `<WRAPPER_PATH>` (root-owned). The wrapper:
   - `sleep`s ~25 seconds first, to **offset from the top-of-minute git sync** (`vger-sync` runs at `:00`); this avoids racing the sync that will commit the bot's renames.
   - runs the bot under a **non-blocking lock** so a slow run is skipped, not queued: `flock -n <LOCKFILE> -c 'python3 <BOT_DIR>/datestamper.py <VAULT_ROOT>'`.
   - appends stdout to the log at `<BOT_DIR>/datestamper.log`.
3. **Add the cron entry** (root crontab): `* * * * * <WRAPPER_PATH> >/dev/null 2>&1`.
4. **First run establishes the baseline.** The very first invocation records the current inbox contents to `<STATE_DIR>/baseline.txt` and stamps nothing (ADR-002). Confirm the baseline file appeared and the backlog was untouched before trusting it.
5. **(Optional) ownership tidy.** If the bot runs as root, renames preserve each file's original owner; a periodic `chown` sweep can tidy the root-written **log** file if mixed-ownership in the service dir is undesirable.

## Credentials / permissions

- **No secrets, no tokens, no network** — the bot authenticates to nothing (DS-NFR-2). The only "credential" is **filesystem permission**: the running user must be able to read the inbox, read birth time (`stat`), and rename files in the inbox.
- Running as **root** on the server is the shipped choice so renames can touch any inbox file while preserving ownership. `<STATE_DIR>` must be writable by that same user.
- The baseline lives at `<STATE_DIR>/baseline.txt`, **outside `<VAULT_ROOT>`**, so it never syncs into the vault (a hard requirement — ADR-002).

## Cadence

- **Every minute**, via cron, indefinitely. Each run is a complete, independent pass (§ Algorithms in the technical design); there is no daemon and no in-memory carry-over between runs.
- Each run does Pass 1 (Item_ID) then Pass 2 (date-stamp). Typical steady-state run: zero actions, zero output.
- The non-blocking lock means at most one run operates at a time; a run still going when the next minute fires is skipped (DS-NFR-6).

## Failure modes & responses

| Symptom | Likely cause | Response |
|---|---|---|
| A brand-new file is not stamped within ~2 min | File not yet **cold** (still being written/synced), or it is `_AI-*` (excluded), or a subfolder file (out of scope), or the run is being skipped by the lock | Wait one cycle; confirm the file is top-level and non-excluded; check the log for a SKIP/WARN |
| Log shows `WARN abort: N candidates exceed MAX_RENAMES_PER_RUN` | A large batch appeared at once (e.g. a bulk import), tripping the runaway backstop (DS-FR-10) | Investigate the batch; if legitimate, run `--backfill` **deliberately** once (below) after confirming nothing linked will break |
| Log shows `SKIP (target exists)` | Two files would collide on the same stamped name | Expected safety behavior; rename one input manually if both must land |
| The backlog got mass-renamed | `--backfill` was run, or the baseline file was missing on a non-first run | Recover via **git history** (DS-NFR-4); restore/rebuild `<STATE_DIR>/baseline.txt` |
| Stamps look like the wrong time | Birth time unavailable (fell back to mtime) or wrong `STAMP_TZ` | Check `STAMP_TZ`; see ADR-001 on the birth-time caveat |
| A note's frontmatter looks wrong after an Item_ID edit | Should not happen (atomic, line-based, byte-preserving) — but if a file had **malformed** frontmatter it is left untouched by design | Fix the closing `---` fence manually; the next run will then add the Item_ID |

## Monitoring

- **The log is the monitor.** `<BOT_DIR>/datestamper.log` receives a UTC-timestamped line **only when the bot acts** (DS-FR-14). A quiet log is a healthy log.
- **Watch it work:** tail the log while dropping a test file into the inbox top level.
- **Health check without changing anything:** run with `--dry-run` against `<VAULT_ROOT>` — it logs what each pass *would* do and touches nothing (including the baseline).
- **Git as audit trail:** every rename appears as a commit from the vault's sync job; the history is the record of every stamp.

## Common operational tasks

- **Dry-run (no changes):** `python3 <BOT_DIR>/datestamper.py --dry-run <VAULT_ROOT>`
- **Backfill the existing backlog on purpose:** `python3 <BOT_DIR>/datestamper.py --backfill <VAULT_ROOT>` — **only** after accepting that pre-existing links to those files will not be rewritten.
- **Re-baseline (forget history; treat current inbox as pre-existing):** delete `<STATE_DIR>/baseline.txt`; the next run re-baselines and stamps nothing.
- **Change the stamp timezone:** edit `STAMP_TZ` at the top of `datestamper.py` (e.g. to `ZoneInfo("UTC")`).

## Disable / remove

- **Pause:** comment out the cron line (root crontab).
- **Remove entirely:** delete the cron line, delete `<WRAPPER_PATH>`, delete `<BOT_DIR>`, and remove `<STATE_DIR>`. Existing stamps and Item_IDs remain in the vault (they are just filenames and frontmatter); nothing needs unwinding.
