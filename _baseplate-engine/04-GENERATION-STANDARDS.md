---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: baseplate-engine-04-generation-standards
title: "Baseplate Engine — 04 Generation Standards"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
doc_type: baseplate-engine
role: generation-standards
scope: subject-agnostic
---

# 04 — GENERATION STANDARDS (enforced on every document, every stack)

> **Generate each selected document from its structural Type and required sections (`03-SELECTION.md` registry). These standards are not style advice — they are gate criteria. The consistency audit (`05-GATES.md`) checks them mechanically; a document that violates them does not ship.**

## The standards

1. **Numbered requirements with stable, namespaced IDs.** ID scheme = product slug + type + number (`ACME-FR-7`, `ACME-NFR-3`). IDs never get reused or renumbered — downstream verifications reference them. Requirements state *what*, never *how* (a requirement that names an implementation belongs in L3, not L1).
2. **Explicit non-goals** in every requirements-bearing document. Scope is defined by both edges; a scope with only an inside is not bounded.
3. **Precedence declared** across the stack (which document wins on conflict), and within contracts: *"the config/schema is law; code conforms."* The precedence declaration is an extractable field, set at selection (S-3).
4. **Requirement ↔ verification bijection.** Every requirement maps to a named verification; every verification maps back to a requirement. No orphans in either direction. The map lives in the stack's traceability document (D-7).
5. **Assumptions and open questions are first-class sections with owners.** Never absorb an unknown into confident prose. If the interview did not settle it, it is an open question with an owner, visible in the document.
6. **Dependency verification (F2/F13).** Before writing any named tool, package, API, dataset, or service into a document, confirm it exists and confirm its access model. Log the check in `_dependency-log.md`: `dependency | exists? | access model | method of check | date`. A dependency you cannot verify is flagged `[UNVERIFIED]` and routed to the operator — never written as fact. **Fabricated dependencies are the single most common stack-poisoning failure.**
7. **Anti-staleness.** Any fact imported from outside this product's interview — a date, a version number, a config value, an assumption carried from a previous product — is marked with its source and verification date at the point of use (e.g., `(verified 2026-07-16 against npm registry)`). Carryover without re-verification is a named failure mode.
8. **Write for the stranger.** Zero conversation context. Define terms at first use or in a glossary in the stack. No "as we discussed," no "the usual setup," no reference to anything not in the `Artifacts/` folder.
9. **Gates over prose polish.** When effort must be rationed, spend it on the verification layer (requirement IDs, the traceability map, the acceptance criteria), not on nicer wording. This is the Displacement-durable core; commodity prose is not.
10. **Behavioral completeness — define every equivalence class (PF-6).** For every behavior that parses, matches, counts, deduplicates, or compares, ask: *would two competent builders resolve this the same way?* If not, the edge case is an implicit decision that must be made explicit in the document — the resolution order, the case-sensitivity rule, what's in vs out of scope, the counting/dedup equivalence, the exact output shape. A behavior stated as *what* but silent on *how it resolves the ambiguous case* passes the consistency audit (the doc is internally consistent) and fails the stranger test (as `stack-should-answer` questions). This is PF-6, and it was the entire reason the Linkrot shakedown took three stranger-test runs to converge. Front-run it here so the stranger test is a confirmation, not a discovery.
11. **Item_ID is a UUID, never a slug (vault conformance).** Every generated document's `Item_ID` frontmatter value MUST be an uppercase 8-4-4-4-12 UUID (Drafts-style, e.g. `3F2A9C41-7B0E-4D6A-9E11-2C8F5A0B7D34`), matching the target vault's `Master_Schema`. Slugs (e.g. `acme-prd`) are not permitted in shipped artifacts; generate a fresh UUID per document and never reuse one. The `_types/` templates carry an `Item_ID: "<UUID>"` placeholder to enforce this at instantiation.

## Per-document sequence

For each selected document, in dependency-layer order:

1. Instantiate its structural Type's required sections — the source of truth is the `03-SELECTION.md` class registry ("Required sections" column) plus the Type definition in `_types/`. (There is no separate `_templates/` directory; the registry + Type files *are* the templates.)
2. Fill numbered requirements / decisions / behaviors with stable IDs.
3. Verify every named dependency as you write it; log each.
4. Add the non-goals and the assumptions/open-questions sections.
5. Run the document's **internal consistency pass**: every ID unique; every cross-reference inside the document resolves; no `[TBD]` outside the open-questions section.
6. Advance the document's `baseplate_document_status` to `internally-consistent`.

Only when every selected document is `internally-consistent` do you run the stack-level consistency audit (`05-GATES.md`).

## Do not

- Do not write a requirement that says *how*. That couples L1 to L3 and defeats the point of separating them.
- Do not silently pick a default for an unknown. Make it an open question with an owner.
- Do not name a dependency you have not checked this session, even if you "know" it exists — verification is per-stack and dated (anti-staleness).
