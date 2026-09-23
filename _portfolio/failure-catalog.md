---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: baseplate-portfolio-failure-catalog
title: "Baseplate — Portfolio Failure Catalog"
Date_Added: 2026-07-16
Date_Modified: 2026-09-23
Needs_Processing: false
doc_type: baseplate-portfolio
role: failure-catalog
scope: cross-cartridge
---

# Baseplate — Portfolio Failure Catalog

> **The compounding asset (the moat, REQ-M1).** Cross-cartridge failure/lesson log in the F-code idiom. Loaded at every cartridge session start; guard against every entry. Appended at each cartridge close-out with any new failure mode the engagement surfaced (OFR-12/13). This file lives in the **Grows-Through-Use Zone** (`_portfolio/`, D-5): the release ships the seed entries below; the operator's use grows the rest; `git pull` must merge-not-clobber (see `CONTRIBUTING.md` § Content zones).

Each entry: name, trigger, why it matters, fix, prevention. Seeded with the five failure modes named at design time (OFR-12).

## PF-1 — Dependency fabrication

**Trigger:** A document names a tool, package, API, dataset, or service that does not exist (or does not exist in the form described / with the assumed access model).

**Why it matters:** The single most common stack-poisoning failure. The builder — often an agent with no way to sanity-check — takes the dependency as real and builds against a phantom. Discovered late, expensively.

**Fix:** Remove or correct the dependency; re-verify the real one against its registry/docs; log the check + date.

**Prevention:** Generation standard 6 (`04-GENERATION-STANDARDS.md`): verify every named external before writing it; log in `_dependency-log.md`. Consistency audit checks the log is complete. F2/F13 inheritance.

## PF-2 — Stale-context carryover

**Trigger:** A setting, date, version, or fact is imported from a previous product's stack (or a previous season) without re-verification — because it "was true last time."

**Why it matters:** The bursty cadence (dormant between products) makes carryover tempting and staleness invisible. A wrong version pin or a stale assumption silently propagates into the new stack.

**Fix:** Re-verify the imported fact against its current source; mark it with source + verification date, or remove it.

**Prevention:** Generation standard 7 (anti-staleness): every imported fact carries its source + verification date at point of use. The elicitation protocol forbids carrying an interview answer over from a previous product without re-asking.

## PF-3 — Orphan requirements

**Trigger:** A requirement with no verification, or a verification with no requirement — the bijection breaks in one direction.

**Why it matters:** An unverified requirement can silently fail; a requirement-less verification tests something no one asked for. The V-model spine (L4 verifies L1) only works if the map is total.

**Fix:** Add the missing verification, or the missing requirement, or delete the orphan with a recorded reason.

**Prevention:** The traceability document (D-7) holds the bijection; the consistency audit (`05-GATES.md` Gate 1) checks no orphans in either direction.

## PF-4 — Gold-plating

**Trigger:** A document or requirement is included because it is customary, not because a product-shape signal earned it — it fails the "would the stranger need this?" test.

**Why it matters:** Unearned documents cost generation and maintenance effort, couple change-rates that should be independent, and dilute the stack the stranger must read. (Baseplate applies this to itself: this is why the Type set is structural + registry, not one-Type-per-class.)

**Fix:** Remove the unearned document/requirement, or record the interview answer that earns it.

**Prevention:** Selection protocol rule 7 (`03-SELECTION.md`): every inclusion cites a triggering PI-answer; an unexplained inclusion is gold-plating.

## PF-5 — Silent assumption absorption

**Trigger:** An unknown the interview did not settle is filled with a plausible default and written as confident prose, instead of being surfaced as an open question.

**Why it matters:** The absorbed assumption looks like a decision but no one made it. The stranger builds on it; if it is wrong, the whole downstream is wrong, and there is no record of where the error entered.

**Fix:** Convert the absorbed assumption back into an explicit open question with an owner; route it to the operator.

**Prevention:** Generation standard 5: assumptions and open questions are first-class sections with owners. The elicitation protocol records unknowns as open questions, never fills them.

## PF-6 — Under-specified behavioral semantics

*Added 2026-07-16 from the Linkrot shakedown (the first real cartridge). This is the portfolio catalog earning its first use-derived entry.*

**Trigger:** A requirements or design document states *what* a behavior does but leaves an implicit decision that changes the *result* — resolution rules (path vs basename, case sensitivity), what counts as in-scope (code blocks, embeds, non-target file types), external-protocol details that cause false positives (User-Agent, redirect following), or the exact shape of output on edge cases (empty result, JSON purity).

