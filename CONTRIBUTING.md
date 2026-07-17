---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: baseplate-contributing
title: "Baseplate — Contributing"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
---

# Contributing to Baseplate

Baseplate ships at v1.0.0-rc.1. This document says what is in-scope without a major bump, what requires one, and the content-zone boundary that keeps `git pull` from clobbering your work.

## Content zones

Baseplate declares the four OVE-canonical content zones plus a **fifth zone** for the portfolio catalog.

### Engine Zone — release-owned; updated by `git pull`

| Path pattern | Notes |
|--------------|-------|
| `README.md`, `AI-BOOTSTRAP.md`, `INSTALL.md`, `OPERATOR-GUIDE.md`, `CONTRIBUTING.md`, `LICENSE.md`, `VERSION.md`, `CHANGELOG.md`, `UPDATE-PROMPT.md` | Front-door docs |
| `_baseplate-engine/**` | Engine prose (the canon, elicitation, selection, generation standards, gates) + `_meta/` (the manual `VALIDATION-CHECKLIST.md`) |
| `_types/**` | The eight structural Type definitions (Convention 6) |
| `.gitignore` | Defines the Operator-Private patterns below |

Operators do not hand-edit Engine-Zone files.

### Operator-Private Zone — gitignored; never tracked

| Pattern | Why |
|---------|-----|
| `_USER.md` | Operator profile; never auto-inferred (P7) |
| `<Product>/Sessions/*.md` | Per-cartridge session logs (verbatim working conversation) |
| `<Product>/_design-state.md`, `<Product>/_dependency-log.md` | Operator's active per-product working state |
| `.DS_Store`, `.obsidian/` | Filesystem/workspace noise |

These patterns apply to **your own** product cartridges. The release-owned `Example-Product-*/` cartridges are **not** operator-private — see the Shipped Examples Zone below; a `.gitignore` carve-out re-includes their working files so the worked example ships complete.

### Operator-Extension Zone — operator-created; survives `git pull`

| Pattern | Notes |
|---------|-------|
| `<Product-Name>/` at the OV root | Your own product cartridges — the OV is designed to be extended here |

### Shipped Examples Zone — release-owned; updated by `git pull`

| Path | Notes |
|------|-------|
| `Example-Product-*/` | Worked-example product cartridges demonstrating the stranger-test flow — these **ship in full** (their `Sessions/`, `_design-state.md`, and `_dependency-log.md` are re-included by a `.gitignore` carve-out, since they are release-owned reference implementations, not operator-private work) |

### Grows-Through-Use Zone — release-seeded, operator-appended *(Baseplate's fifth zone)*

| Path | Notes |
|------|-------|
| `_portfolio/failure-catalog.md` | The cross-cartridge failure/lesson catalog. The release ships the seed entries (PF-1..PF-5); your use appends more. |

**The tension this zone names:** the catalog is *neither* pure engine (the release doesn't own your appended lessons) *nor* pure operator content (the seed entries are release doctrine). On update, `git pull` must **merge, not clobber** — the update-workflow and `UPDATE-PROMPT.md` both use a stash/append-merge that preserves operator-appended entries. This is a generalization of how a failure-mode catalog naturally behaves; it is a candidate pattern to formalize upstream in OVE.

## What requires a major version bump

Renaming or removing an engine chapter; changing the cartridge backbone filenames; changing a structural Type's required sections such that existing stacks no longer validate; changing the stranger-test pass rule. Major bumps require a migration note in `CHANGELOG.md`.

## Release identity

`CHANGELOG.md`'s top entry is the single source of truth for the version; `VERSION.md` and `README.md` derive from it and must agree at ship (inherited from OVE's release discipline).

## Voice

Engine content is subject-agnostic (the engine never names a specific product) and written in the precise engineering-spec register: directive, term-defining, non-goal-stating, no flattery, no emojis.
