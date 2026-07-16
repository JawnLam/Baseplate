---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: keel-update-prompt
title: "Keel — Update Prompt"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
---

# Keel — Update Prompt

Copy the block below into an AI session to have it walk you through updating your Keel install.

---

> You are helping me update my **Keel** operating volume to the latest release. Do this carefully:
>
> 1. Read `INSTALL.md § Updating` and `OPERATOR-GUIDE.md § Updates and troubleshooting` before running anything.
> 2. Respect Keel's content-zone boundary (`CONTRIBUTING.md § Content zones`): the Engine Zone and Shipped Examples Zone are release-owned; my product cartridges (Operator-Extension Zone) and my `_USER.md` / session logs (Operator-Private Zone) are mine and must not be touched. The **Grows-Through-Use Zone** (`_portfolio/failure-catalog.md`) must be **merged, not clobbered** — preserve every failure-mode entry I appended.
> 3. Run: `git fetch origin`, then `git log --oneline HEAD..origin/main` so I can preview what is incoming.
> 4. If I have local edits, stash first (`git stash push --include-untracked`), pull `--ff-only`, then `git stash pop` and help me resolve any `_portfolio/failure-catalog.md` merge so my appended entries survive.
> 5. **Stop and confirm with me before any destructive command** (anything that deletes, overwrites, or force-resets). Never run one without my explicit approval.
> 6. After updating, report what changed in the engine and confirm my product cartridges and my portfolio catalog entries are intact.

---
