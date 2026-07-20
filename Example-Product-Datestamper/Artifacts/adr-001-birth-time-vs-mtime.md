---
type: BASEPLATE_Design_Document
Item_ID: 8BF7C252-C436-4165-BC4B-2DAB9093F01E
title: "Inbox-Datestamper — ADR-001: Birth time vs mtime for the stamp"
baseplate_Product_Slug: "DS"
baseplate_Doc_Class: adr
baseplate_Layer: 2
baseplate_Document_Status: audited
baseplate_Precedence_Rank: 3
Date_Added: 2026-07-19
Date_Modified: 2026-07-19
Needs_Processing: false
---

# ADR-001 — Use filesystem **birth time** (not mtime) as the stamp value

## Context

The stamp is meant to record **when a file was created / first arrived** in the inbox. Two filesystem timestamps are candidates: **mtime** (last-modified) and **birth time** (creation). On this vault the situation is complicated: git operations (checkout, reset) and Obsidian Sync **rewrite filesystem timestamps**, so neither timestamp is a perfect record of "when the human first made this." mtime in particular changes on every edit or sync, so it drifts *after* arrival.

## Options

1. **mtime.** Always available, no external call. But it changes on any later edit/sync, so a file edited a week after arrival would stamp with the edit date — wrong for "creation."
2. **Birth time via `stat -c %W`, fall back to mtime when unavailable.** Closer to "creation"; on this vault it effectively means "when the file landed on the server." Requires a subprocess to GNU `stat` (the CPython here lacks `st_birthtime`). Returns 0 when the filesystem/kernel records no birth time, requiring a fallback.
3. **Parse a timestamp out of the file's own content** (e.g. a frontmatter `created:` field). Most semantically correct but only works for notes with that field, not for audio/PDF/images, and many inbox files have no frontmatter at all.

## Decision

**Option 2 — birth time with an mtime fallback.** The operator explicitly wanted "creation time," and birth time is the closest filesystem approximation that works uniformly across *all* file types (notes, audio, PDF, images), which mattered because the stamp pass covers any file type. Read via `stat -c %W`; if that is `0`/absent/unparseable, use mtime.

## Consequences

- **Positive:** a single, uniform rule for every file type; the stamp approximates arrival, not last-edit; the value is computed once and **frozen into the filename**, so later timestamp rewrites cannot change an already-stamped file.
- **The caveat (stated, not hidden):** birth time here really means **"when the file landed on this server,"** not necessarily when it was first typed on another device. Because the bot stamps promptly (every minute) and freezes the value, this is stable going forward. The only way a stamp misrepresents arrival is a file that sat **unstamped** through a later git/sync timestamp rewrite — a narrow window the every-minute cadence keeps small.
- **Portability cost:** `stat -c %W` is GNU coreutils; BSD/macOS use `stat -f %B`. The *semantics* (birth time, mtime fallback) are portable; the exact invocation is platform-specific and is a known migration point if the bot moves off Linux (recorded in the dependency log).
- **Fallback visibility:** when birth time is unavailable and mtime is used, the stamp silently reflects mtime; this is acceptable because on a freshly-arrived file mtime ≈ arrival anyway.
