---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: baseplate-portfolio-failure-catalog
title: "Baseplate — Portfolio Failure Catalog"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
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

## Adding new entries

At each cartridge close-out (and whenever two consecutive stranger-test reruns are blocked by the same question class), add the new failure mode here in the same format: name (PF-n), trigger, why, fix, prevention. The catalog grows; every new cartridge loads it; the failure recurs less. This is the portfolio's compounding value.
