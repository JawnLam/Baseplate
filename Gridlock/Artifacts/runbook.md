---
Item_ID: gridlock-runbook
type: BASEPLATE_Operations_Document
title: "Gridlock — Runbook"
baseplate_Product_Slug: "GL"
baseplate_Doc_Class: runbook
baseplate_Layer: 5
baseplate_Document_Status: internally-consistent
baseplate_Precedence_Rank: 13
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
---

# Gridlock — Runbook

> **How Gridlock runs, for a stranger.** Audience: an AI agent or technical stranger operating the deployment. The product's owner is non-technical and is never required to execute anything here. This document wins on deployment facts. It absorbs deploy and rollback (no separate deployment document exists in this stack). Setup uses only dependencies verified in the stack's engineering documents (`technical-design.md` §1).

## 0. Deployment facts (the one-screen summary)

| Fact | Value |
|---|---|
| Host | A Hostinger VPS owned by the product's owner (existence confirmed by the owner 2026-07-20; plan/specs pending — Open question R-1) |
| OS (assumed) | Ubuntu 24.04 LTS — **verify on first login** (`lsb_release -a`); if different, adapt package steps and record the deviation in this file |
| App user / dir | `gridlock` / `/opt/gridlock` (releases in `/opt/gridlock/releases/<git-sha>`, `current` symlink) |
| Data dir | `/var/lib/gridlock` (SQLite `gridlock.db`, `logs/`, `data/` balance sets) |
| Services | `gridlock.service` (Node server, localhost:8080) · `caddy.service` (TLS, static client, proxy) |
| Health | `GET https://<domain>/healthz` → `200 {"ok":true,...}` |
| Cadence | Runs continuously; no scheduled jobs except backup (§5) |

## 1. First-time provisioning (once per VPS)

