---
type: Fleeting
timestamp: "2026-07-20T00:00:00Z"
Item_ID: gridlock-dependency-log
title: "Gridlock — Dependency Log"
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
doc_type: baseplate-cartridge-dependency-log
---

# Gridlock — Dependency Log (OFR-6)

> Every named external (tool, package, API, dataset, service) in any stack document gets an entry here **before** it is written into the document: name, what it's used for, method-of-check, date. Fabricated dependencies are the most common stack-poisoning failure (PF-1). The consistency audit checks this log is complete.

| Dependency | Role | Method of verification | Date | Status |
|---|---|---|---|---|
| Hostinger VPS | Deployment target (PI-8) | Operator attestation — operator owns an active Hostinger VPS subscription; Hostinger VPS hosting is a well-known commercial service. Plan tier/specs NOT yet known — recorded as an open question for the runbook. | 2026-07-20 | verified (existence); specs pending |

*Inspirations, not dependencies (recorded for anti-staleness clarity):* *Android: Netrunner* (mechanics inspiration only — IP excluded per PI-5) and *Marvel SNAP* (pacing/feel reference only, explicitly a suggestion not an anchor per PI-4). Neither is built against; neither belongs in stack documents as a dependency.

**Rule for generation:** the technology stack (runtime, framework, libraries) is NOT yet chosen. Each named technology enters the technical design only after a verification entry lands here — registry/docs checked, version pinned, date logged.