**Why it matters:** The stranger cannot build deterministically — two builders (or two runs) resolve the ambiguity differently and produce different products. These gaps pass the consistency audit (the documents are internally consistent) and surface only at the stranger test, as `stack-should-answer` questions. In the Linkrot shakedown, eight such classes blocked the first stranger-test run.

**Fix:** For each `stack-should-answer` question, freeze the behavior in the design document (a "Resolution & extraction semantics (frozen)" section is the pattern that worked), add an acceptance test, and keep the traceability bijection intact.

**Prevention:** During generation (`04-GENERATION-STANDARDS.md`), for every behavior ask "would two builders resolve this the same way?" — if not, it is an implicit decision that must be made explicit, not absorbed (PF-5's cousin at the behavioral level). The stranger test is the backstop that catches what generation missed; two consecutive reruns blocked by the same class escalate here.

## PF-7 — Cross-zone dangling reference

*Added 2026-07-19 from the Inbox-Datestamper cartridge (the first **real** product; second use-derived entry — the flywheel growing).*

**Trigger:** A document *inside* `Artifacts/` references a cartridge-level file the stranger never receives — the interview (`_product-interview.md`), the selection record (`_selection-record.md`), the dependency log (`_dependency-log.md`), or the stranger-test log (`stranger-test-log.md`). The reference resolves for the author (who sees the whole cartridge) but dangles for the stranger (who gets only the frozen `Artifacts/` folder, ONF-5).

**Why it matters:** It silently breaks the self-containment the stranger test depends on. Linkrot hit this at its **first stranger-test run** (its acceptance plan cited `stranger-test-log.md`, absent from the folder). It recurred in the datestamper draft — twice (an acceptance-plan cite of `stranger-test-log.md` and a traceability cite of `_dependency-log`). A recurrence across two cartridges of the same class is exactly the signal that promotes a finding to the catalog.

**Fix:** Remove the cross-zone reference or replace it with an in-`Artifacts/` source. State the fact the reader needs inline (e.g., "the stranger test is a separate gate recorded outside this folder") rather than pointing at the out-of-folder file. Trace requirements to in-folder design elements, not to cartridge-level logs.

**Prevention:** During the consistency audit (`05-GATES.md` Gate 1, "cross-references resolve within `Artifacts/`"), grep every `Artifacts/` document for references to the four cartridge-level filenames; any hit is a defect. Front-run it in generation: when citing a source from inside `Artifacts/`, confirm the target also lives in `Artifacts/`. Catching it at Gate 1 (as the datestamper did) rather than at the stranger test (as Linkrot did) is the win.

## PF-8 — Unparameterized acceptance metric

*Added 2026-07-20 from the Gridlock cartridge (third cartridge; first game). Surfaced by the Gate-2 evaluator as a non-blocking observation on a run that otherwise SHIPPED clean.*

**Trigger:** A success metric or acceptance criterion references a threshold ("within the band set in the balance data", "below the data-defined ceiling") that no schema, data file, or tunables registry actually names as an extractable value. The prose promises a number lives somewhere; nowhere does.

**Why it matters:** It passes the consistency audit (every cross-reference resolves; the sentence is internally coherent) and usually survives the stranger test (two builders still build the identical product — the gap is in the *acceptance readout*, not behavior). But at verification time the threshold gets invented ad hoc by whoever runs the test, making the acceptance result unreproducible and quietly negotiable — the exact failure the verification layer exists to prevent.

**Fix:** Name the threshold as a first-class key in the stack's data schema (in Gridlock: `balance_possession_score_band`, `balance_playbook_winrate_ceiling` added to the tunables appendix), marked as an acceptance input, not an engine input.

**Prevention:** During generation of any L4 document or success-metric section, for every comparative phrase ("within", "below", "at most", "no more than") ask: *does the number this compares against exist as an extractable field somewhere in the stack?* If it only exists as prose, parameterize it. Kin to PF-3 (the bijection breaks at the value level rather than the ID level) and PF-6 (an implicit decision hiding in confident prose).

## PF-9 — Cross-document enumeration drift

*Added 2026-07-20 from the Gridlock cartridge's close-out packaging probe (the first run of the 06-CLOSE-OUT orientation probe — the new gate earning its first catch).*

**Trigger:** A closed set (here: the six drive enders) is restated in full in multiple documents instead of being owned by one and cited by the rest. The copies drift — the PRD and an ADR listed four enders, the rules spec (correctly) listed them all, and an acceptance test invented a fifth count ("the five legal enders"). Same genus: a summary document (the packaging bootstrap) restating per-document metadata (precedence ranks) that then disagreed with the front-matter fields of record.

**Why it matters:** Each copy is locally plausible, so the drift passes per-document consistency passes, and Gate 1's cross-reference check only verifies that references *resolve*, not that restated *content* agrees. Precedence rules resolve the conflict formally, but a builder reading the lower-precedence copy first builds the wrong mental model — and a drifted count in a *test* description quietly weakens verification.

**Fix:** Give every closed set exactly one owning document; every other mention either cites the owner or names the set only ("the drive enders GL-RS-3 enumerates"), never re-lists it. For metadata (ranks, statuses), machine-check summaries against the fields of record.

**Prevention:** During generation, treat any full restatement of an enumerable set outside its owning document as a defect (cite, don't copy). During close-out packaging (`06-CLOSE-OUT.md` step 5), the mechanical check verifies bootstrap-restated metadata against front-matter fields. The orientation probe is the backstop that caught all of it here.

## PF-10 — Operational content pinned outside the stack

*Added 2026-07-21 from the PTIS cartridge (fifth cartridge; first production business system). Blocked stranger-test run 1; invisible to every mechanical Gate-1 check.*

**Trigger:** A construction document *cites or delegates to* operational content — query sets, endpoint URLs, portal/board identities, seed lists — that actually lives only outside the frozen stack (the cartridge-level dependency log, the source spec, "the operator's research"), while naming no out-of-folder **file**, so the PF-7 filename sweep passes clean.

**Why it matters:** The stranger cannot build the configured behavior: two builders invent different queries/URLs/targets and produce observably different products. It is PF-7's content-level cousin — the dependence is at the level of *facts*, not links — and it survives every cross-reference-resolution check because nothing dangles; the knowledge simply is not there. In PTIS, four instances blocked run 1 (the press-alert query set, the exact litigation query strings, the state→license-board mapping, the portal URL set) — all present in the cartridge's dependency log and source spec, none in `Artifacts/`.

**Fix:** Move the content itself into the stack — named tunables or appendix entries in the owning design document, with anti-staleness marks (and, for volatile URLs, an explicitly repair-editable posture) — then repoint the citing prose at the in-stack key.

**Prevention:** During generation, for every phrase of the form "the configured X," "per the source's Y," "from the operator's research": confirm the configuration/list itself resides in `Artifacts/`; if it exists only in cartridge logs or source material, inline it. At Gate 1, grep construction documents for delegation phrases ("configured", "the source's", "per the operator's") and resolve each to an in-folder owner. The stranger test remains the backstop that caught it first.

## PF-11 — Source-citation bleed in decomposition

*Added 2026-07-29 from the OmniLattice-Deal-Room cartridge (the first cartridge to **decompose a near-complete monolithic source PRD** into a stack, rather than author from an interview or recover a small tool). Caught at the Gate-1 reference sweep, not the stranger test.*

**Trigger:** When a stack is built by decomposing a large existing source document, that source's own internal apparatus — its section numbers (`§5.7`, `Appendix D`), its scattered pre-existing IDs (`SO-*`, `V-*`, `J2-*`), and its framework vocabulary — survives into the `Artifacts/` documents as references. They resolve for the author (who holds the whole source) but **dangle for the stranger, who receives only the frozen folder** and never sees the source. It is the decomposition-specific cousin of PF-7: the dangling target is the *source document* (or the authoring engine's vocabulary), not a cartridge-level file — so a filename sweep for the four cartridge files misses it.

**Why it matters:** These references pass a naïve consistency pass (they look like ordinary cross-refs) and only reveal themselves as `stack-should-answer` confusion (a builder told to "see §5.7" with no §5.7 in the folder) or as an engine-vocabulary leak that ties the packaged folder back to the authoring system it must stand apart from. In this cartridge, three slipped to Gate 1: a construction doc citing the cartridge-level stranger-test-log, one citing the dependency log, and three engine-vocabulary tokens (`PF-#`, `generation standard N`).

**Fix:** For every citation inherited from the source, resolve it to an in-`Artifacts/` target or inline the fact. **Re-anchor the source's section numbers as your own document's headings** (so `§5.x` resolves intra-doc), **re-cast the source's scattered IDs into the stack's single namespace** (here `DR-*`), and **strip all authoring-engine vocabulary** from the packaged documents.

**Prevention:** During generation of a decomposed stack, treat every inherited `§`/appendix/ID citation as suspect: either it resolves inside `Artifacts/` or it is inlined. At Gate 1, extend the reference sweep beyond the four cartridge filenames to also grep for (a) the authoring engine's vocabulary and (b) bare source-section citations (`§\d`, `Appendix [A-Z]`) that have no in-folder anchor. Catching it at Gate 1 (as here) rather than the stranger test is the win; front-running it in generation (re-anchoring section numbers as you write) is better still.

## PF-12 — Unsourced required input

*Added 2026-08-04 from the AcuityFlow-v1 and AcuityFlow-Potemkin-Demo cartridges (built back-to-back). **Nine of the fourteen stranger-test blocks across the two engagements were this one mode** (three of four in the first cartridge, six of ten in the second), in two structurally dissimilar products — a nine-component regulated clinical system and a two-component single-file facade. Invisible to every mechanical Gate-1 check: every blocked freeze passed the consistency audit cleanly.*

**Trigger:** The stack names a value, artefact, or choice as **required** — and may even parameterise it properly into the tunables registry — but never states **who supplies it, or whether the builder should source it, wait for it, or proceed without it.** The value is correctly registered as *needed*; its **provenance** is missing.

Six instances, all caught by the stranger and none by the audit:

- A datastore named only in a licence table, in a stack that gives decision records to truncation length — so its silence read as "decided somewhere," and it was not.
- An audit observation window described as a fixed, non-compressible clock with **no framework, no duration, and no registry row** — a duration, which the drafting session's own PF-8 sweep missed because PF-8 greps for *comparatives*.
- A synthetic-corpus statistical profile required to be "supplied," with no supplier named — on the critical path, so one builder sources it and ships while another waits for an operator input that was never coming.
- A monetary figure described as operator-supplied with a documented fallback, but with no tunable, no supplier, and no open-question row.
- Override-reason labels shared across two screens that must agree, authored nowhere.
- A roster the prose implied held nine items while only the count was authored.

**Why it matters:** PF-5 is a *silent* assumption absorbed into confident prose; PF-8 is a *threshold with no extractable value*. This is the third case and it hides between them — **the item is explicit and often correctly parameterised, so both existing sweeps pass it**, and the resulting ambiguity is not about the value but about the *act*: source it, or wait. Two competent builders resolve that differently, and the divergence is observable in the shipped product. It surfaces only when a reader tries to *use* the documents, which is exactly what the stranger test is and the audit is not.

**Fix:** Give the item a **named supplier**, and state the consequence of the answer not being available yet. The generalising fix that closed it in both cartridges was to redefine the tunables registry's `Kind` column as the answer to *"who supplies this, and do I wait for them?"* — `Product` (this stack decided it; do not ask), `Site`/`Operator` (**wait for them**; each carries an open question), `Builder` (**yours; do not wait**) — plus a standing rule that a required value with no named supplier is a defect the builder must raise. Where the supplier is the operator, add the open-question row and state the honest default the build proceeds on (counts-only, a visible placeholder, a documented fallback mode).

**Prevention:** During generation, sweep every required input — not just every comparative — and ask **"who supplies this?"** A tunables registry that records values without suppliers is half a registry. At Gate 1, extend the PF-8 sweep from comparative phrases to **durations, artefacts, and choices**, and check that every entry in the tunables registry names a supplier. Front-run it by treating the `Kind` column as load-bearing rather than descriptive. Kin to PF-8 (which parameterises the value but not its provenance) and to PF-4's inverse: **an unearned inclusion is gold-plating; an unsupplied requirement is an unbuildable one.**

## PF-13 — Records-zone defect invisible to Construction-scoped gates

*Added 2026-08-04 from the AcuityFlow-v1 and AcuityFlow-Potemkin-Demo cartridges. The finding behind this catalog's own reconciliation to per-volume numbering: this entry is `PF-13` in the **Baseplate** volume; a `PF-13` in another volume's catalog is unrelated. Distinct from PF-12 — PF-12 is a product defect the stranger test catches; this is an engine defect no close-out gate can see.*

**Trigger:** A `Records/` file carries an unresolvable reference, or a figure restated across several records that has drifted, and no close-out check inspects it — because every close-out gate (Gate 1, the packaging mechanical check, the reference sweep, and the orientation probe) is scoped to `Construction/` and the product bootstrap. Two instances, both in `Records/`, both survived every gate: (a) five records cited a portfolio entry `PF-14` that does not exist in this volume — the drafting assumed a `PF-13` that in fact belongs to a *different* operating volume's catalog, and the unbounded "records may reference anything" exemption *permitted* it rather than merely missing it; (b) a stranger-test finding total was restated across six records and diverged (a PF-9-genus enumeration drift, but in `Records/`, which the PF-9 guard does not reach).

**Why it matters:** It is not a build hazard — `Construction/` verifies clean and the builder never needs `Records/`. But "the record of the engagement contains a defect the gates structurally cannot see" is a distinct, recurring shape: a *scope* failure, not a novel symptom. Enforcement scoped to one zone leaves the other zones unchecked by construction, so a whole class of defect is *permitted*, not just undetected. The two symptoms were PF-7 (dangling reference) and PF-9 (enumeration drift) by genus — but neither guard reaches `Records/`, so both passed every gate clean.

**Fix:** Bound the records exemption — records may reference anything *that resolves*. Sweep `Records/` for identifiers in this volume's own controlled namespaces (`PF-`, `OFR-`, `ONF-`, `BM-`, and the cartridge's ID scheme) and confirm each resolves; check that figures restated across records agree with one owning record; read this volume's catalog's last entry number before assigning a new `PF-n` (numbering is per operating volume).

