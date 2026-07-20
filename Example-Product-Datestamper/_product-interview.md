---
type: BASEPLATE_Narrative_Document
Item_ID: 6A4686FF-4642-4CAD-BBFE-05336A893F39
title: "Inbox-Datestamper — Product Interview"
baseplate_Product_Slug: "DS"
baseplate_Doc_Class: product-interview
baseplate_Layer: 0
Date_Added: 2026-07-19
Date_Modified: 2026-07-19
Needs_Processing: false
---

# Inbox-Datestamper — Product Interview (PI-1..PI-12)

*First real cartridge. Answers **recovered from the shipped implementation** (`datestamper.py` + `DATESTAMPER-PROCEDURE.md`), not invented. Each answer is citable by the selection record.*

- **PI-1 · One-liner.** A zero-AI-token cron bot that runs once a minute on a Markdown vault's `_InBox` and does two deterministic chores: (1) guarantees every top-level `.md` file has a non-empty uppercase-UUID `Item_ID` in its YAML frontmatter, and (2) renames every *new* top-level file (any type) with a creation-time prefix `YYMMDDHHmm - ` derived from filesystem birth time in Pacific time.

- **PI-2 · Builder.** **Mixed — a solo human maintainer with AI assistance — but the binding constraint is that it must be maintainable *cold*** (the runbook is written "so a future person or AI can maintain it cold"). *(Selection consequence: high L3 precision — frozen behavioral semantics — because the cold maintainer/rebuilder has only these documents; and L5 precision because the deployment is the thing handed over.)*

- **PI-3 · Operator↔builder relationship.** Same person / solo — the operator writes, runs, and maintains it; no separate team, no contractual handoff. The only "handoff" is temporal: a future cold maintainer (person or AI). *(L0 collapses into the PRD; the cold-handoff shapes L5.)*

- **PI-4 · Interface surfaces.** **A CLI only, invoked non-interactively by a cron wrapper.** Arguments: optional `--dry-run`, optional `--backfill`, optional positional `VAULT_ROOT`. No GUI, no library API, no inbound network interface. Headless/batch. *(No UX spec earned; no inbound attack surface.)*

- **PI-5 · External parties.** **None.** Personal vault infrastructure. The bot makes **no network calls at all** — purely local filesystem, time, and string logic. *(No L0 alignment docs, no L6 obligations.)*

- **PI-6 · Data sensitivity.** Operates on the operator's own personal notes. No regulated data, **no confidentiality/exfiltration surface** (nothing is transmitted). The real sensitivity is **data integrity**: the bot *mutates* real files (renames + frontmatter edits), so its safety properties (non-destructive Item_ID edits, mtime preservation, idempotency, link-safety, a runaway backstop, git-recoverability) are load-bearing requirements. Persistent state = a single flat `baseline.txt` kept *outside* the vault so it never syncs. *(No threat model earned — no external attacker; but data-integrity NFRs are central, and the birth-time caveat is a design concern.)*

- **PI-7 · Components / integration.** **One Python process** invoked by a small shell wrapper. It has no internal sub-components, but several **environmental couplings**: cron (scheduler), `flock` (overlap lock), a `sleep` offset to avoid racing the top-of-minute git sync, GNU `stat` (birth time), a `chown` sweep (tidies root-written files), and the vault's git sync (`vger-sync`) which commits the renames. *(< 3 interacting software components → standalone L2 architecture not earned; the runtime topology is operational content → it belongs in the L5 runbook, which *is* earned.)*

- **PI-8 · Runtime shape.** **Runs continuously — cron every minute, indefinitely.** Not a one-shot deliverable; a long-lived scheduled service. *(This is the decisive difference from Linkrot: **L5 runbook is earned** — setup, cadence, failure modes & responses, monitoring.)*

- **PI-9 · Lifespan / revision.** Indefinite personal use. Revision plausible but infrequent (timezone change, new exclusion patterns, a one-off `--backfill`). The observable contracts (the `YYMMDDHHmm - ` stamp format, the Item_ID normalization rules, the exclusion set) should stay stable — downstream state and the operator's muscle memory depend on them.

- **PI-10 · Deadlines.** None. Single workstream. *(No WBS/plan earned.)*

- **PI-11 · Contested decisions already visible.** **Two, both real and both discussed in the source:** (a) **birth time vs mtime** for the stamp — the operator wanted "creation time," but on this vault git and Obsidian Sync rewrite filesystem timestamps, so birth time really means "when the file landed on the server," with a stated caveat; (b) **baseline / "new files only" vs backfill** — on first run the bot records a baseline and stamps *nothing*, to avoid a surprise mass-rename and broken `[[wikilinks]]` on the existing backlog. *(Two ADRs earned.)*

- **PI-12 · Money / formal obligation.** None crosses a boundary. *(No SOW earned.)*

---

## Reflected picture (confirmed before selection)

A long-lived, zero-token, single-process cron bot that mutates real files in a personal vault inbox — so it is defined as much by its **safety properties and its runtime operation** as by its two chores. It has no network, no external parties, no GUI, and no persistent data *model* (just a flat baseline list). It earns a PRD (with the safety NFRs first-class), a precise technical design that **freezes every behavioral equivalence class** (because a cold maintainer has only the docs), **two ADRs** (birth-time, baseline-first), a **runbook** (it runs on a schedule forever), the unconditional L4 verification trio, and a license. It does **not** earn: standalone L2 architecture (one process), a threat model (no attacker surface), a data dictionary (no data model), a UX spec (no GUI), an SLO (personal tool), a SOW, or a WBS.
