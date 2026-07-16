---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: baseplate-changelog
title: "Baseplate — Changelog"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
---

# Changelog

Format follows [Keep a Changelog](https://keepachangelog.com/); versioning follows [SemVer](https://semver.org/).

## [0.1.0] — 2026-07-16

First build. Designed inside Operating-Volume-Engineering v2.6 and passed through its gauntlet.

### Added

- **Engine** (`_baseplate-engine/`): `00-START-HERE`, `01-THE-CANON` (the seven-layer taxonomy, source-grounded), `02-ELICITATION` (the product interview), `03-SELECTION` (the decision procedure + machine-readable document-class registry), `04-GENERATION-STANDARDS`, `05-GATES` (consistency audit + stranger test + revision), and `BOOTSTRAP-NEW-STACK`.
- **Eight structural Types** (`_types/`): Requirements, Design, Narrative, Verification, Operations, Contract, Stranger-Test-Log, Selection-Record — plus a class registry mapping ~20 canon document classes to them.
- **Gates:** a per-stack consistency audit and the **stranger test** (a fresh instance builds from the frozen `Artifacts/` folder; any `stack-should-answer` question blocks ship).
- **Portfolio failure catalog** (`_portfolio/`, the Grows-Through-Use Zone), seeded with five failure modes (dependency fabrication, stale-context carryover, orphan requirements, gold-plating, silent assumption absorption).
- **Posture** (Convention 10): `domain_stakes: low`; moat = REQ-M1 (data flywheel, the portfolio catalog) + REQ-M2 (switching cost, accumulated stacks).
- **Traceability matrix** (`_meta/TRACEABILITY.md`) and **golden-session** machinery (inherited from OVE v2.6).
- Front-door docs, five-zone content model, optional adapted validator + manual checklist.

### Notes

Schema is v0.x (DRAFT) — freezes at the first stranger-test-passed cartridge. "Baseplate" is a working title pending a vocabulary-audit decision.
