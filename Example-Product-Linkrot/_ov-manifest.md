---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: linkrot-ov-manifest
title: "Linkrot — Product Cartridge Manifest"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
doc_type: baseplate-cartridge-manifest
product_slug: "LR"
builder: "AI coding agent (solo, from these documents)"
---

# Linkrot — Product Cartridge Manifest

> Worked example. Product identity captured from the interview (`_product-interview.md`, PI-1..PI-12); the founding-document stack it earned lives in `Artifacts/`.

## Product

A one-shot command-line tool that scans a directory of Markdown files and reports broken links — internal (wikilinks, relative paths) and external (HTTP URLs). Read-only; CI-friendly exit codes; Python 3.11+ standard library only.

## Builder & handoff

Built by an **AI coding agent** solely from the stack in `Artifacts/` — which is exactly why this cartridge exists as Baseplate's worked example: the stranger test (`stranger-test-log.md`) confirms a fresh instance can build Linkrot from the frozen `Artifacts/` folder alone.

## Cartridge contents

- `_product-interview.md` — the PI-1..PI-12 record.
- `_selection-record.md` — the document set chosen (inclusions + exclusions with rationale), the `LR-` ID scheme, and the precedence declaration (S-3).
- `_dependency-log.md` — dependency verification (Python stdlib only; no third-party packages).
- `Artifacts/` — the founding-document stack (PRD, technical design, ADR-001, acceptance test plan, traceability matrix, license).
- `stranger-test-log.md` — the Gate 2 record (run-1/2 blocked → run-3 PASS, converged).
