---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: keel-version
title: "Keel — Version"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
keel_Version: "0.1.0"
schema_status: "DRAFT"
release_date: 2026-07-16
---

# Keel — Version

This is Keel **v0.1.0** — the first build, designed inside Operating-Volume-Engineering v2.6 and passed through its gauntlet (traceability matrix + golden-session gate). CHANGELOG.md is the authoritative release history; this file and README derive their version from it.

| Identifier | Value | Notes |
|---|---|---|
| **Software** | v0.1.0 | First build. Engine (canon + 5 protocol chapters), 8 structural Types, 5-zone content model incl. the Grows-Through-Use portfolio zone, consistency-audit + stranger-test gates, seeded portfolio failure catalog (PF-1..PF-5) |
| **Schema** | v0.1 (DRAFT) | Structural-Types + document-class registry. Frozen at first stranger-test-passed cartridge |
| **Engine** | v0.1 | `_keel-engine/` chapters 00–05 + BOOTSTRAP-NEW-STACK |
| **Built on** | OVE v2.6.0 | Provenance only — Keel has no runtime dependency on OVE once shipped |
| **Knowledge source** | self_contained | The canon is baked into `01-THE-CANON`; no KAOV |
| **Release date** | 2026-07-16 | |

## Schema policy

v0.x is pre-freeze: the structural Types and the document-class registry may change additively. The schema freezes at the first cartridge that passes the stranger test (the shakedown). After freeze, changes to a structural Type's required sections or the stranger-test pass rule require a major bump with a migration note.

## Naming note

"Keel" is a working title carried from the design engagement, pending a vocabulary-audit decision. If renamed, the `keel_` namespace renames with it.

Original work by Jawn Lam. Built on Operating-Volume-Engineering (CC-BY 4.0).
