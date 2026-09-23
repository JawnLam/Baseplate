---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: baseplate-version
title: "Baseplate — Version"
Date_Added: 2026-07-16
Date_Modified: 2026-08-04
Needs_Processing: false
baseplate_Version: "1.3.0"
schema_status: "FROZEN"
release_date: 2026-08-04
---

# Baseplate — Version

This is Baseplate **v1.4.0** — the lifecycle release. v1.4.0 extends Baseplate past a product's birth: three additive document classes join the selection registry (the **evolution roadmap / deferred-scope register**, **maintenance & stewardship**, and **data lifecycle** — each earned by interview answers and mapped to existing structural Types, so the frozen v1.0 schema is untouched), and a new engine chapter **`07-EVOLUTION.md`** adds two recursive routes: **MILESTONE-STACK** (a ripened roadmap entry re-enters Baseplate — re-entry gate, scoped interview, selection delta, generation, affected gates, repackaging, register advance) and **ROADMAP-UPDATE** (governed register maintenance with recorded rationale and a register-only audit). The packaged folder's self-containment is unweakened: builders never need Baseplate; recursion is operator-side re-entry through the bootstrap's provenance, and the product bootstrap gains a tenth required section (§ Evolution) stating that seam self-containedly. Also reconciles the canonical catalog with four in-field entries (PF-14 retired-unused, PF-15 operator-intent inversion, PF-16 source-intake integrity, PF-17 fix-round coherence debt) and adds PF-15's missing traceability paragraph. Release gates: the constitutional regression probe and a new evolution probe, both passed by fresh instances; scope pre-registered before the build. v1.3.0 narrows the close-out `Records/` reference exemption (`06-CLOSE-OUT.md` Step 1.4) from "may reference anything" to "may reference anything **that resolves**" and adds a bounded manual sweep of `Records/` for this volume's controlled identifiers (`PF-`/`OFR-`/`ONF-`/`BM-`/cartridge-ID), a cross-record count-agreement check (Step 5.1), and a per-volume catalog-numbering rule (Step 6) — closing PF-13 (a Records-zone defect invisible to Construction-scoped gates, from the AcuityFlow engagements). It also reconciles the release history with four in-field catalog entries (PF-9..PF-12) that were added during real engagements but never recorded, bringing the catalog range to PF-1..PF-13; the frozen v1.0 schema is untouched. v1.2.0 adds a generation-standard mandate (rule 11) that every generated `Item_ID` is an uppercase 8-4-4-4-12 UUID matching the vault `Master_Schema` — slugs are retired from shipped artifacts (the two example cartridges were re-stamped) and the `_types/` templates carry a `"<UUID>"` placeholder; the frozen v1.0 schema is untouched. v1.1.0 added the **close-out packaging protocol** (`06-CLOSE-OUT.md`, OFR-16): a shipped stack is now packaged into a self-contained handoff folder (product `AI-BOOTSTRAP.md` + `Construction/` + `Records/` + `Build/`) before the engagement may be called done. Schema untouched (still FROZEN v1.0); minor bump per SemVer (additive engine chapter + strengthened route definition). v1.0.0 provenance: Designed inside Operating-Volume-Engineering and passed through its gauntlet (traceability matrix + golden-session gate); its worked example (`Example-Product-Linkrot`) converged to a stranger-test PASS, which fired the schema-freeze trigger. Promoted from rc.2 once the maturity evidence (bucket 5) was demonstrated: a **cross-family golden session** (Google Gemini) passed, and the **first real cartridge** (`Example-Product-Datestamper`, recovered from a production tool) passed the stranger test on run 1 and grew the portfolio catalog its second use-derived entry (PF-7) — converting REQ-M1 (the flywheel) from committed to demonstrated. `CHANGELOG.md` is the authoritative release history; this file and README derive their version from it.

| Identifier | Value | Notes |
|---|---|---|
| **Software** | v1.4.0 | Engine (canon + 7 protocol chapters + bootstrap), 8 structural Types + class registry (incl. the three v1.4.0 lifecycle classes), 5-zone content model incl. the Grows-Through-Use portfolio zone, consistency-audit + stranger-test gates + close-out packaging + evolution routes, portfolio failure catalog (PF-1..PF-17), manual VALIDATION-CHECKLIST |
| **Schema** | v1.0 (FROZEN) | The 8 structural Types + the document-class registry + the stranger-test pass rule are frozen as of the first stranger-test-passed cartridge (Linkrot, 2026-07-16). See § Schema policy. Unchanged in v1.4.0 (the three new classes are additive menu rows on existing Types — the permitted additive case; the evolution routes are engine prose) |
| **Engine** | v1.4 | `_baseplate-engine/` chapters 00–07 + BOOTSTRAP-NEW-STACK |
| **Built on** | OVE v2.7.1 | Provenance only — Baseplate has no runtime dependency on OVE once shipped |
| **Knowledge source** | self_contained | The canon is baked into `01-THE-CANON`; no KAOV |
| **Release date** | 2026-09-23 (v1.4.0) | Prior: v1.3.0 2026-08-04; v1.0.0 2026-07-19 |

## Schema policy

**The schema is FROZEN** as of the first cartridge to pass the stranger test (Linkrot, 2026-07-16). The frozen surface is: the eight structural Types and their required sections (`_types/`), the document-class registry (`03-SELECTION.md`), and the stranger-test pass rule (`05-GATES.md` Gate 2 ST-3). After this point, per `CONTRIBUTING.md`:

- **Additive** (no bump needed beyond patch): new document classes on the menu that map to an existing structural Type; new failure-catalog entries; documentation.
- **Major bump + migration note:** changing a structural Type's required sections, removing/renaming a Type or engine chapter, or changing the stranger-test pass rule.

## Naming

The vocabulary audit is **closed**: the OV is named **Baseplate** (from the working titles Foundry → Keel → Baseplate). The name and the `baseplate_` namespace are settled; a future rename would be a major bump (it renames the namespace).

Original work by Jawn Lam. Built on Operating-Volume-Engineering (CC-BY 4.0).
