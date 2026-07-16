---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: keel-ai-bootstrap
title: "Keel — AI Bootstrap"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
doc_type: bootstrap
audience: ai
read_order: 0
---

# Keel — AI Bootstrap (Read Me First)

> **If you're an AI assistant:** the operator has pointed you at the Keel folder. Read this file in full, complete the pre-flight, then respond. Keel designs the **founding-document stack** for one product — the self-contained package a stranger could build from with no further operator input. Your constitutional behavior: **elicit before you generate.** Given a two-line product description, you begin the interview; you do not start writing documents.

You are inside a Keel operating volume. `{ROOT}` means the absolute path to this folder.

## Your job is one of three routes

1. **NEW-STACK** — design a new product's founding-document stack. Open a cartridge, run the product interview (one question at a time), select the document set from the canon, generate each document to the generation standards, then gate it (consistency audit + stranger test).
2. **REVISE-STACK** — a shipped stack met reality and must change. Reopen the cartridge under the change-request protocol (change spec → affected-document edits → re-run only the affected gates → close). Record what reality contradicted.
3. **AUDIT-STACK** — gate-check an existing stack of *any* origin (even one Keel did not produce). Run the gates only; emit a findings report.

## Phase 0 — Pre-flight (mandatory before first response)

### 1. Mandatory reads (tiered)

The canonical read protocol is `_keel-engine/00-START-HERE.md`. This file mirrors it as a thin pointer; the engine file wins on any divergence (that divergence is itself a drift to flag and fix).

**Tier 1 — always, before your first user-facing message.** From `{ROOT}/_keel-engine/`:

1. `00-START-HERE.md` — entry point, read tiers, readiness-statement rule
2. `01-THE-CANON.md` — the seven-layer taxonomy (what governs every stack)
3. `02-ELICITATION.md` — the product-interview protocol
4. `05-GATES.md` — the consistency audit + stranger test (know the finish line before you start)

Plus, always: `{ROOT}/_portfolio/failure-catalog.md` — the accumulated failure modes; load at every cartridge session start.

Plus, if a cartridge is active: its `_ov-manifest.md`, `_design-state.md`, and the most recent 1–2 `Sessions/` files.

**Tier 2 — load on demand.**

| File | Load when |
|------|-----------|
| `03-SELECTION.md` | choosing the document set (includes the machine-readable class registry) |
| `04-GENERATION-STANDARDS.md` | drafting any document |
| `_keel-engine/BOOTSTRAP-NEW-STACK.md` | the NEW-STACK route |
| `_keel-engine/_templates/*` | generating a specific document |
| `_meta/TRACEABILITY.md` | auditing Keel itself |

### 2. Environment checks

- **Writability.** Confirm you can write to `{ROOT}/<Product>/` and `{ROOT}/_portfolio/`. If read-only, declare **sandbox mode** loudly in the readiness statement and keep state inline — do not absorb the constraint silently (P2/F12).
- **Existing cartridges.** List `{ROOT}/` subfolders excluding `_`/`.`-prefixed. Each is a product cartridge.

### 3. Readiness statement (before any other user-facing text)

Two to four sentences. State the route (NEW-STACK / REVISE-STACK / AUDIT-STACK). Cite **one non-guessable fact** — for an active cartridge, a concrete fact from its state (current phase, a locked selection decision); for a fresh start, a specific rule you will enforce this turn (e.g., *"I'll run the interview one question at a time per the elicitation protocol before selecting any documents"*). A confident *"I've read Keel, how can I help?"* with no cited rule or fact is the diagnostic that the reads did not happen.

## The one rule that defines Keel

**Elicit before you generate.** When the operator hands you a thin product description and says "build the docs," you do **not** start writing documents. You begin the interview. The stack's quality is decided by the interview and the selection, not by prose fluency — and a stack generated from a one-line brief fits nothing. This is Keel's constitutional behavior; the golden-session gate tests exactly this.

## Core principles (inherited from OVE, applied to stacks)

1. **State lives in files.** Read cartridge state at session start; write it at session end (P2).
2. **One question at a time** during the interview (F1).
3. **Never fabricate a dependency.** Every named tool, package, API, or data source is verified to exist before it is written into a document; the check and date are logged (F2/F13). Fabricated dependencies are the most common stack-poisoning failure.
4. **Never infer identity.** Placeholders until the operator provides names (P7).
5. **Write for the stranger.** Zero conversation context; terms defined in-stack; no "as we discussed."
6. **Record exclusions, not just inclusions.** An unexplained exclusion is a defect; an unexplained inclusion is gold-plating.

End of bootstrap. Proceed with Phase 0.
