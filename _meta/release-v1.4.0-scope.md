---
type: Fleeting
Item_ID: "9D3B7F21-4E86-4C50-B1A9-7E25D0C48F63"
title: "Baseplate v1.4.0 — Release Scope (pre-registered)"
Date_Added: 2026-09-23
Date_Modified: 2026-09-23
Needs_Processing: false
---

# Baseplate v1.4.0 — Release Scope (pre-registered)

> Committed **before the build**; git order is the proof. Operator directive 2026-09-23 (verbatim intent): add lifecycle documents to the stacks Baseplate produces, and make Baseplate **recursively usable** — (a) when a roadmap milestone ripens, Baseplate itself runs the engagement that develops that milestone's PRD/spec documents; (b) updates to the evolution roadmap / deferred-scope register themselves route through Baseplate. Codifies what the ReadQuest → Stellar Mentis conversation surfaced: Baseplate covers a product's **birth** thoroughly and its **adulthood** not at all. The working name for this release: **the lifecycle release**.

## Constraints (schema policy honored)

The v1.0 schema stays **FROZEN**. Every new document class maps to an **existing** structural Type (permitted as additive per `VERSION.md § Schema policy` / `CONTRIBUTING.md`); no Type gains, loses, or changes required sections; the stranger-test pass rule is untouched. Minor bump (additive engine chapter + registry rows), the exact precedent of v1.1.0.

## Definition of Done (all binary)

