---
type: Fleeting
timestamp: "2026-07-20T00:00:00Z"
Item_ID: baseplate-engine-06-close-out
title: "Baseplate Engine — 06 Close-Out"
Date_Added: 2026-07-20
Date_Modified: 2026-08-04
Needs_Processing: false
doc_type: baseplate-engine
role: close-out-packaging-protocol
scope: subject-agnostic
---

# 06 — CLOSE-OUT (package & handoff)

> **A stack that passed both gates is not done until it is packaged.** This procedure (OFR-16) transforms the working cartridge into a **self-contained handoff package** whose test is one sentence, from the operator who commissioned it: *"There is a folder called `<Product>` — read everything in it and build it for me."* A fresh AI pointed at the packaged folder must be able to construct the product with **zero input from outside the folder**, except the operator inputs the package itself registers (credentials, taste calls, authorizations — each with a when-to-ask trigger). **The NEW-STACK route is not complete until this procedure has run; a cartridge left in working layout after Gate 2 is a defect, not a finished engagement.**

## Precondition

Gate 2 verdict SHIP; every stack document at `baseplate_document_status: shipped` (`05-GATES.md`).

## The target layout (final shape, exactly this)

The packaged top level contains **only subfolders plus one file**:

```
<Product>/
├── AI-BOOTSTRAP.md      # the PRODUCT's bootstrap (not Baseplate's) — orientation, TOC,
│                        #   operator-input register, definition of done
├── Construction/        # the gated stack (the working `Artifacts/`, renamed at packaging)
│   └── MANIFEST.md      #   file inventory with checksums + gate verdicts
├── Records/             # non-essential history — interview, selection record, dependency
│   │                    #   log, design state, manifest, session logs, stranger-test log
│   └── README.md        #   declares: not needed for construction; immutable
└── Build/               # the builder's workspace, seeded at packaging
    ├── build-state.md   #   phase / done / next — updated every build session (P2)
    └── deviations.md    #   the standing reconciliation rule (see step 4)
```

## Procedure

### Step 1 — Restructure

