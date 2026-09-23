---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: baseplate-operator-guide
title: "Baseplate — Operator Guide"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
---

# Baseplate — Operator Guide

## Engine vs your work (the content zones)

`git pull` updates the engine without touching your work, because the two never share paths:

- **Engine / Shipped Examples** (release-owned): `_baseplate-engine/`, `_types/`, the front-door docs, `Example-Product-*/`. Don't hand-edit — customize via your own cartridges.
- **Your product cartridges** (`<Product-Name>/`): yours; the OV is designed to be extended here.
- **Your private work** (`_USER.md`, `<Product>/Sessions/`, `<Product>/_design-state.md`): gitignored, never tracked.
- **The portfolio catalog** (`_portfolio/failure-catalog.md`, Grows-Through-Use Zone): ships seeded; your appended entries are preserved on update via stash/merge — never clobbered.

See `CONTRIBUTING.md § Content zones` for the full path patterns.

## Running a stack (symptom → what to do)

| Symptom | What to do |
|---------|-----------|
| The AI starts writing documents from your one-line description | Stop it. That violates elicit-before-generate. Say "run the interview first." A correct session opens with the product interview. |
| A document names a tool/API you're not sure exists | Good — that's the dependency-verification gate. Make the AI verify it (and log the check) or flag it `[UNVERIFIED]`. |
| The consistency audit finds an orphan requirement | Add the missing verification (or requirement), or delete the orphan with a reason. Re-run the audit. |
| The stranger test asks a question the stack should have answered | Ship block. Fix the stack to answer it, then rerun with a *fresh* instance (never the drafting session). |
| A shipped stack met reality and is now wrong | Use REVISE-STACK (`05-GATES.md § Revision`), not ad-hoc edits. Record what reality contradicted — it feeds the portfolio catalog. |
| A roadmap milestone is ready to act on *(v1.4.0)* | Bring the packaged folder back and say so — MILESTONE-STACK (`07-EVOLUTION.md`) runs the re-entry gate, a scoped interview, and the gates before any milestone document is drafted. "Go ahead and write the milestone PRD" gets the gate first, on purpose. |
| The roadmap itself needs changing *(v1.4.0)* | ROADMAP-UPDATE (`07-EVOLUTION.md`): every add/amend/retire carries rationale + date; entries are never silently deleted. |

## Updates and troubleshooting

The update workflow (`INSTALL.md § Updating`) is fetch → preview → ff-only pull, with stash/pop when you have local edits. **The one conflict you will hit repeatedly** is `_portfolio/failure-catalog.md`: the release adds seed entries; you added your own. On `git stash pop` (or the pull), resolve by **keeping both** — your appended `PF-n` entries plus any new seed entries. Never take "theirs" wholesale; that drops your accumulated lessons (which are the point).

If a fast-forward fails, you have local commits on the engine — move your customizations into your own cartridge instead, reset the engine files to `origin/main`, and re-pull.