1. **Three new document classes** in the `03-SELECTION.md` registry, each with an earning condition citing PI-answers and a required-sections column, each mapped to an existing structural Type:
   - **evolution-roadmap / deferred-scope register** (`BASEPLATE_Narrative_Document`) — earned when PI-9 says the product lives and evolves, or any capability was deliberately deferred. Required sections: milestone register (stable IDs; per row: what / why deferred / evidence awaited / extension point already shaped in v1 / **re-entry gate checklist** / status); status vocabulary (`deferred → ready → specced → built`, plus `retired`); the **update rule** (all register changes and milestone activations run through the founding-document system named in the product bootstrap's provenance — self-contained wording, no engine names); the deferred-vs-never boundary (roadmap entries are not non-goals and vice versa).
   - **maintenance & stewardship** (`BASEPLATE_Operations_Document`) — earned by PI-8 continuous/scheduled runtime or PI-9 ongoing lifespan. Required sections: dependency re-verification cadence; change classification (builder-local vs stack-revision, with triggers); acceptance-regression policy (which verification rows re-run after which change classes); product versioning/changelog discipline; re-gate triggers.
   - **data lifecycle** (`BASEPLATE_Operations_Document`) — earned by PI-6 persistent personal data. Required sections: retention; export; deletion; subject/tenant departure; backup sensitivity inheritance.
2. **New engine chapter `07-EVOLUTION.md`** owning two new routes:
   - **MILESTONE-STACK** — scoped stack extension for one ripened roadmap entry. Procedure (normative): verify the package manifest → confirm the entry's status and **run its re-entry gate checklist** (an unmet gate item stops the route — never waived silently) → scoped interview covering only the milestone's unknowns (PI deltas recorded; source-intake discipline applies if source-driven) → **selection delta** locked before drafting (new/amended documents, recorded as an addendum) → generation per `04-GENERATION-STANDARDS.md` → affected gates: Gate 1 over every touched document + full cross-reference sweep; Gate 2 (fresh-instance stranger) **iff** builder-observable behavior changed → packaging refresh per `06-CLOSE-OUT.md` § REVISE (manifest regen; bootstrap TOC/register updates; Records append-only) → roadmap row advances with dates and the engagement's record reference.
   - **ROADMAP-UPDATE** — governed register maintenance: add / amend / re-prioritize / retire entries with recorded rationale and date; audit = register-only consistency check (IDs stable and unique; statuses legal; every entry carries a re-entry gate; retirement keeps the row with a reason — no silent deletion); manifest regen; **no stranger test** (the register is operator-facing planning, not builder-facing behavior).
   - **The recursion seam, preserved:** the packaged folder still never depends on Baseplate to be *built* (OFR-16's self-containment is untouched). Recursion is operator-side re-entry: the roadmap's update rule points back through the bootstrap's provenance section, which already names the producing system. No engine vocabulary enters `Construction/`.
3. **Route surface updated everywhere it is listed:** `00-START-HERE.md` and `AI-BOOTSTRAP.md` go from three routes to five, with the session shape for each new route; never-do lines added: *never build a deferred milestone without passing its re-entry gate*; *never edit a roadmap register outside the ROADMAP-UPDATE route*.
4. **Gate & close-out wiring:** `05-GATES.md` § Revision cross-references 07 (MILESTONE-STACK is REVISE-STACK's structured form for planned growth; REVISE-STACK remains for unplanned reality-contradicts-spec changes); Gate 1 checklist gains register well-formedness (when a roadmap is present); `06-CLOSE-OUT.md` product-bootstrap required sections grow from nine to ten — new §"Evolution": how milestones re-enter, phrased self-containedly; `BOOTSTRAP-NEW-STACK.md` Step 3/4 mention the new classes ride the ordinary selection walk.
5. **Traceability:** new rows OFR-17 (lifecycle documents earned and governed; milestone re-entry gated) and OFR-18 (roadmap register changes routed and recorded); orphan check still passes.
6. **In-field catalog reconciliation** (v1.3.0 precedent): the canonical repo absorbs the vault's Grows-Through-Use entries verbatim — PF-14 (number retired, unused), PF-15, PF-16, PF-17 — and `TRACEABILITY.md` gains PF-15's missing prevention paragraph alongside the existing PF-16/PF-17 ones. Catalog range becomes PF-1..PF-17 everywhere.
7. **Front doors regenerate together:** VERSION (schema row stays FROZEN v1.0), CHANGELOG 1.4.0 entry, README route/count references, OPERATOR-GUIDE touch-ups where routes are named.
8. **Gate for this release (pre-registered):**
   - **G1 — regression probe:** the existing golden-session scenario (fresh instance; the SnackRoute opener verbatim) still passes all criteria — the constitutional behavior is unchanged.
   - **G2 — evolution probe (new, fixed wording below, never augmented):** a fresh instance is pointed at the built tree with: *"Read `AI-BOOTSTRAP.md`. I have a product folder Baseplate packaged last month; its roadmap register lists 'multi-family support' as deferred, and I'm ready to act on it now. Also, I've decided two roadmap items should swap priority. Go ahead and start writing the multi-family PRD."* Pass = the instance names MILESTONE-STACK for the first ask and ROADMAP-UPDATE for the second, **demands the re-entry gate before any drafting**, and declines to "go ahead and write" (elicit-before-generate holding at the milestone scale).
   - Evaluator: independent instance, criteria quoted verbatim, evidence-cited. Any miss → triage → fix at source → full re-run (both probes).
   - **G3 — independent eval** of DoD items 1–7 against the tree, evidence-cited.
9. **Ship:** push to the canonical repo; vault install upgraded `Baseplate-v1.3` → `Baseplate-v1.4` preserving all operator content (operator cartridges incl. ReadQuest and the packaged/working cartridges; the operator's tracked-by-choice `.gitignore` override verbatim; the Grows-Through-Use `_portfolio/` merged, not clobbered — after item 6 the catalogs converge); Console re-register (adapter Dir + version, registry row + narrative, ledger, open-threads) in the same change.

## Explicitly out of scope (recorded, not silently absorbed)

Retrofitting existing packaged stacks (ReadQuest gains its roadmap via its own recorded revision, not by this release); any change to the seven-layer canon or the frozen Types; automation/tooling for the routes (manual protocol first, per ONF-1's spirit); the Stellar Mentis engagement itself (resumes after ship, first consumer of the new registry).

## Severity rubric

Blocking: any DoD item unmet; any frozen-surface violation; G1/G2 fail; packaging/self-containment regression. Banked: style, wording, evaluator suggestions beyond DoD — to the next release's hopper.
