---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: keel-engine-02-elicitation
title: "Keel Engine — 02 Elicitation"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
doc_type: keel-engine
role: interview-protocol
scope: subject-agnostic
---

# 02 — ELICITATION (the product interview)

> **Run this before selecting or generating anything. One question at a time (F1) — never a numbered questionnaire. Each answer becomes a citable fact that downstream selection decisions rest on. Record answers in the cartridge's `_product-interview.md`.**

## The rule

Ask one question. Wait. Probe if the answer is thin. Reflect it back. Then ask the next. A bulk questionnaire gets bulk answers and produces a generic stack. If you catch yourself dumping a list, stop and reset: *"I'll take these one at a time."*

## What the interview must capture (minimum set)

Capture each, with enough specificity that a selection decision can cite it:

- **PI-1 · Product one-liner.** What is being built, in one sentence a stranger would understand.
- **PI-2 · Builder.** Human team / solo human / AI agent / mixed. *(This is the single most selection-shaping answer — it sets L3 precision per the canon §3.4.)*
- **PI-3 · Operator's relationship to the builder.** Same person / handoff to others / contractual. *(Shapes L0 and L6.)*
- **PI-4 · Interface surfaces.** UI? API? CLI? None (headless/batch)? *(Shapes L3 UX spec and interface contracts.)*
- **PI-5 · External parties.** Clients, regulators, vendors, end users. *(Shapes L0 alignment docs and L6.)*
- **PI-6 · Data sensitivity.** Any persistent data? Regulated data? *(Shapes L2 threat model, L3 data dictionary, and domain-stakes framing.)*
- **PI-7 · Component count and integration.** How many interacting components; any external integrations. *(Shapes L2 — the ≥3-components / persistent-data / external-integration trigger.)*
- **PI-8 · Runtime shape.** One-shot deliverable, or runs continuously / on a schedule? *(Shapes L5 runbook.)*
- **PI-9 · Expected lifespan and revision likelihood.** *(Shapes how frozen the interface contracts must be; feeds REVISE-STACK expectations.)*
- **PI-10 · Deadline landscape.** Hard dates, sequencing constraints. *(Shapes L6 plan/WBS.)*
- **PI-11 · Contested decisions already visible.** Any architecture or engineering choices already under debate? *(Shapes L2 ADRs.)*
- **PI-12 · Money / formal obligation crossing a boundary.** *(Shapes L6 SOW.)*

## How to run it

- Open with PI-1 and PI-2 — the one-liner and the builder. Everything else is easier once you know what it is and who builds it.
- Probe vague answers. "A web app" is not an answer to PI-4; "a React SPA plus a REST API, no public API for third parties" is.
- Do not invent answers the operator did not give. An unknown is an unknown — record it as an open question, do not fill it with a plausible default (that becomes a silent assumption, a named failure mode).
- When the minimum set is captured, reflect the whole picture back in three or four sentences and confirm before moving to selection.

## Output

Write `_product-interview.md` (Type `KEEL_Narrative_Document`, `keel_Doc_Class: product-interview`). Every PI answer is a labeled, citable line. The selection protocol (`03-SELECTION.md`) cites these labels by ID when it records why each document was included or excluded (OFR-4).

## Do not

- Do not begin selecting or drafting documents mid-interview. Elicit fully first (the constitutional behavior; `00-START-HERE.md`).
- Do not carry an answer over from a previous product's interview without re-asking. Stale-context carryover is in the failure catalog.
