---
type: BASEPLATE_Design_Document
Item_ID: linkrot-adr-001
title: "Linkrot — ADR-001 — External URL checking is opt-in"
baseplate_Product_Slug: "LR"
baseplate_Doc_Class: adr
baseplate_Layer: 2
baseplate_Document_Status: audited
baseplate_Precedence_Rank: 3
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
---

# ADR-001 — External URL checking is opt-in (default off)

## Context

Linkrot can check internal links (fast, deterministic, offline) and external HTTP(S) links (slow, network-dependent, flaky, and privacy-leaking — checking a URL reveals it to its host). Making external checks the default would make the common case (fix internal rot) slow and non-deterministic.

## Options

1. External checks always on.
2. External checks off by default, opt-in via `--check-external`.
3. Two separate tools.

## Decision

**Option 2.** Internal-only by default; `--check-external` opts in (LR-FR-5). One tool, one clear flag.

## Consequences

- Default runs are fast, deterministic, offline-safe, and privacy-preserving.
- External flakiness (transient 5xx, rate limits) is isolated to opt-in runs; the acceptance plan documents that external results are best-effort (AT-6 tolerates transient network conditions by re-running).
- CI can use the fast default to gate internal rot without network flakiness.