**Prevention:** `06-CLOSE-OUT.md` Step 1.4 (Records identifier-resolution sweep, bounded to controlled identifiers), Step 5.1 (records count-agreement), and Step 6 (per-volume catalog-number read). Generalising lesson for the engine itself: when adding any gate or sweep, name the *zone* it covers, and ask what the other zones now permit by its omission.

## PF-14 — number retired, unused

**Deliberately skipped.** This volume's records already carry the token `PF-14` with a prior life: on 2026-08-04, five AcuityFlow records cited "PF-14" as a portfolio entry that did not exist (the drafting mis-assumed the catalog's tail), and the recorded corrections in those files quote the token as *the error being corrected*. Assigning a real entry to the same number would make those corrections ambiguous — a sweep hitting "PF-14" could no longer tell a historical mention from a live reference. Numbering is append-only and cheap; the number is retired unused. (Consequence of the PF-13 per-volume-numbering rule; adjudication recorded in the AFPD cartridge's session-02/03 records.)

## PF-15 — Operator-intent inversion survives both gates

*Added 2026-08-05 from the AcuityFlow-Potemkin-Demo second revision — the volume's first operator rejection of a faithfully built artefact. Caught by neither gate; caught by the operator holding the build.*

**Trigger:** A stack specifies an interaction model (here: a linear thirteen-beat tap-through rail) that is internally consistent, fully buildable, and **wrong** — it inverts the operator's stated intent (*"look and function like an app"*). Both gates pass it, correctly by their own terms: the consistency audit verifies form; the stranger test verifies that a fresh builder can produce the specified artefact deterministically. The stranger *restates the wrong model fluently* — ST-1 measures fidelity to the documents, not to the operator's head. Two independent builds then converge on the same rejected artefact, proving the spec deterministic and the determinism mis-aimed.

**Why it matters:** This is the failure the gate stack is structurally blind to. PF-5/PF-6/PF-12 are all gaps *within* the documents that a use-attempting reader hits; this is a spec that contains no gap — every reader builds the same thing, and the thing is not what the operator meant. The cost profile is the worst in this catalog: the defect survives sealing, survives a commissioned build, and is discovered *by the principal audience* ("a slide deck with extra steps"), at the maximum-embarrassment moment and the maximum rework distance. In this engagement it cost a full 13-document redraft, four fresh stranger runs, and an operator who had to say it twice.

**Fix:** Separate the conflated axes and re-decide each on its own record (here: scripted *content* retained; linear *rails* replaced by free navigation), then re-gate in full. The amended decision record documents both axes explicitly so the conflation cannot silently reform.

**Prevention:** **For any stack whose deliverable is interaction-bearing, the operator validates a rendered reference — something they can drive — before the spec seals.** A cheap sketch at the right fidelity is sufficient; prose approval is not, because interaction intent lives below the level prose reaches ("thirteen scripted screens, driven by the viewer's own taps" described the rail and read as an app). The validated reference is cited in the deciding ADR as non-normative provenance; the documents stay self-contained. Kin to PF-5 (an absorbed assumption — but absorbed into the *interaction model*, where confident prose reads as decided), and the inverse of PF-6: there, silence makes two builders diverge; here, perfect specification makes them converge on the wrong thing. The stranger test cannot catch it by design — a stranger who questioned a coherent, buildable interaction model would be re-planning, which ST-2 forbids. Only the operator can, and only against something running.

## PF-16 — Source-intake integrity unverified

*Added 2026-08-09 from the Agentic-SMB-Valuation-Engine cartridge (a source-driven NEW-STACK run under heads-down operator authority). Caught at intake by a checksum comparison — the first entry in this catalog earned before the interview even began.*

**Trigger:** Operator-supplied source documents are accepted at face value — their count, identity, and content assumed to match the operator's announcement. In this engagement the operator announced two documents; byte-comparison found them **identical** (same 6,583 bytes, same MD5). The second document's intended content — its very title promised an architectural gap-analysis — never reached the engagement, and nothing but the checksum made that visible.

**Why it matters:** A source-driven engagement's entire derivation authority flows from its source material. A duplicated, truncated, or mis-exported source silently narrows scope, and **no downstream gate can notice**: every gate verifies the stack against itself and against the interview record — never against what the operator *meant* to supply. The missing content is unknowable by construction; only its absence is detectable, and only at intake. Had the duplicate gone unnoticed, the stack would have shipped gates-green against half the intended input, and the defect would have surfaced as operator rejection at the worst possible distance (PF-15's cost profile, arrived at by a different road).