1. SSH as root. Verify OS (`lsb_release -a`). Create user: `adduser --system --group --home /opt/gridlock gridlock`.
2. Install Node 24 LTS: download the official `linux-x64` tarball from `nodejs.org/dist/latest-v24.x/`, verify its published SHA-256 (SHASUMS256.txt from the same directory), extract to `/opt/node`, symlink `node`/`npm` into `/usr/local/bin`. (Chosen over distribution packages: Ubuntu 24.04's archive carries an older Node line.)
3. Install Caddy: `apt install caddy` (Ubuntu 24.04 package, 2.6.x).
4. Create `/var/lib/gridlock/{logs,data}` owned by `gridlock`.
5. Firewall: allow 22, 80, 443 only (`ufw allow 22,80,443/tcp && ufw enable`). Port 8080 stays localhost-only (the server binds 127.0.0.1).
6. Place `Caddyfile` (§7) at `/etc/caddy/Caddyfile`; `systemctl reload caddy`.
7. Install `gridlock.service` (§7); `systemctl enable gridlock`.

## 2. Deploy (every release)

1. `mkdir /opt/gridlock/releases/<sha>` → copy the built release there (server bundle, client `dist/`, `package.json` with pinned versions).
2. `npm ci --omit=dev` inside the release dir.
3. Copy the release's `data/` balance files into `/var/lib/gridlock/data/` **only if** the balance set changed; the server validates the set on load and refuses invalid sets whole (behavior: `technical-design.md` §6).
4. Flip symlink: `ln -sfn /opt/gridlock/releases/<sha> /opt/gridlock/current`.
5. `systemctl restart gridlock`.
6. Verify §3. Keep the previous release directory (rollback target); prune to the last 3.

**Live-match note:** restarting voids live matches (crash policy, `technical-design.md` §6). Deploys during active playtests should wait for idle (check `/healthz` → `live_matches: 0`).

## 3. Health check (after every action; also the routine check)

1. `systemctl is-active gridlock caddy` → both `active`.
2. `curl -fsS https://<domain>/healthz` → `200` with `{"ok":true, "live_matches":N, "balance_set":"<name>", "uptime_s":...}`.
3. Client smoke: load the site in a browser, reach the HOME screen.
4. Logs on suspicion: `journalctl -u gridlock -n 200 --no-pager`.

## 4. Failure modes & responses

| Symptom | Diagnosis path | Response |
|---|---|---|
| Site unreachable | `systemctl status caddy`; `journalctl -u caddy -n 100` | Restart caddy; if TLS/cert errors, confirm domain DNS → VPS IP and ports 80/443 open (Caddy needs both for issuance) |
| Healthz down, Caddy fine | `journalctl -u gridlock -n 200` | Restart gridlock; if crash-looping, roll back (§6) |
| Server refuses to start: invalid balance data | Startup log lists violations (GL-RS-38 checks) | Restore the previous `data/` set from backup or the prior release; restart |
| Disk full / near full (`df -h`) | Match logs growth (`du -sh /var/lib/gridlock/logs`) | Compress old months (`gzip logs/<yyyy>/<mm>/*.jsonl` — replay tooling reads .gz); if still tight, escalate to owner for plan upgrade — do NOT delete logs (they are the balance dataset, `data-dictionary.md` §3) |
| SQLite corruption (startup integrity error) | Server log names the DB file | Stop service; restore latest §5 backup; restart; report data-loss window to owner |
| Match complaints: "abandoned" results | Expected after any restart/crash (voided matches) | No action beyond confirming the restart cause |
| Suspected replay/determinism bug | — | Pull the match's `.jsonl`, run `sim replay <file>` locally; file an engine defect with the divergent event index |

## 5. Backup (nightly, cron on the VPS)

`/etc/cron.d/gridlock-backup`: at 04:10 UTC daily, run as `gridlock`: `node server/main.js --backup <dir>` (the server's backup mode — an online SQLite backup via its own database driver, safe while the service runs), then `tar` of `data/` and the current month's logs into `/var/lib/gridlock/backups/<date>.tar.gz`; keep 14; copy off-box **only if** the owner later provides a second location (Open question R-3). Restore: stop service → extract over `/var/lib/gridlock` → start → §3.

## 6. Rollback

1. `ln -sfn /opt/gridlock/releases/<previous-sha> /opt/gridlock/current`
2. If the bad release changed the balance set: restore prior `data/` from the previous release dir or backup.
3. `systemctl restart gridlock` → §3.
DB schema changes (none exist in 1.x — `data-dictionary.md` is the schema of record) would require a written migration note here before any release that alters tables.

## 7. Service configurations (reference copies — the deployed files are the live truth)

**`/etc/systemd/system/gridlock.service`:** `[Unit]` After=network-online.target · `[Service]` User=gridlock, WorkingDirectory=/opt/gridlock/current, ExecStart=/usr/local/bin/node server/main.js, Environment=GRIDLOCK_DATA_DIR=/var/lib/gridlock PORT=8080 HOST=127.0.0.1 MAX_LIVE_MATCHES=200, Restart=on-failure, RestartSec=3 · `[Install]` WantedBy=multi-user.target. (`MAX_LIVE_MATCHES` backs the protocol's `SERVER_FULL` error — `interface-contracts.md` IC-A-1.)

**`/etc/caddy/Caddyfile`:**
```
<domain> {
    encode zstd gzip
    handle /ws        { reverse_proxy 127.0.0.1:8080 }
    handle /healthz   { reverse_proxy 127.0.0.1:8080 }
    handle            { root * /opt/gridlock/current/client-dist
                        try_files {path} /index.html
                        file_server }
}
```

## 8. Monitoring

Minimal by design (no external services — `architecture.md` §3): a 10-minute cron on the VPS curls `/healthz` and, on failure, restarts `gridlock` once and appends to `/var/lib/gridlock/health-incidents.log`. The operating agent reviews that file at each session. No paging exists; the prototype's availability promise is informal (SLOs deliberately excluded — `prd.md` §4 deferred list).

## 9. Assumptions & open questions

| # | Item | Owner | Status |
|---|---|---|---|
| R-1 | VPS plan/specs (RAM, disk, OS image) unknown (PRD A-1). Everything above fits a 1 GB-RAM entry VPS; confirm on first login and record here. | Owner (answer), agent (record) | **Open — blocks first provisioning, nothing else** |
| R-2 | A domain name pointed at the VPS is required for HTTPS certificate issuance (and PWA installability needs HTTPS). None is recorded yet. A subdomain of any owner-held domain suffices. | Owner | **Open — blocks first provisioning, nothing else** |
| R-3 | Off-box backup destination — none exists; nightly backups are on-box only until provided. Risk accepted for prototype. | Owner | Open, risk accepted |
