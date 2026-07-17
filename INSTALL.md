---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: baseplate-install
title: "Baseplate — Install"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
---

# Install Baseplate

## 1. Clone into a version-named folder

```bash
git clone https://github.com/JawnLam/Baseplate.git ~/path/to/Baseplate-v1.0
cd ~/path/to/Baseplate-v1.0
# Disable push so you never accidentally upload your product cartridges
git remote set-url --push origin DISABLED_TO_PREVENT_ACCIDENTAL_PUSH_OF_PERSONAL_WORK
git remote -v   # fetch = real URL; push = DISABLED_...
```

The folder is named `Baseplate-v<major>.<minor>`. On a major.minor release, rename it to match (`mv Baseplate-v1.0 Baseplate-v1.1`); `CHANGELOG.md` announces the transition.

## 2. Point an AI at it

Open the folder in any AI environment that reads local markdown (Claude Code, Cursor, Claude Desktop, ChatGPT/Gemini with file attachments, Obsidian + an AI plugin). Say:

> **"Read `AI-BOOTSTRAP.md` and help me design the founding-document stack for my product."**

## Updating

```bash
cd ~/path/to/Baseplate-v<major>.<minor>
git fetch origin
git log --oneline HEAD..origin/main          # preview incoming

# No local engine edits: clean fast-forward
git pull --ff-only origin main

# Local edits or appended portfolio entries: stash → pull → pop
git stash push --include-untracked -m "pre-update state"
git pull --ff-only origin main
git stash pop                                 # resolve any _portfolio/failure-catalog.md merge — KEEP your appended entries
```

Your product cartridges (Operator-Extension Zone), `_USER.md`/session logs (Operator-Private Zone), and appended `_portfolio/` entries (Grows-Through-Use Zone) survive updates. See `OPERATOR-GUIDE.md § Updates and troubleshooting` and `CONTRIBUTING.md § Content zones`.

## Requirements

Any capable AI (Claude / GPT-4-class+ / Gemini 2.x+). No runtime dependencies — Baseplate ships no validator by design; both gates run manually (`_baseplate-engine/_meta/VALIDATION-CHECKLIST.md`).
