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
| Node.js 24 LTS "Krypton" (v24.18.0) | Runtime: server, engine, sim harness | nodejs.org/dist/index.json queried; v24.18.0 listed as current LTS | 2026-07-20 | verified |
| TypeScript 7.0.2 | Language for all components | `npm view typescript version` against registry.npmjs.org | 2026-07-20 | verified |
| Vite 8.1.5 | Client build tool / dev server | `npm view vite version` | 2026-07-20 | verified |
| ws 8.21.1 | WebSocket server library | `npm view ws version` | 2026-07-20 | verified |
| better-sqlite3 12.11.1 | SQLite persistence driver | `npm view better-sqlite3 version` | 2026-07-20 | verified |
| Preact 10.29.7 | Client UI library | `npm view preact version` | 2026-07-20 | verified |
| Vitest 4.1.10 | Test runner | `npm view vitest version` | 2026-07-20 | verified |
| Zod 4.4.3 | Runtime schema validation (protocol + balance data) | `npm view zod version` | 2026-07-20 | verified |
| Caddy 2.6.2 | TLS reverse proxy / static file server | `apt-cache policy caddy` on Ubuntu 24.04 (candidate 2.6.2-6ubuntu0.24.04.3); caddyserver.com unreachable through session proxy — apt archive used as the authoritative check | 2026-07-20 | verified (Ubuntu 24.04 package) |
| systemd | Process supervision on the VPS | Ships with Ubuntu 24.04 LTS (assumed VPS OS — see runbook assumption) | 2026-07-20 | verified (OS built-in), OS assumption open |
| nginx 1.24.0 | Fallback reverse proxy (only if Caddy rejected) | `apt-cache policy nginx` on Ubuntu 24.04 | 2026-07-20 | verified; NOT selected — noted as fallback only |
| Ubuntu 24.04 LTS | Assumed VPS operating system (runbook §0) | Session container runs Ubuntu 24.04 with live apt archive access (the Caddy/nginx checks above ran against it); Ubuntu 24.04 LTS is a current LTS release | 2026-07-20 | verified (distro exists); VPS-actual OS unconfirmed → runbook R-1 |
| ufw, cron, journalctl/systemctl | Firewall, scheduler, service tooling named in runbook | Ubuntu 24.04 base-system components (ship with the assumed OS) | 2026-07-20 | verified as OS built-ins, contingent on OS assumption |

*Inspirations, not dependencies (recorded for anti-staleness clarity):* *Android: Netrunner* (mechanics inspiration only — IP excluded per PI-5) and *Marvel SNAP* (pacing/feel reference only, explicitly a suggestion not an anchor per PI-4). Neither is built against; neither belongs in stack documents as a dependency.

**Rule for generation:** the technology stack (runtime, framework, libraries) is NOT yet chosen. Each named technology enters the technical design only after a verification entry lands here — registry/docs checked, version pinned, date logged.
