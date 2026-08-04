---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: baseplate-changelog
title: "Baseplate — Changelog"
Date_Added: 2026-07-16
Date_Modified: 2026-08-04
Needs_Processing: false
---

# Changelog

Format follows [Keep a Changelog](https://keepachangelog.com/); versioning follows [SemVer](https://semver.org/).

## [1.3.0] — 2026-08-04

**Records-zone reference discipline (PF-13); release history reconciled.** Operator-directed engine change from the AcuityFlow-v1 and AcuityFlow-Potemkin-Demo engagements: the close-out reference sweep (`06-CLOSE-OUT.md` Step 1.4) previously exempted `Records/` files from reference checking entirely ("Records files may reference anything — they are history"). That exemption was unbounded, so an *unresolvable* pointer inside `Records/` was permitted, not merely unchecked. Two defects rode through every gate on the strength of it — a records citation to `PF-14` for what was really this volume's `PF-12` (the assumed intervening number belonging to a **different** operating volume's catalog), and a stranger-test total restated across six records with one divergent copy — because Gate 1, the mechanical packaging check, and the orientation probe are all Construction-scoped. Schema untouched (FROZEN v1.0); minor bump — additive engine discipline plus a narrowed exemption, no Type/section/pass-rule change.

### Added

- **PF-13 — Records-zone defect invisible to Construction-scoped gates** (`_portfolio/failure-catalog.md`): a `Records/` file carrying an unresolvable controlled-identifier reference or a drifted restated figure survives every gate because all gates are Construction-scoped. Prevention is at close-out (the Step 1.4 / 5.1 / 6 additions below); the generalising lesson — *when adding any gate or sweep, name the zone it covers and ask what the other zones now permit by its omission* — is recorded with the entry. Traced in `_meta/TRACEABILITY.md` (OFR-16 row + failure-modes paragraph).

### Changed

- **`_baseplate-engine/06-CLOSE-OUT.md` Step 1.4 (reference sweep)** — the `Records/` exemption is narrowed from "may reference anything" to "may reference anything **that resolves**": records remain free to point outside the package (the engine, the portfolio catalog, prior sessions, source material) but may not point at something that does not exist. Adds a manual, bounded sweep of `Records/` for this volume's controlled identifiers (`PF-`, `OFR-`, `ONF-`, `BM-`, and the cartridge's own ID scheme), confirming each resolves **in this volume**; free-prose and external references remain unbounded. Notes that a controlled number found only in another volume's catalog, or in an unreconciled copy of this one, is a dangling reference here (PF-13).
- **`_baseplate-engine/06-CLOSE-OUT.md` Step 5.1 (mechanical packaging check)** — now also requires the Step-1.4 sweeps clean for **both** `Construction/` and `Records/`, and that every figure restated across `Records/` (gate counts, stranger-test totals, rerun counts) agrees with its one owning record (PF-9 genus applied to the record zone).
- **`_baseplate-engine/06-CLOSE-OUT.md` Step 6 (close the cartridge)** — adds the per-volume catalog-numbering rule: read the last `PF-n` in **this** volume's catalog and assign the next integer; a `PF-` number seen in another volume's catalog, or in an unreconciled copy of this one, is not this volume's next number. Never assign a number by assumption.
- **`_baseplate-engine/06-CLOSE-OUT.md` checklist** — gains the Records reference-resolution + cross-record count-agreement box (PF-13).
- **`_meta/TRACEABILITY.md`** — OFR-16 row's Verification cell gains the Records identifier-resolution sweep, the cross-record count-agreement check, and the per-volume catalog-number read; its "failure prevented" gains PF-13.

### Reconciliation

- **Release history brought current with in-field catalog growth.** Four failure modes were appended to the portfolio catalog and traceability matrix during real engagements after v1.2.0 shipped — **PF-9** (cross-document enumeration drift, Gridlock close-out probe), **PF-10** (operational content pinned outside the stack, PTIS), **PF-11** (source-citation bleed in decomposition, OmniLattice-Deal-Room), and **PF-12** (unsourced required input, the AcuityFlow engagements) — as the additive/patch-class changes the change discipline permits, but none was recorded in `CHANGELOG.md` or `VERSION.md`. This release backfills those entries into the canonical catalog + traceability (they had reached only the in-use install) and brings the recorded catalog range to **PF-1..PF-13**. No fabricated per-entry dates: the four are folded here rather than given retroactive version stamps.

## [1.2.0] — 2026-07-20

