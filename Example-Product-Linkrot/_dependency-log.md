---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: linkrot-dependency-log
title: "Linkrot — Dependency Verification Log"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
---

# Linkrot — Dependency Verification Log

> Every named external in the stack, verified before it was written (OFR-6 / generation standard 6 / PF-1). Method + date recorded.

| Dependency | Exists? | Access model | Method of check | Date |
|------------|---------|--------------|-----------------|------|
| Python 3.11+ standard library (`re`, `pathlib`, `urllib.request`, `json`, `argparse`, `concurrent.futures`) | Yes | Bundled with the interpreter; no install | Known stdlib modules; confirmed all named modules are standard-library in Python 3.11 (no third-party package required) | 2026-07-16 |

**No third-party packages are named anywhere in the stack** (LR-NFR-1), so there is no external-package dependency to fabricate. This is the deliberate design choice recorded in technical-design.md § Alternatives rejected (no Markdown parser library).
