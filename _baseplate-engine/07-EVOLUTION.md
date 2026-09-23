---
type: Fleeting
timestamp: "2026-09-23T00:00:00Z"
Item_ID: baseplate-engine-07-evolution
title: "Baseplate Engine — 07 Evolution"
Date_Added: 2026-09-23
Date_Modified: 2026-09-23
Needs_Processing: false
doc_type: baseplate-engine
role: evolution-protocol
scope: subject-agnostic
---

# 07 — EVOLUTION (the lifecycle routes: MILESTONE-STACK and ROADMAP-UPDATE)

> **A stack's job does not end at handoff.** A product that lives accumulates deferred capabilities, and the roadmap register (`03-SELECTION.md` registry: evolution-roadmap class) is where they wait — each with the evidence it's waiting for and the gate it must pass to be built. This chapter owns the two routes that act on that register. Both are **recursive**: the same disciplines that produced the stack (interview, locked selection, generation standards, gates, packaging) produce its growth. Neither route weakens the packaged folder's self-containment — see § The seam.

## The two routes

- **MILESTONE-STACK** — one ripened register entry becomes stack documents. The heavyweight route: scoped interview, selection delta, generation, affected gates, repackaging.
- **ROADMAP-UPDATE** — the register itself changes (add, amend, re-prioritize, retire). The lightweight route: recorded rationale, register-only audit, repackaging of the one document.

Use **REVISE-STACK** (`05-GATES.md § Revision`), not these routes, when *reality contradicted the shipped spec* — a defect or a wrong decision. Evolution routes are for **planned growth**; revision is for **repair**. The tell: if the work item traces to a roadmap register row, it's evolution; if it traces to a `Build/deviations.md` entry or an operator rejection, it's revision. (A repair discovered *during* a milestone engagement is handled inside that engagement under the same affected-document discipline — PF-17.)

## The milestone register (what the routes act on)

Defined by the evolution-roadmap class (`03-SELECTION.md`). The load-bearing properties:

- **Stable IDs** in the product's namespace (e.g. `SM-MS-3`); never reused or renumbered.
- **Statuses:** `deferred` (waiting for its evidence) → `ready` (operator declares the evidence exists) → `specced` (its MILESTONE-STACK engagement shipped documents) → `built` (its acceptance rows are green in the product). Plus `retired` (will not be built — row kept, reason recorded). Status changes are ROADMAP-UPDATE acts, except `ready → specced` (set by a completed MILESTONE-STACK) and `specced → built` (set by the builder's acceptance report).
- **Re-entry gate:** every entry carries the checklist that must pass before its milestone is specced — the questions deferral deliberately left open (whose answers were the reason to wait). An entry with no re-entry gate is malformed (the audit catches it).
- **Deferred is not never:** non-goals belong in the requirements documents; register entries are intentions with conditions. An entry that becomes a true never migrates to `retired` here *and* a non-goal there, in one ROADMAP-UPDATE + affected-document pass.

## MILESTONE-STACK (the route)

**Input:** a packaged product folder + exactly one register entry at `ready`. **Output:** the same package, extended — new/amended construction documents, regenerated manifest, updated bootstrap, advanced register row, appended records.

1. **Verify the package.** Manifest check first, as ever (a mismatch stops the route — report, don't proceed).
2. **Run the entry's re-entry gate.** Every checklist item answered with evidence, in writing. An unmet item stops the route: the honest outcome is "not ready after all — back to `deferred` with what we learned," recorded via ROADMAP-UPDATE. **Never waived silently; an operator may waive an item only explicitly, and the waiver rides the record.**
3. **Scoped interview.** Only the milestone's unknowns — the PI questions whose answers change for this capability (typically a subset: surfaces, data sensitivity, external parties, runtime shape). One at a time; source-intake discipline (hashing, provenance stamps) applies if the operator supplies documents. Recorded as an interview addendum in `Records/` (append-only — the original interview is history, not a draft to edit).
4. **Selection delta, locked before drafting.** Which documents are new, which are amended, which gates each will face — recorded as a selection-record addendum citing the scoped interview's answers. The registry walk applies to *new* classes the milestone earns (a milestone can earn a document the v1 shape didn't — e.g. multi-tenancy earning a data-lifecycle doc).
5. **Generate.** Per `04-GENERATION-STANDARDS.md`, unchanged: verified dependencies, anti-staleness marks, stranger-safe prose, IDs continue the product's existing families (never renumber; new requirements take the next integers).
6. **Affected gates.** Gate 1 over every touched document **plus** the full cross-reference and bijection sweeps over the whole construction set (a milestone that breaks an untouched document's reference has still broken the stack). Gate 2 (fresh-instance stranger, canonical prompt, fresh instance) **iff builder-observable behavior changed** — which for any milestone worth the name it did; the exemption exists for documentation-shaped milestones, and the selection delta must claim it explicitly if used.
7. **Repackage.** Per `06-CLOSE-OUT.md § REVISE-STACK on a packaged cartridge`: manifest regenerated, bootstrap TOC and operator-input register updated, `Records/` appended (the milestone engagement's interview addendum, selection addendum, gate logs) — never rewritten.
8. **Advance the register.** The entry moves to `specced` with the date and a pointer to the engagement's records. When the builder's acceptance report later lands, `specced → built` with the report's date.

## ROADMAP-UPDATE (the route)

**Input:** the packaged folder + the operator's intent (add / amend / re-prioritize / retire / status-change entries). **Output:** the same package with one changed document and a regenerated manifest.

1. Verify the package (manifest check).
2. Apply the changes to the register document: every change carries **rationale and date** in the entry's own history line; retirement keeps the row (reason recorded); additions get the next stable ID and a complete row including the re-entry gate.
3. **Register-only audit:** IDs unique and never reused; statuses legal per the lifecycle above; every non-retired entry carries a re-entry gate; no entry contradicts a requirements document's non-goals (if one now does, the affected-document discipline fires — this stopped being a register-only change).
4. Repackage: manifest regen; bootstrap TOC only if the register's filename/position changed (rare).
5. No stranger test — the register is operator-facing planning. If step 3 found the change leaking into builder-facing documents, the work item was mis-routed: it is a MILESTONE-STACK or REVISE-STACK engagement, and this route stops.

## The seam (self-containment is not weakened)

The packaged folder still **never depends on Baseplate to be built** (`06-CLOSE-OUT.md § After packaging` stands unamended in force). Recursion is **operator-side re-entry**: the roadmap document's update rule — in self-contained wording — directs milestone activation and register changes to "the founding-document system named in this package's bootstrap provenance." The provenance section already names that system; nothing else in `Construction/` may. A builder who never heard of Baseplate loses nothing; an operator who returns the folder here gets the full discipline back.

## Failure modes this chapter exists to prevent

- **The wish-list rot:** a roadmap nobody governs becomes a junk drawer; entries without gates become someday-lies. (The register audit + the malformed-entry rule.)
- **The eager milestone:** building a deferred capability without the evidence deferral was waiting for — speculation with a head start. (Step 2; the never-do line in `00-START-HERE.md`.)
- **The drive-by register edit:** roadmap changed in a text editor, rationale nowhere, planning history gone. (ROADMAP-UPDATE's recorded-rationale rule; the never-do line.)
- **The milestone that quietly rewrites history:** records edited instead of appended, IDs renumbered, the original interview "cleaned up." (Steps 3/5/7's append-only and ID-continuity rules.)
