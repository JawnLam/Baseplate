---
type: BASEPLATE_Design_Document
Item_ID: datestamper-technical-design
title: "Inbox-Datestamper — Technical Design & CLI Contract"
baseplate_Product_Slug: "DS"
baseplate_Doc_Class: technical-design
baseplate_Layer: 3
baseplate_Document_Status: audited
baseplate_Precedence_Rank: 2
Date_Added: 2026-07-19
Date_Modified: 2026-07-19
Needs_Processing: false
---

# Inbox-Datestamper — Technical Design & CLI Contract

> Merged L3: the build-precision document (algorithms + **frozen behavioral semantics**) and the interface contract (CLI surface, exit behavior). Written for a cold builder/maintainer who has only this stack. Where this document freezes a behavior, that behavior is **law** (precedence rank 2, below the PRD's scope): a builder resolves the ambiguous case exactly as stated here.

## 1 · Shape

A single Python process, invoked non-interactively (by cron in production; see `runbook.md`). One run does two ordered passes over the inbox's top-level entries and exits. It holds no long-lived process state; its only cross-run memory is the **baseline file** (§2). No threads for the work itself; no network.

## 2 · Data models

**In-memory (per run):**
- `top_level_files(inbox)` → the sorted list of regular, non-hidden filenames directly in the inbox.
- `candidates` → the list of `(name, path)` pairs eligible for stamping after all filters.
- `baseline` → a `set[str]` of filenames, or `None` if no baseline file exists yet.

**Persistent (cross-run):** the **baseline file**, a UTF-8 text file **stored outside the vault** (so it never syncs), one top-level filename per line, sorted, trailing newline. It is written **once**, on the first run ever (or never, under `--dry-run` on that first run). It is not updated on later runs — "new" is defined as "not in this original baseline" (see ADR-002 for why it is not a rolling set).

There is **no other persistent data and no database** — hence no data dictionary in this stack (selection record).

## 3 · Frozen behavioral semantics (the equivalence classes)

*Every behavior below is frozen so two builders resolve it identically (generation standard 10 / PF-6). This is the section the stranger test probes hardest.*

### 3.1 What is "top-level"
Entries returned by listing the inbox directory itself, filtered to: **regular files only** (not directories, not symlink-to-dir), **name does not start with `.`** (hidden files skipped), sorted by name (ascending, byte order). Subdirectories and anything inside them are never considered by either pass.

### 3.2 What is "already-stamped"
The filename matches the anchored regex `^\d{10} - ` — **exactly ten ASCII digits**, then a space, a hyphen, a space. Eleven digits, nine digits, or a different separator do **not** match (and such a file would be treated as unstamped and get a prefix). The stamp the bot writes always produces this exact form, so its own output is idempotent.

### 3.3 What is "excluded"
The filename matches `^(?:\d{10} - )?_AI-` **case-insensitively** — i.e. the name begins with `_AI-`, optionally after an existing stamp prefix. Excluded files are skipped by **both** passes. (Rationale: `_AI-*` files are AI control/instruction files whose exact names other systems depend on.)

### 3.4 Item_ID pass scope
Applies to a top-level file iff: not excluded (§3.3) **and** its name ends with `.md` **case-insensitively** **and** it is cold (§3.6). Note the deliberate asymmetry: **the Item_ID pass is `.md`-only; the `.markdown` extension is *not* in scope for Item_ID** (only `.md`). The date-stamp pass, by contrast, covers all extensions.

### 3.5 Item_ID normalization (the state machine)
Given a `.md` file in scope:
1. If the file has **no frontmatter** (does not start with `---` + newline) → prepend exactly `---\nItem_ID: <uuid>\n---\n\n` before the existing body. Action: *added frontmatter + Item_ID*.
2. If it **has frontmatter** but the block has **no closing `---` fence** → **leave untouched** (malformed; do not guess). Action: none.
3. If frontmatter exists and closes:
   - **No `Item_ID:` key** → insert `Item_ID: <uuid>` as the **first** line of the frontmatter body. Action: *added Item_ID*.
   - **`Item_ID:` present, value is a valid uppercase 8-4-4-4-12 UUID** → **leave untouched**. Action: none.
   - **value is a valid UUID in any other case** → rewrite it uppercased in place. Action: *uppercased Item_ID*.
   - **value is non-empty but not a UUID** (a slug, garbage) → replace with a fresh UUID. Action: *replaced non-UUID Item_ID*.
   - **value is empty** → fill with a fresh UUID. Action: *filled empty Item_ID*.
   - Only the **first** `Item_ID:` line is edited (count=1); the surrounding block and body are preserved byte-for-byte (no YAML re-serialization).
- **UUID form:** `uuid4()` uppercased → `^[0-9A-F]{8}-[0-9A-F]{4}-[0-9A-F]{4}-[0-9A-F]{4}-[0-9A-F]{12}$`.
- **Value parsing:** the value is read from `^[ \t]*Item_ID:[ \t]*(.*?)[ \t]*$`, then stripped of surrounding whitespace and one layer of matching single or double quotes before the UUID tests.

### 3.6 "Cold" (stability gate)
A file is cold iff `now - mtime >= STABILITY_SECONDS` (default 20). Computed from the current mtime at check time. A file that cannot be `stat`-ed is treated as **not cold** (skipped this run). This gate applies to both passes and defers files that are mid-write or mid-sync.

### 3.7 Birth-time resolution (the stamp value)
`birth_epoch(path)`: run `stat -c %W <path>`; parse its stdout as an integer; **if > 0, use it**; otherwise (0, error, or unparseable) **fall back to `os.stat(path).st_mtime`**. The resulting epoch is rendered with `datetime.fromtimestamp(epoch, STAMP_TZ).strftime("%y%m%d%H%M")` where `STAMP_TZ` defaults to `America/Los_Angeles`. The `%y%m%d%H%M` format is always ten digits (two-digit year, zero-padded fields). See ADR-001 for the birth-vs-mtime decision and its caveat.

### 3.8 Candidate selection for stamping
A top-level file is a stamping candidate iff: **not excluded** (§3.3) **and not already-stamped** (§3.2) **and** (`--backfill` is set **or** the name is **not in the baseline**) **and** cold (§3.6). On the first run ever with no baseline and no `--backfill`, the baseline is recorded and the pass returns with zero candidates.

### 3.9 Ordering within a run
The **Item_ID pass runs first**, then the baseline is loaded, then the date-stamp pass. Because the Item_ID edit **preserves mtime** (§3.10), a file that receives its Item_ID this run is still cold for the stamp pass this run — both chores can complete in one invocation (DS-FR-13).

### 3.10 Atomic write + mtime preservation
An Item_ID edit is applied by: capturing the file's `(atime, mtime)`; writing the full new content to a temp file (`<path>.idtmp`); `os.replace(tmp, path)` (atomic on the same filesystem); then restoring the captured `(atime, mtime)` with `os.utime`. Preserving mtime is required so the edit does not reset the coldness clock (§3.9). The no-frontmatter prepend uses the same atomic write.

### 3.11 Runaway backstop
After building `candidates`, if `len(candidates) > MAX_RENAMES_PER_RUN` (default 500) the date-stamp pass logs a WARN and renames **nothing** that run (all-or-nothing abort; it does not stamp the first 500). Guards against a misfire on a huge backlog.

### 3.12 Rename-collision
For each candidate, compute `newname = prefix + name`. If `newname` already exists on disk, **skip** that file and log `SKIP (target exists)`; otherwise `os.rename(path, newpath)`. Never overwrite.

### 3.13 Logging
A log line is printed **only** when the bot acts (an Item_ID action, a rename, a SKIP, a WARN, or the first-run baseline notice) or under `--dry-run`. Each line is `"<UTC timestamp> <message>"` with `flush=True`. A run that does nothing prints nothing, keeping the log — and therefore git/sync — quiet.

## 4 · Algorithms (per-run control flow)

1. Parse args: `--dry-run`, `--backfill`, optional positional `VAULT_ROOT` (else derive from the script location by walking up to the first ancestor containing `_INFRA`).
2. Resolve `inbox = <VAULT_ROOT>/_InBox`; error out if it is not a directory.
3. **Pass 1 — Item_ID:** for each cold, non-excluded, top-level `.md` file, run the §3.5 state machine; log any action.
4. Load `baseline` (§2). If `baseline is None` and not `--backfill`: write the baseline (unless dry-run), log the first-run notice, **return**. If `baseline is None` and `--backfill`: treat baseline as empty.
5. **Pass 2 — date-stamp:** build `candidates` (§3.8); if empty, return silently; if over the backstop, WARN and return (§3.11); else for each candidate compute the prefix (§3.7) and rename (§3.12), logging each.

## 5 · CLI / interface contract

| Surface | Contract |
|---|---|
| Invocation | `python3 datestamper.py [--dry-run] [--backfill] [VAULT_ROOT]` |
| `--dry-run` | Log intended actions; change nothing, including the baseline (DS-FR-12). |
| `--backfill` | Ignore the baseline; treat every unstamped, cold, non-excluded top-level file as a stamping candidate (DS-FR-6). |
| `VAULT_ROOT` (positional) | Vault root; if omitted, discovered by walking up from the script to the first directory containing `_INFRA`. |
| stdout | Human-readable, UTC-timestamped action lines only (DS-FR-14). |
| stderr / exit | Non-zero exit with a message if the vault root cannot be located or the inbox does not exist; otherwise exit 0. |

**Versioning:** the observable contracts that downstream state depends on — the `YYMMDDHHmm - ` stamp shape (§3.2/3.7), the Item_ID uppercase-UUID form (§3.5), and the `_AI-` exclusion (§3.3) — are frozen; changing any of them is a breaking change to the vault's naming/ID conventions and must be treated as such (a re-baseline and/or backfill migration).

## 6 · Failure modes & responses

| Failure | Behavior |
|---|---|
| File mid-write / mid-sync | Not cold (§3.6) → deferred to a later run. No partial action. |
| Interrupted Item_ID write | Atomic replace (§3.10) → the file is either the old bytes or the full new bytes, never partial. |
| Filesystem reports no birth time (`%W == 0`) | Fall back to mtime (§3.7); stamp still written. |
| Two files would collide on the stamped name | Second one skipped and logged (§3.12); neither overwritten. |
| Huge backlog appears at once | Backstop aborts the whole stamp pass with a WARN (§3.11); operator investigates, may run `--backfill` deliberately. |
| Malformed frontmatter | Left untouched (§3.5 case 2); never corrupted by a guess. |
| Overlapping invocations | Prevented at the deployment layer by a non-blocking lock (DS-NFR-6; `runbook.md`). |
| Wrong/unintended rename | Recoverable via git history (DS-NFR-4). |

## 7 · Alternatives rejected

- **A rolling baseline (update it every run).** Rejected in favor of a frozen first-run baseline — see ADR-002. A rolling set would let a file that was manually renamed *back* to an unstamped form escape stamping, and blurs the "existing backlog" protection.
- **mtime instead of birth time.** Rejected — see ADR-001. Birth time better approximates "creation," accepting the git/sync-rewrite caveat, which is bounded by stamping promptly and freezing the value into the name.
- **A Markdown/YAML parser library for the Item_ID edit.** Rejected: it would add a third-party dependency (violating DS-NFR-1) and risk re-serializing (reordering keys, dropping comments) the operator's frontmatter. A line-based, first-`Item_ID`-only edit preserves the file byte-for-byte except the one line.
- **Stamping the existing backlog by default.** Rejected as unsafe (mass rename, broken links) — made opt-in via `--backfill` (ADR-002).
- **Blocking lock (`flock` without `-n`).** Rejected: a slow run must be *skipped*, not queued, or a backlog of minute-by-minute invocations could pile up.