1. Rename `Artifacts/` → `Construction/`.
2. Move into `Records/`: `_product-interview.md`, `_selection-record.md`, `_dependency-log.md`, `_design-state.md`, `_ov-manifest.md`, `stranger-test-log.md`, and the whole `Sessions/` folder.
3. Create `Build/` and seed its two files (step 4).
4. **Reference sweep on the new layout** (PF-7 / PF-13 at package level): grep `Construction/` for the old folder name (`Artifacts`) and for every Records filename — any hit inside a construction document is a defect. Construction documents may reference only `Construction/` contents. **Records files may reference anything *that resolves*** — records are history and may legitimately point outside the package (the engine, the portfolio catalog, prior sessions, source material), but they may not point at something that does not exist. **Sweep `Records/` for identifiers in this volume's own controlled namespaces — `PF-`, `OFR-`, `ONF-`, `BM-`, and the cartridge's own ID scheme — and confirm each resolves to a real entry** (this volume's `_portfolio/failure-catalog.md`, `_meta/TRACEABILITY.md`, or the stack itself). This is a manual grep and a lookup, not a new gate, and it is bounded to controlled identifiers — free-prose references to engine section numbers, external sources, or prior products remain unbounded. A `PF-`/`OFR-`/`ONF-`/`BM-` identifier is scoped to **this** operating volume; a number that exists only in another volume's catalog (or in a copy of this volume not yet reconciled) is a dangling reference here (PF-13).

### Step 2 — Manifest (`Construction/MANIFEST.md`)

A table: every construction document with its SHA-256 and byte size, plus the package date, the Gate 1 result, and the Gate 2 verdict + rerun count. The builder's first instruction (in the product bootstrap) is to verify this inventory — a partial copy, a sync-mangled file, or a tampered document must be detected **before** building, not during. On any later stack revision (REVISE-STACK), regenerate the manifest in the same change.

### Step 3 — The product `AI-BOOTSTRAP.md`

The single top-level file. Required sections, in order:

1. **What this folder is** — product one-liner; the folder's claim (complete founding documents, gates passed); what the reader is expected to do (build it).
2. **Verify first** — check `Construction/MANIFEST.md` against the folder before reading further; what to do on mismatch (stop, report to operator).
3. **Table of contents** — the three subfolders' roles, then every construction document with a one-line purpose and its precedence rank. State the reading order (dependency-layer order) and where conflicts resolve (the precedence declaration).
4. **Operator-input register** — a table of every input the builder will ever need from the operator: `item | why it is needed | when to ask (phase trigger)`. Credentials, domain names, authorization preferences, taste verdicts. This is the **complete** list — if the builder needs something from the operator that is not in this table, that is a package defect to report.
5. **Definition of done** — point at the acceptance test plan's gates; require a final acceptance report to the operator. "It compiles" is not done; the stack's verification layer is.
6. **Build workspace rules** — all built code and artifacts go in `Build/`; `Build/build-state.md` is updated every session (state lives in files, P2); construction and records files are never edited by the builder.
7. **Deviations rule** — restate the standing rule from step 4 below.
8. **Records notice** — `Records/` is history: not needed for construction, never to be modified.
9. **Provenance** — produced by Baseplate `<version>`, package date, stranger-test verdict and rerun count, drafting-model note.

P7 applies: the bootstrap names no human unless the operator explicitly provided a name for it.

### Step 4 — Seed the build workspace

- `Build/build-state.md`: phase `not-started`, empty done/next/decisions sections, and the instruction to update it every session.
- `Build/deviations.md`: the **standing reconciliation rule** — *when the builder finds a defect, contradiction, or genuine ambiguity in the construction documents, it does not silently patch around it; it records the finding here and surfaces it to the operator. These entries are the raw material for REVISE-STACK (`05-GATES.md` § Revision).*

### Step 5 — Packaging verification (both checks required)

1. **Mechanical:** every path the product bootstrap references exists; every manifest hash matches; the step-1.4 reference sweeps are clean for **both** `Construction/` (old-name / Records filenames) **and** `Records/` (controlled-identifier resolution); every figure restated across `Records/` — gate counts, stranger-test finding totals, rerun counts — agrees with its one owning record (cite, don't recopy; a divergent restatement is a defect, PF-9-genus in `Records/`); top level contains exactly the one file + subfolders.
2. **Orientation probe:** a fresh instance (no conversation history) receives only the packaged folder and is asked what it would do first. Pass = it verifies the manifest, correctly states what it is building, and identifies its first phase of work **using only the bootstrap and construction documents**. This is a cheap orientation check, not a rerun of Gate 2 — but any missing/unresolvable reference it hits is a packaging defect: fix and re-probe.

### Step 6 — Close the cartridge

Final write to `Records/_design-state.md` (phase: `packaged`; the packaging is the last entry) and a final session log in `Records/Sessions/`. Append any new failure mode to `_portfolio/failure-catalog.md` and trace it in `_meta/TRACEABILITY.md` in the same change (OFR-12/13). **Before assigning a new `PF-n`, read the last entry number in *this* volume's `_portfolio/failure-catalog.md` and assign the next integer after it. Catalog numbering is per operating volume: a `PF-` number seen in another volume's catalog — or in a copy of this volume not yet reconciled with the release catalog — is not this volume's next number. Never assign a number by assumption.** Then stop — the cartridge is closed.

## After packaging

- **The builder never needs Baseplate.** The packaged folder must stand alone — copy it anywhere, hand it to any agent; no reference back to the Baseplate volume, its engine, or its portfolio may exist inside `Construction/` or the product bootstrap.
- **REVISE-STACK on a packaged cartridge** edits `Construction/` in place (keep IDs stable), re-runs the affected gates, regenerates `MANIFEST.md`, appends — never rewrites — `Records/`, and updates the product bootstrap only if the operator-input register or TOC changed.
- **Balance/data tuning** that a stack explicitly externalizes (values its documents mark as data-file tunables) is builder work in `Build/`, not a stack revision — unless it changes a numbered rule's semantics, which routes to REVISE-STACK.

## Checklist (all boxes before the engagement is called done)

- [ ] Final layout exact: one file + `Construction/` + `Records/` + `Build/` at top level
- [ ] Reference sweep clean (no old-name or Records references inside `Construction/`)
- [ ] Records reference-resolution sweep clean (every `PF-`/`OFR-`/`ONF-`/`BM-`/cartridge-ID identifier in `Records/` resolves in **this** volume) and every figure restated across `Records/` agrees with its owning record (PF-13)
- [ ] `MANIFEST.md` written; hashes verified
- [ ] Product `AI-BOOTSTRAP.md` contains all nine required sections, including the complete operator-input register with when-to-ask triggers
- [ ] `Build/` seeded (build-state + deviations rule)
- [ ] Mechanical packaging check green
- [ ] Orientation probe passed with a fresh instance
- [ ] Final state write + session log; portfolio/traceability updated if anything new surfaced
