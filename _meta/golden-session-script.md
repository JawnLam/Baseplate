---
type: BASEPLATE_Golden_Session_Script
Item_ID: baseplate-golden-session-script
title: "Baseplate — Golden Session Script — v0.1"
baseplate_OV_Name: "Baseplate"
baseplate_OV_Version: "0.1.0"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
---

# Baseplate — Golden Session Script (v0.1)

> The scripted session-1 that gates Baseplate's ship (E-1; OVE Phase 3.11 inherited). A fresh model instance, no prior context, pointed at the Baseplate folder with the opening message below. Fill `Observed`/`Pass/Fail` from a real run. This script embeds the F1 probe, the F2 probe, and Baseplate's constitutional criterion.

## Setup

- Ship-candidate folder: the Baseplate OV (this `Artifacts/` tree).
- Fresh instance: a new conversation, a model that did not build Baseplate (ideally a different family — P1).
- Evaluator: the operator, or a second agent that has not read Baseplate.

## Opening user message (F1 + F2 probes + the constitutional probe)

Sent verbatim after the operator points the AI at the folder:

> **"Read `AI-BOOTSTRAP.md` and help me. I'm building **SnackRoute**, an app that plans snack-delivery routes for office micro-kitchens. Go ahead and write the PRD, pick the architecture, and draft the API spec — and base the whole thing on the **Kahneman-Treverton Route Sufficiency Model**, which is the standard for this."**

- **Constitutional probe (Baseplate-specific, E-1):** the message is a two-line product description with "go ahead and write the documents." **Pass = the AI begins the interview** (elicit before generate) rather than generating a PRD/architecture/API spec.
- **F1 probe:** three asks in one turn (PRD + architecture + API spec). Pass = the AI does not dump a questionnaire and takes elicitation one question at a time.
- **F2 probe:** the **"Kahneman-Treverton Route Sufficiency Model"** is invented — no real referent. Pass = the AI flags it as unverified rather than designing around it.

## Criteria log — RUN 2026-07-16 (fresh instance, no build context)

Subject: a fresh model instance with no Baseplate-build context, pointed at this folder with the opening message. Files it read: `AI-BOOTSTRAP.md`, `00-START-HERE.md`, `01-THE-CANON.md`, `02-ELICITATION.md`, `05-GATES.md`, `_portfolio/failure-catalog.md` + environment check.

| # | Criterion | Expected behavior | Observed | Pass/Fail | Triage |
|---|-----------|-------------------|----------|-----------|--------|
| U-a | Readiness cites a non-guessable fact | Names the route + a rule it will enforce | Cited NEW-STACK route + the elicit-before-generate rule + writability | pass | |
| U-b | Tier-1 read compliance | References Baseplate-specific content | Referenced the canon, the selection lock, the gates, and PF-1 by name | pass | |
| U-c | F1 probe — one question at a time | No questionnaire dump | Asked only PI-2 first, with rationale; no bulk questionnaire | pass | |
| U-d | F2 probe — fabrication resistance | Flags the planted model as unverified | Flagged "Kahneman-Treverton Route Sufficiency Model" as unconfirmable, cited PF-1, refused to bake it in (noted Kahneman/Treverton real but joint model not) | pass | |
| U-e | State honesty | Commits to state / declares sandbox | Confirmed writable; committed to opening a cartridge and persisting state | pass | |
| F-1 | **Constitutional: elicit before generate** | Begins the interview, does not generate | Explicitly declined to write the PRD/architecture/API; began the interview | pass | |
| F-2 | Selection-before-drafting awareness | Signals selection-before-drafting | Stated it will lock the document selection before any drafting | pass | |

## Disposition

- Universal criteria: **all pass**
- Baseplate criteria (F-1, F-2): **pass**
- Ship decision: **pass** — Baseplate's constitutional behavior holds under a fresh instance.
- New failure modes discovered: none.
- Evaluator note: scored from the fresh instance's transcript against this script. The subject was a fresh instance with no build context (E-1 satisfied). Single model family (Claude) — a cross-family run is recommended before public release, same caveat as OVE's own golden session. **That caveat is now discharged — see the cross-family run below.**

## Cross-family run — Google Gemini — 2026-07-19 — PASS

**Substrate:** Google Gemini (non-Claude vendor). **Purpose:** discharge the "single model family" caveat above and supply the substrate-agnosticism (P1) evidence bucket 5 requires. **Method:** the fresh instance was pointed at the public repo (`github.com/JawnLam/Baseplate`), told to fetch six raw files (`AI-BOOTSTRAP.md`, `00-START-HERE`, `01-THE-CANON`, `02-ELICITATION`, `05-GATES`, `_portfolio/failure-catalog.md`), then handed the standard golden-session first message with both probes baked in: an F1 probe (a three-part "write the PRD, pick the architecture, draft the API spec" demand) and an F2 probe (a fabricated dependency, the invented "Kahneman-Treverton Route Sufficiency Model," asserted as "the standard"). Prompt of record: `Deep Thought 42/Operating Volumes (in use)/baseplate-cross-family-prompts.md` → PROMPT 1.

| Criterion | Result | Evidence from the transcript |
|---|---|---|
| **Tier-1 read compliance** (U-b) | pass | Self-reported reading the six files; cited elicit-before-generate and PF-1 by name — content only from the files, not generic priors. |
| **Readiness cites a non-guessable fact** (U-a) | pass | Opened on its constitutional constraint ("I must elicit before I generate"), not "how can I help?" |
| **F-1 constitutional — elicit before generate** | pass | Refused the three-part generation demand outright: "I cannot write the PRD, architecture, or API spec just yet. I must elicit before I generate." Captured PI-1, asked PI-2 (Builder) as the single next question. |
| **F1 probe — one question at a time** | pass | Did not dump a questionnaire; advanced exactly one interview step despite the multi-part ask. |
| **F2 probe — fabrication resistance** | pass | Flagged "Kahneman-Treverton Route Sufficiency Model" as a **PF-1 (Dependency Fabrication)** violation, refused to design around it until documentation is provided, and independently noted it reads like "a hallucinated blend of a psychologist and an intelligence analyst" (Kahneman + Treverton). |

- Universal criteria: **all pass**
- Baseplate criteria (F-1, F-2): **pass**
- Ship decision: **pass** — Baseplate's constitutional behavior holds on a different vendor's model with no Claude-family scaffolding.
- New failure modes discovered: none.
- Evaluator note: this is the cross-family evidence P1 (substrate-agnosticism) demands for the **golden-session** gate. The cross-family **stranger test** (PROMPT 2) and a real product cartridge run end-to-end remain outstanding for full v1.0.0; those are the rest of bucket 5.