**Fix:** Hash every supplied source at intake; compare against the announcement (count, names, sizes, hashes); echo the verified inventory back in the receipt/readiness statement; record any anomaly as an operator-owned open question; proceed only on the verified subset — never inventing the gap (PF-5 discipline applied to the intake boundary).

**Prevention:** Make source-intake verification a standing first step of any source-driven route: checksum all files, compare all pairs, state the inventory of record in the interview document's provenance section. A late-arriving true second document is a REVISE-STACK event, cleanly. Kin to PF-2 (staleness at the temporal boundary; this is integrity at the *intake* boundary) and PF-15 (both are defects invisible to gates that verify the stack rather than the operator's head).

## PF-17 — Fix-round coherence debt in frozen contracts

*Added 2026-08-09 from the Agentic-SMB-Valuation-Engine cartridge. Fired the catalog's escalation rule exactly: ship-gate runs 2 and 3 were blocked by the same question class — defects introduced by the fix rounds themselves.*

**Trigger:** A gate-driven fix round edits one frozen document — adds a mechanism, a field, a grammar form — and the neighboring documents that co-own the semantics are not re-derived. Each edit lands locally coherent and globally contradictory. In this engagement: an override flag was added with no durable record, making the standing validate-clean invariant unsatisfiable; a recompute mode collided with an acceptance test's evidence-preservation demand; a provenance grammar gained a third form while the next document still said "exactly two"; the new form then lacked a class-binding row and made every ingest fail on paper.