**Item_ID UUID mandate.** Operator-directed generation-standard change: every generated document's `Item_ID` must now be an uppercase 8-4-4-4-12 UUID matching the target vault's `Master_Schema` — slugs are retired from shipped artifacts. Schema untouched (FROZEN v1.0 — this is a generation-standard + template-placeholder change, not a Type or required-section change); minor bump per SemVer.

### Added

- **Generation standard 11** (`04-GENERATION-STANDARDS.md`) — mandates the uppercase 8-4-4-4-12 UUID `Item_ID` format; slugs not permitted; a fresh UUID per document.

### Changed

- **All 8 structural Type templates** (`_types/`) — `Item_ID` placeholder changed from `"UUID-OR-SLUG"` to `"<UUID>"` to enforce the mandate at instantiation. Frontmatter-field placeholder only; no required-section change, so schema semantics are unchanged.
- **Both example cartridges** (`Example-Product-Linkrot`, `Example-Product-Datestamper`) — re-stamped from slug `Item_ID`s to fresh UUIDs for conformance.

### Migration

- Any stack generated before v1.2.0 with slug `Item_ID`s should be re-stamped to uppercase UUIDs to conform to the vault `Master_Schema`. In-stack cross-references use requirement IDs and filenames (not `Item_ID`), so re-stamping is non-breaking.

## [1.1.0] — 2026-07-20

**Close-out packaging protocol (OFR-16).** Operator-directed engine addition from the third cartridge (Gridlock, the first game): a stack that passes Gate 2 is no longer a finished engagement — it must be **packaged into a self-contained handoff folder** a fresh AI can build from on the strength of one sentence ("read everything in this folder and build it for me"). Schema untouched (FROZEN v1.0); minor bump — additive chapter plus strengthened route definition.

### Added

- **`_baseplate-engine/06-CLOSE-OUT.md`** — the packaging protocol: restructure to the final layout (top level = product `AI-BOOTSTRAP.md` + `Construction/` [the former `Artifacts/`] + `Records/` [non-essential history, immutable] + `Build/` [seeded builder workspace]); `Construction/MANIFEST.md` with per-file SHA-256s + gate verdicts (verify-before-build); a product bootstrap with nine required sections including the **operator-input register** (`item | why | when-to-ask trigger` — the complete list of everything the builder may ever need from the operator); seeded `Build/build-state.md` (state lives in files, exported to the build phase) and `Build/deviations.md` (the standing reconciliation rule feeding REVISE-STACK); packaging verification = mechanical check + **fresh-instance orientation probe**; REVISE-STACK-on-a-packaged-cartridge rules (edit `Construction/` in place, regenerate manifest, append-only `Records/`).
- **OFR-16** traced in `_meta/TRACEABILITY.md`.
- **PF-8 — Unparameterized acceptance metric** (from the Gridlock Gate-2 evaluator): a success metric referencing a threshold no schema names as an extractable value; survives both gates by construction, so prevention is generation-time (every comparative phrase must resolve to a named field). Cataloged in `_portfolio/failure-catalog.md`, traced in `_meta/TRACEABILITY.md`.

### Changed

- **`00-START-HERE.md`** — session shape now ends at close-out packaging, not the stranger test; Tier-2 table gains the 06 row; never-do list gains "declare an engagement finished without running the close-out packaging."
- **`05-GATES.md § Close-out`** — split into per-session state write vs per-cartridge packaging; the latter routes to 06.
- **`BOOTSTRAP-NEW-STACK.md` Step 6** — rewritten as "Close-out packaging (mandatory)"; the quality-gate checklist is now titled "before the engagement is called done" and gains the packaging box.
- **`AI-BOOTSTRAP.md`** (root mirror) — Tier-2 table gains the 06 row.
- **`_baseplate-engine/_meta/VALIDATION-CHECKLIST.md`** — gains the close-out packaging section; Overall gains the packaging box.
- Version strings synchronized (VERSION, README, CONTRIBUTING, INSTALL folder-name example).

## [1.0.0] — 2026-07-19

**Baseplate 1.0.0.** Promoted from rc.2 on the strength of the maturity evidence below (bucket 5). No engine, schema, or Type change from rc.2 — 1.0.0 is rc.2 plus the demonstrated maturity evidence. Two bucket-5 confirmations are deferred to a **1.0.1 roadmap** (they refine, they do not gate): the cross-family *stranger* test (the cross-family *golden* already passed) and a standalone REVISE-STACK demonstration (deferred deliberately rather than corrupt the faithful real cartridge).

