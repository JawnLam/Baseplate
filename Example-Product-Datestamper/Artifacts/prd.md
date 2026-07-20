---
type: BASEPLATE_Requirements_Document
Item_ID: datestamper-prd
title: "Inbox-Datestamper — PRD"
baseplate_Product_Slug: "DS"
baseplate_Doc_Class: prd
baseplate_Layer: 1
baseplate_ID_Scheme: "DS-FR-<n> / DS-NFR-<n>"
baseplate_Document_Status: audited
baseplate_Precedence_Rank: 1
Date_Added: 2026-07-19
Date_Modified: 2026-07-19
Needs_Processing: false
---

# Inbox-Datestamper — Product Requirements (PRD)

## Problem & why (collapsed L0)

A Markdown vault has a single `_InBox` where new notes, clippings, audio, PDFs, and images land from many sources (mobile capture, sync, web clipper). Two chores need doing on every new arrival, forever, and doing them by hand does not scale: (1) each note needs a stable unique ID so downstream tooling can reference it; (2) each file needs a creation-time prefix so the inbox sorts chronologically and the arrival time is preserved in the name even after later timestamp rewrites. **Inbox-Datestamper** is a zero-AI-token cron bot that does exactly these two chores, once a minute, on the *top-level* inbox files — and does them **safely**, because it mutates real files the operator cares about. Success = new files reliably get a stable `Item_ID` and a frozen creation-time prefix within a minute or two of landing, with **zero** unintended renames of pre-existing or linked files and **zero** content corruption.

## Definitions (for the stranger)

- **Inbox:** the directory `<VAULT_ROOT>/_InBox`. Only its **top-level** entries are in scope.
- **Top-level file:** a regular, **non-hidden** (name not starting with `.`) file located *directly* in the inbox — not in any subfolder (e.g. `_InBox/YouTube Inbox/` is out of scope).
- **Frontmatter:** a YAML block at the very start of a file, opened by a line that is exactly `---` and closed by a later line beginning `---`. "Has frontmatter" means the file text starts with `---` immediately followed by a newline.
- **`Item_ID`:** a YAML key whose value is a **UUID in Drafts-style uppercase 8-4-4-4-12 form** (e.g. `3F2A9C41-7B0E-4D6A-9E11-2C8F5A0B7D34`), matching the vault's `Master_Schema`.
- **Birth time:** the filesystem creation timestamp of the file (epoch seconds), read via the OS; when the filesystem does not record one, the file's modification time (mtime) is used instead. See ADR-001 for why birth time was chosen and its caveat.
- **Stamp / date-stamp:** the prefix `YYMMDDHHmm - ` (ten digits, space, hyphen, space) prepended to a filename, where `YYMMDDHHmm` is the birth time rendered in the configured timezone (default `America/Los_Angeles`). Example: `Meeting notes.md` → `2607091315 - Meeting notes.md`.
- **Already-stamped:** a filename that already begins with the pattern `^\d{10} - ` (exactly ten digits, then space-hyphen-space).
- **Excluded file:** any file whose name matches `_AI-` at the start (case-insensitive), with or without an existing stamp prefix — these are AI control/instruction files and are never touched by either chore.
- **Cold:** a file whose modification time has been settled for at least the stability window (default 20 seconds); a file being written or synced right now is *not* cold and is deferred to a later run.
- **New (for stamping):** a top-level file that is **not present in the baseline** (see ADR-002) and is not already-stamped.
- **Baseline:** a stored flat list of the inbox's top-level filenames recorded on the bot's first-ever run, kept **outside the vault** so it never syncs.

## Functional requirements

