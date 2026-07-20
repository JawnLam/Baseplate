---
type: Fleeting
timestamp: "2026-07-19T00:00:00Z"
Item_ID: datestamper-ov-manifest
title: "Inbox-Datestamper — Product Cartridge Manifest"
Date_Added: 2026-07-19
Date_Modified: 2026-07-19
Needs_Processing: false
doc_type: baseplate-cartridge-manifest
product_slug: "DS"
builder: "Mixed — solo human maintainer + AI coding agent, maintained cold from these documents"
---

# Inbox-Datestamper — Product Cartridge Manifest

> **The first *real* (not invented) cartridge.** Linkrot is a designed-for-the-example product; the inbox-datestamper is a bot that has been running in production (a personal Obsidian vault's inbox, cron every minute) since 2026-07-09. Its founding-document stack here was **recovered from the shipped implementation** (`datestamper.py` + its maintainer runbook), not authored from imagination — which is exactly what makes it the cartridge that converts REQ-M1 (the portfolio flywheel) from *committed* to *demonstrated*.

## Product

A **zero-AI-token** cron bot that does two deterministic chores on the top-level files of a Markdown vault's `_InBox`, once a minute, forever:

1. **Item_ID guarantee** — every top-level `.md` gets a non-empty uppercase-UUID `Item_ID` in YAML frontmatter (insert/fill/normalize; never destructive).
2. **Date-stamp** — every *new* top-level file (any type) is renamed with a creation-time prefix `YYMMDDHHmm - ` computed from filesystem birth time in Pacific time.

Pure filesystem/time/string logic; no network, no model calls; git-recoverable.

## Builder & handoff

Built and maintained by a **solo operator with AI assistance**, and — the load-bearing constraint — **written to be picked up cold** by a future person or AI. That "cold maintainer" is the stranger this stack is written for: the runbook (`Artifacts/runbook.md`) and the frozen semantics in the technical design exist so the bot can be rebuilt or safely modified from the `Artifacts/` folder alone.

## What this cartridge exercises that Linkrot did not

- **L5 Operations Type** (`BASEPLATE_Operations_Document` — the runbook): Linkrot was a one-shot CLI and never earned L5; the datestamper runs continuously on a schedule, so the runbook Type gets its first real workout (PI-8).
- **Two ADRs** (`BASEPLATE_Design_Document` at L2): birth-time-vs-mtime, and baseline/"new-files-only" — both genuinely contested decisions the source discusses (PI-11).
- **PF-6 front-run:** the Linkrot shakedown *discovered* under-specified behavioral semantics on run 1. Here the design freezes every equivalence class up front (generation standard 10). The stranger test is the check on whether that lesson took.

## Cartridge contents

- `_product-interview.md` — the PI-1..PI-12 record (recovered from the shipped bot).
- `_selection-record.md` — the document set chosen (inclusions + exclusions with rationale), the `DS-` ID scheme, and the precedence declaration (S-3).
- `_dependency-log.md` — dependency verification (Python 3.9+ stdlib + the `stat` coreutil; no third-party packages).
- `Artifacts/` — the founding-document stack (PRD, technical design, ADR-001, ADR-002, runbook, acceptance test plan, traceability matrix, license).
- `Sessions/` — session log(s) for this cartridge.
- `stranger-test-log.md` — the Gate 2 record.