- **Cross-family golden session — Google Gemini — PASS (2026-07-19).** A fresh non-Claude instance, pointed only at the public repo, exhibited Baseplate's constitutional behavior: it refused a three-part "write the PRD/architecture/API" demand and began the interview (elicit before generate, one question at a time), and it flagged a fabricated dependency (the invented "Kahneman-Treverton Route Sufficiency Model") as a **PF-1** violation rather than designing around it. This discharges the "single model family (Claude)" caveat on the golden-session gate and supplies the substrate-agnosticism (P1) evidence for that gate. Logged in `_meta/golden-session-script.md § Cross-family run`.
- **First real cartridge shipped — Inbox-Datestamper — stranger-test PASS on run 1 (2026-07-19).** `Example-Product-Datestamper/` is the first cartridge built from a **real, in-production** tool (requirements recovered from the shipped `datestamper.py`, not invented). It earns Types Linkrot never exercised — **L5 Operations (runbook)** and **two ADRs** — and it **front-ran PF-6** (froze every behavioral equivalence class up front). Result: the fresh-instance stranger test passed on the **first** run (zero `stack-should-answer`), where Linkrot took three. This **converts REQ-M1 (the portfolio flywheel) from committed to demonstrated:** close-out grew the catalog its second use-derived entry, **PF-7 (cross-zone dangling reference)**, caught by the Gate 1 audit. `_meta/TRACEABILITY.md` O-1 flipped from "intentional-empty" to "demonstrated."
- **Engine self-audit fix (ENG-1):** removed the empty `_baseplate-engine/_templates/` directory that contradicted `04-GENERATION-STANDARDS.md` ("there is no separate `_templates/` directory") — a Gate-1-class self-contradiction the real-cartridge shakedown surfaced.
- **1.0.1 roadmap (deferred confirmations, non-gating):** the cross-family *stranger test* (PROMPT 2, Gemini — prompt staged) and a standalone REVISE-STACK demonstration. The golden-session substrate-agnosticism, the first real end-to-end cartridge, and the compounding flywheel (two PF entries from two real shakedowns) are all demonstrated as of 1.0.0.

## [1.0.0-rc.2] — 2026-07-16

Independent re-verification of the rc.1 self-audit (a fresh model instance, not the author) found seven residual defects the rc.1 fixes missed; all closed. rc.2 is the first candidate that genuinely passes Baseplate's own Gate 1 consistency audit. (Promotion to v1.0.0 still awaits bucket 5 — a cross-family run + real cartridges.)

- **Phantom validator survived in two places** rc.1 didn't touch: `INSTALL.md § Requirements` ("the optional validator needs Python 3.7+") and README's folder-structure row for `_meta/` ("validator"). Both now state the no-validator-by-design posture. `CONTRIBUTING.md`'s Engine-Zone row likewise still said "templates, meta, validator" — now points at the manual `VALIDATION-CHECKLIST.md`.
- **`_templates/` reference survived in `AI-BOOTSTRAP.md`** (rc.1 fixed 00-START-HERE and 04-GENERATION-STANDARDS only). Since 00-START-HERE declares AI-BOOTSTRAP a mirror whose divergence is itself a defect, the row now matches: `_types/*` + the `03-SELECTION.md` registry are the templates.
- **Duplicate requirement ID in `_meta/TRACEABILITY.md`:** the new behavioral-completeness row was labeled OFR-6, which already names dependency verification — violating generation standard 1 (stable, unique IDs) in the engine's own matrix. Renumbered **OFR-15** and moved after OFR-14.
- **Broken table in `CONTRIBUTING.md`:** the Operator-Private-Zone clarification paragraph was inserted mid-table, orphaning the `.DS_Store` row. Row restored to the table; paragraph moved below it.
- **Orphaned waiver destination:** `05-GATES.md` Gate 1 sent audit waivers to `_decisions.md`, a file the rc.1 backbone reconciliation removed. Waivers now go to the cartridge state (`_design-state.md`, or `_ov-manifest.md` for single-session cartridges). `_meta/posture.yaml`'s REQ-B1 evidence updated to the same backbone file names.
- **Stale version examples in `INSTALL.md`** (`Baseplate-v0.1` clone/rename examples) updated to the v1.x era.

Verified clean after the fixes: no `validate.py`/`validator`/`_templates/` references outside the changelog, no references to backbone files that don't ship, unique requirement IDs in the traceability matrix, PF-1..PF-6 all traced, gitignore carve-out confirmed by probe (`git check-ignore`: example-cartridge files re-included, operator-cartridge files ignored).

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