| ID | Requirement (what, not how) | Verified by |
|----|------------------------------|-------------|
| DS-FR-1 | For every **cold, top-level `.md`** file in the inbox that is not excluded, Inbox-Datestamper guarantees a non-empty `Item_ID` exists in the file's YAML frontmatter. | AT-1 |
| DS-FR-2 | Item_ID is normalized per fixed rules: **missing** → insert as the first frontmatter key; **present but empty** → fill; **present but not a UUID** → replace with a fresh UUID; **valid UUID in wrong case** → uppercase it in place; **already a valid uppercase UUID** → leave untouched. New UUIDs are Drafts-style uppercase 8-4-4-4-12. | AT-2 |
| DS-FR-3 | The Item_ID pass is **non-destructive**: it edits only the `Item_ID` line (or prepends a minimal frontmatter block if none exists), preserves the rest of the file byte-for-byte, **preserves the file's mtime**, and never renames the file. | AT-3 |
| DS-FR-4 | A file with an **opening `---` fence but no closing fence** (malformed frontmatter) is left untouched by the Item_ID pass — the bot does not guess where frontmatter ends. | AT-4 |
| DS-FR-5 | For every **cold, top-level, not-excluded, not-already-stamped, "new"** file (any file type), Inbox-Datestamper renames it to `<YYMMDDHHmm> - <original name>`, where `YYMMDDHHmm` is the file's birth time in the configured timezone. | AT-5 |
| DS-FR-6 | "New" is defined by the baseline: on the **first run ever** (no baseline stored) the bot records the current top-level inbox contents as the baseline and stamps **nothing**; on every later run, any top-level file **not in the baseline** is eligible. Running with `--backfill` ignores the baseline and treats every unstamped file as eligible. | AT-6 |
| DS-FR-7 | **Idempotency:** a file whose name already matches `^\d{10} - ` is never stamped again. | AT-7 |
| DS-FR-8 | **Exclusion:** files matching `_AI-` at the start (case-insensitive, with or without an existing stamp) are excluded from **both** the Item_ID pass and the date-stamp pass. | AT-8 |
| DS-FR-9 | **Coldness gate:** only files whose mtime has settled for at least the stability window are acted on by either pass; a file still being written or synced is deferred to a later run. | AT-9 |
| DS-FR-10 | **Runaway backstop:** if a single run would stamp more than the configured maximum (`MAX_RENAMES_PER_RUN`), it aborts the date-stamp pass, renames **nothing** that run, and logs a WARN. | AT-10 |
| DS-FR-11 | **Rename-collision safety:** if the computed stamped name already exists on disk, the bot skips that file (does not overwrite) and logs the skip. | AT-11 |
| DS-FR-12 | **`--dry-run`:** the bot logs what each pass *would* do and makes **no** change — no frontmatter edit, no rename, and no baseline write. | AT-12 |
| DS-FR-13 | **Same-run completion:** within one invocation the Item_ID pass runs before the date-stamp pass; because the Item_ID edit preserves mtime, a newly-arrived `.md` file can receive both its Item_ID and its stamp in the same run (the Item_ID edit does not reset the coldness clock). | AT-13 |
| DS-FR-14 | **Quiet-by-default logging:** the bot emits a log line only when it acts (or under `--dry-run`); a run that changes nothing produces no output, so it creates no git/sync noise. Every log line carries a UTC timestamp. | AT-14 |
| DS-FR-15 | **Scope split:** the Item_ID pass applies only to top-level `.md` files; the date-stamp pass applies to top-level files of **any** type. Subfolders and hidden files are out of scope for both. | AT-15 |

## Non-functional requirements

| ID | Requirement | Verified by |
|----|-------------|-------------|
| DS-NFR-1 | **No third-party runtime dependencies:** Python 3.9+ standard library plus standard OS utilities (`stat`, `cron`, `flock`) only. No PyPI packages. (Python floor is 3.9 because `zoneinfo` — verified 2026-07-19, PEP 615 — is used.) | AT-16 |
| DS-NFR-2 | **Zero AI tokens, no network:** purely local filesystem, time, and string logic; the bot makes no model calls and no outbound requests. | AT-17 |
| DS-NFR-3 | **Atomic, corruption-safe writes:** the Item_ID edit writes to a temporary file and atomically replaces the original, so an interruption cannot leave a partially-written note. | AT-18 |
| DS-NFR-4 | **Git-recoverable:** the vault is a git repository whose separate sync job commits renames, so any rename is undoable via git history. | AT-19 |
| DS-NFR-5 | **Link-safety:** the bot renames only files nothing is expected to link to (new arrivals; the baseline protects pre-existing, possibly-linked files). A filesystem rename does not rewrite `[[wikilinks]]`/embeds — this is why "new files only" is a safety requirement, not a convenience. | AT-6, AT-20 |
| DS-NFR-6 | **No overlapping runs:** at most one instance operates on the inbox at a time (the deployment enforces this with a non-blocking lock; a run that would overlap is skipped, not queued). | AT-21 |

## Non-goals

- Does **not** touch subfolders of `_InBox` — top-level only.
- Does **not** stamp the pre-existing backlog **unless** `--backfill` is explicitly run once.
- Does **not** rewrite `[[wikilinks]]` or embeds pointing at a renamed file (a filesystem rename cannot; the baseline avoids the situation for pre-existing files).
- Does **not** add `Item_ID` to non-`.md` files (audio/PDF/images do not take YAML frontmatter), and does **not** add it to `_AI-*` files.
- Does **not** validate, add, or normalize any frontmatter field other than `Item_ID`.
- Does **not** re-serialize YAML — it is a line-based edit that preserves all other frontmatter content, comments, and formatting byte-for-byte.
- Is **not** a general inbox organizer, deduplicator, or content classifier — two fixed chores only, driven purely by filename, frontmatter, and timestamp.

## Success metrics

- Every new top-level inbox file gets a **stable** creation-time prefix within ~1–2 minutes of becoming cold; the prefix, once written, never changes.
- Every cold top-level `.md` file carries a valid uppercase `Item_ID`.
- **Zero** unintended renames of pre-existing or linked files (baseline-protected); **zero** content corruption (atomic, non-destructive edits); no-op runs produce **no** git noise.

## Assumptions & open questions

- **Assumption:** the bot runs as a user permitted to read birth time and rename inbox files, and renames preserve the files' ownership. (Owner: operator; realized in the runbook's deployment.)
- **Assumption:** the vault is a git repo with a separate job that commits changes, providing the recoverability DS-NFR-4 depends on. (Owner: operator.)
- **Open question (operator-only):** the three tunables — stability window (default 20s), `MAX_RENAMES_PER_RUN` (default 500), and stamp timezone (default `America/Los_Angeles`) — are operator-configurable; the defaults are the shipped values. Not a build blocker.
