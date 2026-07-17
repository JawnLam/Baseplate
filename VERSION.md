---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: baseplate-version
title: "Baseplate — Version"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
baseplate_Version: "1.0.0-rc.2"
schema_status: "FROZEN"
release_date: 2026-07-16
---

# Baseplate — Version

This is Baseplate **v1.0.0-rc.2** — the release candidate. Designed inside Operating-Volume-Engineering and passed through its gauntlet (traceability matrix + golden-session gate); its worked example (`Example-Product-Linkrot`) converged to a stranger-test PASS, which fired the schema-freeze trigger. `CHANGELOG.md` is the authoritative release history; this file and README derive their version from it.

| Identifier | Value | Notes |
|---|---|---|
| **Software** | v1.0.0-rc.2 | Engine (canon + 5 protocol chapters + bootstrap), 8 structural Types + class registry, 5-zone content model incl. the Grows-Through-Use portfolio zone, consistency-audit + stranger-test gates, portfolio failure catalog (PF-1..PF-6), manual VALIDATION-CHECKLIST |
| **Schema** | v1.0 (FROZEN) | The 8 structural Types + the document-class registry + the stranger-test pass rule are frozen as of the first stranger-test-passed cartridge (Linkrot, 2026-07-16). See § Schema policy |
| **Engine** | v1.0-rc | `_baseplate-engine/` chapters 00–05 + BOOTSTRAP-NEW-STACK |
| **Built on** | OVE v2.7.1 | Provenance only — Baseplate has no runtime dependency on OVE once shipped |
| **Knowledge source** | self_contained | The canon is baked into `01-THE-CANON`; no KAOV |
| **Release date** | 2026-07-16 | |

## Schema policy

**The schema is FROZEN** as of the first cartridge to pass the stranger test (Linkrot, 2026-07-16). The frozen surface is: the eight structural Types and their required sections (`_types/`), the document-class registry (`03-SELECTION.md`), and the stranger-test pass rule (`05-GATES.md` Gate 2 ST-3). After this point, per `CONTRIBUTING.md`:

- **Additive** (no bump needed beyond patch): new document classes on the menu that map to an existing structural Type; new failure-catalog entries; documentation.
- **Major bump + migration note:** changing a structural Type's required sections, removing/renaming a Type or engine chapter, or changing the stranger-test pass rule.

## Naming

The vocabulary audit is **closed**: the OV is named **Baseplate** (from the working titles Foundry → Keel → Baseplate). The name and the `baseplate_` namespace are settled; a future rename would be a major bump (it renames the namespace).

Original work by Jawn Lam. Built on Operating-Volume-Engineering (CC-BY 4.0).
