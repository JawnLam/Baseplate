---
type: KEEL_Narrative_Document
Item_ID: linkrot-product-interview
title: "Linkrot — Product Interview"
keel_Product_Slug: "LR"
keel_Doc_Class: product-interview
keel_Layer: 0
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
---

# Linkrot — Product Interview (PI-1..PI-12)

*Shakedown cartridge (E-2 calibration cruise). Answered as a realistic operator; each answer is citable by the selection record.*

- **PI-1 · One-liner.** A command-line tool that scans a directory of Markdown files and reports broken links — both internal (wikilinks and relative file paths) and external (HTTP/HTTPS URLs).
- **PI-2 · Builder.** An **AI coding agent**, building solely from these documents. *(Selection consequence: maximum L3 precision — the documents are the only build channel.)*
- **PI-3 · Operator↔builder relationship.** Same person / solo — the operator commissions and uses it; no handoff to a separate team. *(L0 collapses into the PRD.)*
- **PI-4 · Interface surfaces.** A CLI only: arguments + stdout report (and a non-zero exit code on findings). No GUI, no library API for third parties.
- **PI-5 · External parties.** None. Personal tool.
- **PI-6 · Data sensitivity.** Reads local Markdown (not stored or transmitted). To check external links it issues outbound HTTP requests, which reveal the checked URLs to their hosts. No persistent data store.
- **PI-7 · Components / integration.** One CLI process. One external integration: outbound HTTP for URL liveness. Fewer than three interacting components. *(L2 standalone architecture not earned; collapses into L3.)*
- **PI-8 · Runtime shape.** One-shot, run on demand. Not a service; nothing scheduled. *(L5 runbook not earned; collapses into the L4 acceptance procedure.)*
- **PI-9 · Lifespan / revision.** Ongoing personal use; revision likely (new link syntaxes, config options). *(Interface contract should be versioned.)*
- **PI-10 · Deadlines.** None hard. Single workstream. *(No WBS/plan earned.)*
- **PI-11 · Contested decisions already visible.** Whether to check **external** URLs at all — it adds network latency, rate-limit handling, and flakiness, versus scanning only internal links. *(An ADR is earned.)*
- **PI-12 · Money / formal obligation.** None crosses a boundary. *(No SOW earned.)*
