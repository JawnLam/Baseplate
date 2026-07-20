---
type: BASEPLATE_Design_Document
Item_ID: C5D14352-6860-4A0D-8085-4C0B6DBDF66C
title: "Inbox-Datestamper — ADR-002: Baseline / new-files-only vs backfill"
baseplate_Product_Slug: "DS"
baseplate_Doc_Class: adr
baseplate_Layer: 2
baseplate_Document_Status: audited
baseplate_Precedence_Rank: 4
Date_Added: 2026-07-19
Date_Modified: 2026-07-19
Needs_Processing: false
---

# ADR-002 — Stamp **new files only**, protected by a frozen first-run baseline

## Context

When the bot is first deployed, the inbox already contains a **backlog** of existing files. Some of those files are **linked** from elsewhere in the vault by `[[wikilinks]]` or embeds. A filesystem rename does **not** rewrite those links (only renaming inside Obsidian does), so stamping the backlog would silently **break links** and would hit the operator with a surprise **mass rename** of files they already know by name. The bot needs a way to stamp genuinely-new arrivals while leaving the backlog alone — and to make backlog-stamping possible only as a deliberate act.

## Options

1. **Stamp everything unstamped, every run.** Simplest. But it mass-renames the backlog on first deployment and breaks existing links — unacceptable.
2. **Record a baseline on the first run and stamp only files not in it.** The first run captures the current inbox contents and stamps nothing; every later run stamps only files absent from that baseline. Backlog stamping is available on demand via `--backfill`. Requires storing the baseline somewhere that does not itself sync into the vault.
3. **A rolling baseline** — re-record the inbox contents every run and stamp only the delta. Avoids storing a one-time snapshot but has a subtle hole (below).

## Decision

**Option 2 — a frozen first-run baseline, new-files-only, with an explicit `--backfill` escape hatch.** On the first run ever (no baseline stored), record the inbox's top-level filenames as the baseline and stamp **nothing**. Thereafter, a file is eligible only if it is **not in that original baseline**. Store the baseline **outside the vault** (a path like `<STATE_DIR>/baseline.txt`) so it never syncs and never itself becomes an inbox artifact.

## Consequences

- **Positive:** zero surprise renames and zero broken links on deployment; the backlog is protected permanently; new arrivals are stamped automatically; the operator can still stamp the backlog deliberately with a single `--backfill` run when they have accepted the link-rewrite cost.
- **Frozen, not rolling:** the baseline is written once and never updated (rejecting Option 3). A rolling baseline would re-absorb the current contents each run, so a file an operator manually renamed *back* to an unstamped form — or any file that briefly left and re-entered the baseline set — could quietly slip past stamping. A frozen snapshot means "new" always means "arrived after first deployment," which is the property the safety argument rests on.
- **State lives outside the vault:** this is a hard requirement, not a convenience — a baseline stored *in* the inbox would sync, would be stamped, and would corrupt the very set it defines. It also means the baseline is **not** git-recoverable; losing it causes the next run to re-baseline (and thus stamp nothing new until fresh arrivals), which is a safe failure.
- **Re-baseline is a supported operation:** deleting the baseline file makes the next run treat the current inbox as pre-existing again (stamp nothing) — the documented way to "forget history" safely (see `runbook.md`).
