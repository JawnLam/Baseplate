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

## [1.0.0-rc.1] — 2026-07-16

Release candidate. Baseplate now **passes its own Gate 1 consistency audit** and declares its schema frozen. This closes buckets 1–4 of the v1.0 graduation; promotion to **v1.0.0** awaits the maturity evidence (bucket 5): a cross-family golden + stranger run (all runs so far were Claude-only) and one to three real product cartridges run end-to-end (converting the REQ-M1 flywheel from committed-but-empty to demonstrated, and exercising REVISE-STACK).

### Self-audit fixes (Gate 1 — the engine now passes its own audit)

- **De-orphaned ONF-1:** added `_baseplate-engine/_meta/VALIDATION-CHECKLIST.md` (the manual walkthrough of both gates) — the file the docs promised but didn't ship.
- **Removed the phantom validator:** README's tooling posture no longer references a `validate.py` that doesn't (and can't) exist — Baseplate ships no validator by design; the stranger test can't be mechanized.
- **Removed `_templates/` references** (00-START-HERE, 04-GENERATION-STANDARDS): the class registry's required-sections + the `_types/` definitions *are* the templates; there is no separate directory.
- **Traced PF-6** in `_meta/TRACEABILITY.md` (it was added to the catalog from the shakedown but not traced).
- **Fixed the content-zone contradiction:** the shipped `Example-Product-*` cartridges ship in full — a `.gitignore` carve-out re-includes their working files, and `CONTRIBUTING.md` says the Operator-Private patterns apply only to *your own* cartridges.
- **Completed the worked example's backbone** (`Example-Product-Linkrot/_ov-manifest.md`) and reconciled `BOOTSTRAP-NEW-STACK.md`'s promised backbone with the real cartridge shape (`_selection-record.md` carries S-3, replacing OVE's `_design-decisions.md` + `_schema-draft.md`). Fixed a dangling `_design-decisions.md` reference in the stranger-test log.
- **Fixed stale version strings** (CONTRIBUTING still said v0.1.0).

### Schema freeze (Migration note)

The schema-freeze trigger (first stranger-test-passed cartridge) fired when Linkrot passed on 2026-07-16, but nothing had been declared. **The schema is now FROZEN:** the 8 structural Types + their required sections, the document-class registry, and the stranger-test pass rule. After this point, changing a Type's required sections, removing/renaming a Type or engine chapter, or changing the pass rule is a **major bump with a migration note**; adding menu classes that map to existing Types, catalog entries, and docs stays additive. `schema_status` → `FROZEN`.

### Name settled

The vocabulary audit is closed on **Baseplate** (from Foundry → Keel). `VERSION.md` no longer calls the name "pending"; a future rename would be a major bump.

### Engine refinement landed

Added **generation standard 10 — behavioral completeness** (`04-GENERATION-STANDARDS.md`): define the equivalence class for every parsing/counting/comparison behavior. This is the PF-6 lesson that made the Linkrot shakedown take three stranger-test runs; front-running it makes the stranger test a confirmation, not a discovery.

## [0.1.1] — 2026-07-16

Patch — canon coverage. Extended `_baseplate-engine/01-THE-CANON.md` and the `03-SELECTION.md` class registry to close gaps against the source taxonomy this OV was built from:

- **New document classes on the menu + registry:** MRD (L0, largely-absorbed-into-PRD), ERD (L3, beside the data dictionary), SLI (L5, beside SLO), and a deployment/rollback + migration class (L5). All map to existing structural Types (`BASEPLATE_Narrative_Document` / `_Design_Document` / `_Operations_Document`) — no new Types, no schema change.
- **Lineage name-drops** added inline: Amazon "Working Backwards" (PR/FAQ), Joel Spolsky (functional spec), STRIDE (threat model), Kruchten's 4+1, the Google-design-doc / IETF→Rust→React RFC lineage. The standards grounding (ISO/IEC/IEEE 29148, IEEE 1016, 12207/15288, V-model, Zachman, Nygard, ICD/CDRL) is retained.
- **Rationale expanded:** the canon's §1 now states the multi-audience reason a stack can't collapse into one file (no single artifact serves product/engineering/QA/operations at once) *alongside* the change-rate reason, and notes the SRS is the closest single document to a zero-questions handoff.

No behavioral change to the engine, gates, or generated stacks.

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
