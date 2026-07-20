---
type: BASEPLATE_Verification_Document
Item_ID: 93C70C57-A9EC-4CEA-825C-ADDE60490CFD
title: "Inbox-Datestamper — Acceptance Test Plan"
baseplate_Product_Slug: "DS"
baseplate_Doc_Class: acceptance-test-plan
baseplate_Layer: 4
baseplate_Document_Status: audited
baseplate_Precedence_Rank: 6
Date_Added: 2026-07-19
Date_Modified: 2026-07-19
Needs_Processing: false
---

# Inbox-Datestamper — Acceptance Test Plan

## Test strategy

Behavioral tests against a **temporary inbox fixture** (a throwaway `_InBox` under a temp `_INFRA` root) plus a temp `<STATE_DIR>`. Each test seeds files, runs one invocation (with flags as noted), and asserts on filenames, frontmatter bytes, mtime, and log output. Time-dependent tests set file mtimes explicitly to control coldness; birth-time tests either use a filesystem that records birth time or force the `%W == 0` fallback. Definition of done: **every AT passes**, and the traceability matrix shows a total requirement↔verification bijection.

## Acceptance criteria (AT-1..AT-23)

| ID | Given / When / Then | Pass condition |
|----|---------------------|----------------|
| AT-1 | A cold top-level `note.md` with frontmatter but no `Item_ID` → run → | `Item_ID:` inserted as first frontmatter key with a valid uppercase UUID |
| AT-2 | Files exercising each normalization case: no-frontmatter, empty value, non-UUID slug, lowercase UUID, already-uppercase UUID → run → | Each resolves per §3.5: added-block / filled / replaced / uppercased / **unchanged** respectively |
| AT-3 | A `.md` file gets an Item_ID edit → | File body byte-identical except the one `Item_ID` line/block; **mtime unchanged**; filename unchanged (no rename in Pass 1) |
| AT-4 | A `.md` file with an opening `---` but **no closing fence** → run → | File left **untouched** (no Item_ID added, no corruption) |
| AT-5 | A cold new top-level `Meeting.md` with birth time 2026-07-09 13:15 PT → run (baseline present, file not in it) → | Renamed to `2607091315 - Meeting.md` |
| AT-6 | **First run ever** (no baseline) on a populated inbox → run → then drop a new file → run again → | First run: baseline written, **nothing stamped**; second run: only the new file stamped; backlog untouched |
| AT-7 | A file already named `2607091315 - Meeting.md` → run → | **Not** re-stamped (idempotent; `^\d{10} - ` matched) |
| AT-8 | `_AI-INBOX-INSTRUCTIONS.md` and `2607091315 - _AI-x.md` present → run → | Neither gets an Item_ID nor a (further) stamp (excluded, case-insensitive) |
| AT-9 | A file modified <20s ago (hot) → run → | Deferred: no Item_ID edit, no stamp this run; acted on after it goes cold |
| AT-10 | An inbox with (MAX_RENAMES_PER_RUN + 1) eligible files → run → | **Nothing** renamed; a single `WARN abort:` line logged |
| AT-11 | Two eligible files whose birth times produce the **same** stamped name → run → | First renamed; second **skipped** with `SKIP (target exists)`; no overwrite |
| AT-12 | Any actionable inbox → run `--dry-run` → | Log shows intended actions; **no** file renamed, **no** frontmatter changed, **no** baseline written |
| AT-13 | A cold new `fresh.md` with no Item_ID, not in baseline → single run → | Same run: Item_ID added **and** file stamped (mtime-preservation kept it cold for Pass 2) |
| AT-14 | A run with nothing to do → run → | **No** log output at all; an acting run's lines each begin with a UTC timestamp |
| AT-15 | A subfolder file, a hidden `.dotfile`, a top-level `audio.m4a`, a top-level `note.md` → run → | Subfolder + hidden untouched; `audio.m4a` **stamped** but gets **no** Item_ID; `note.md` gets both |
| AT-16 | Static inspection of `datestamper.py` imports → | Only Python 3.9+ stdlib modules imported; no third-party package (DS-NFR-1) |
| AT-17 | Run under a network monitor / offline → | Zero outbound connections; no model/API calls (DS-NFR-2) |
| AT-18 | Simulate an interrupt between temp-write and replace during an Item_ID edit → | Target file is either the **original** bytes or the **complete** new bytes — never partial (DS-NFR-3) |
| AT-19 | A stamp rename occurs in the git-tracked vault → | The rename is committed by the sync job and revertible via git history (DS-NFR-4) |
| AT-20 | A pre-existing file that is `[[wikilinked]]` elsewhere, present at first run → subsequent runs → | Never renamed (in baseline), so no link breaks (DS-NFR-5) |
| AT-21 | Two invocations overlap (one still running when the next fires) → | Second invocation is **skipped** by the non-blocking lock; no concurrent mutation (DS-NFR-6) |
| AT-22 | A file on a filesystem reporting **no** birth time (`%W == 0`) → run → | Stamp computed from **mtime** fallback; a valid `YYMMDDHHmm - ` prefix still applied (§3.7) |
| AT-23 | A top-level `note.markdown` (not `.md`) → run → | **Stamped** (date-stamp covers all types) but receives **no** Item_ID (Item_ID pass is `.md`-only, §3.4) |

## Pass / fail

- **Pass:** all of AT-1..AT-23 pass. Any failure blocks ship; fix the implementation (or, if the spec is wrong, run REVISE-STACK) and re-run the affected AT.
- The **stranger test** (Gate 2) is a **separate** ship gate over the frozen `Artifacts/` folder — run and recorded at the cartridge level, outside this folder — and is not one of these ATs.

## Sign-off

- **Operator sign-off** on: the two tunable defaults (stability 20s, MAX_RENAMES 500), the stamp timezone (`America/Los_Angeles`), and the license name.
- **Build sign-off:** all ATs green + traceability bijection total (`traceability-matrix.md`, zero orphans).