**Why it matters:** Fix rounds concentrate change precisely where the stack is most load-bearing — the frozen contracts — under exactly the conditions (speed, local focus, gate pressure) that produce cross-document drift. Per-document consistency passes and mechanical reference sweeps verify that citations *resolve*, not that co-owning documents still *agree on the semantics*; the contradiction lives between documents and surfaces only when the next fresh reader attempts to use them. The result is a convergence tax: each rerun pays for the previous rerun's repairs.

**Fix:** For each blocked finding, before editing: enumerate the documents that co-own the mechanism (schema, grammar, behavior, test — the ownership map in the selection record is the index); apply the change to the full seam in one pass; then rerun the gate with a fresh instance. Never let the fix round's own author judge its coherence.

**Prevention:** Treat every fix-round item as a miniature change-request — the revision protocol's affected-document discipline applied *inside* the pre-ship loop, not only after shipping. The enumeration-ownership map (PF-9's front-run) doubles as the seam index: any edit touching an owned set re-reads every citing document. The fresh-instance rerun remains the backstop that caught every instance here. Kin to PF-9 (drift between restatements) and PF-13 (defects between enforcement scopes): this is drift **between co-owning frozen documents, introduced under revision pressure**.

## Adding new entries

At each cartridge close-out (and whenever two consecutive stranger-test reruns are blocked by the same question class), add the new failure mode here in the same format: name (PF-n), trigger, why, fix, prevention. The catalog grows; every new cartridge loads it; the failure recurs less. This is the portfolio's compounding value.
