---
type: Fleeting
timestamp: "2026-07-19T00:00:00Z"
Item_ID: datestamper-dependency-log
title: "Inbox-Datestamper — Dependency Verification Log"
Date_Added: 2026-07-19
Date_Modified: 2026-07-19
Needs_Processing: false
---

# Inbox-Datestamper — Dependency Verification Log

> Every named external in the stack, verified before it was written (OFR-6 / generation standard 6 / PF-1). Method + date recorded. The bot makes **no network calls and names no third-party package**, so there is no remote API or PyPI dependency to fabricate; the externals are the interpreter, its standard library, and a handful of standard Linux system utilities.

| Dependency | Exists? | Access model | Method of check | Date |
|------------|---------|--------------|-----------------|------|
| Python **3.9+** standard library (`os`, `re`, `sys`, `uuid`, `subprocess`, `datetime`) | Yes | Bundled with the interpreter; no install | All named modules are standard library in every supported CPython; confirmed against the Python stdlib module index | 2026-07-19 |
| `zoneinfo` (stdlib) | Yes | Bundled from **Python 3.9+** | `zoneinfo` was added to the standard library in Python 3.9 (PEP 615); this sets the interpreter floor. Confirmed against the stdlib module index | 2026-07-19 |
| IANA time-zone database (`America/Los_Angeles`) | Yes | Read by `zoneinfo` from the OS tz database (`/usr/share/zoneinfo`) or the `tzdata` PyPI fallback | `America/Los_Angeles` is a standard IANA zone key present in every current tzdata release | 2026-07-19 |
| GNU coreutils `stat` with `-c %W` (birth time) | Yes (Linux target) | Invoked via `subprocess`; on PATH | `-c <format>` and the `%W` (birth-time-as-epoch) specifier are GNU coreutils `stat` features; `%W` returns `0` when the filesystem/kernel does not record birth time, which the bot handles by falling back to mtime. **Platform note:** this is GNU `stat`; BSD/macOS `stat` uses `-f %B` instead — the frozen semantics (birth time with mtime fallback) are portable, the exact invocation is not | 2026-07-19 |
| `cron` (scheduler) | Yes | System service; root crontab entry | Standard Unix scheduler; the every-minute `* * * * *` entry is standard cron syntax | 2026-07-19 |
| `flock` (util-linux; overlap lock) | Yes | Invoked in the cron wrapper; on PATH | `flock -n <lockfile>` is standard util-linux; used non-blocking so an overrun run is skipped rather than queued | 2026-07-19 |
| Git (recoverability) | Yes | The vault is a git repo; renames are committed by the separate `vger-sync` job | Not called by the bot; relied on only as the undo mechanism for renames (a design property, not a runtime call) | 2026-07-19 |

**No third-party Python packages are named anywhere in the stack.** The deliberate no-dependency posture (DS-NFR-1) is recorded in `technical-design.md § Alternatives rejected`. The only non-stdlib externals are standard OS utilities (`stat`, `cron`, `flock`), verified above; each is a documented OS facility, not a fabricable library.
