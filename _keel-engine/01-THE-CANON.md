---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: keel-engine-01-the-canon
title: "Keel Engine — 01 The Canon"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
doc_type: keel-engine
role: domain-canon
scope: subject-agnostic
---

# 01 — THE CANON (the seven-layer founding-document taxonomy)

> **Source-grounded (F13).** This chapter is Keel's canonical substrate — the seven-layer founding-document taxonomy, grounded in ISO/IEC/IEEE 29148 (requirements), IEEE 1016 (design descriptions), ISO/IEC 12207 & 15288 (lifecycle document sets), the V-model (specification level paired with verification level), Zachman (artifacts by interrogatives), Nygard's ADRs, and aerospace ICD/CDRL practice. Treat it as authoritative; apply the taxonomy to select and generate every stack.

## 1. The organizing rule

Every founding document answers exactly one lifecycle question. Assign each document to its question. When a document tries to answer two questions, split it; when two documents answer the same question, merge them. The seven questions, in dependency order:

| Layer | Question | Changes at the speed of |
|---|---|---|
| 0 | Why build it? | the market |
| 1 | What must it do? | the product |
| 2 | What shape is it? | the architecture |
| 3 | How exactly is it built? | the engineering |
| 4 | How do we know it works? | the requirements (never the implementation) |
| 5 | How does it run? | operations |
| 6 | Who owes what to whom? | the relationship |

Two structural consequences: (a) documents at different layers change at different speeds — binding them into one file couples their change rates and guarantees staleness; (b) **Layer 4 verifies Layer 1, never Layer 3** — acceptance criteria written against implementation cannot detect design errors.

## 2. The menu (document classes per layer)

- **L0:** product brief / one-pager (problem, audience, why-now); BRD (business objectives; enterprise/consulting contexts); PR/FAQ variant for market-facing products.
- **L1:** PRD (features, scope, explicit non-goals, success metrics — how-agnostic by design); functional spec (exhaustive observable behavior: states, inputs, outputs, errors, edge cases); SRS (the standardized union of functional + non-functional requirements, each numbered and testable); Gherkin-form acceptance criteria as the bridge to L4.
- **L2:** architecture document (components, boundaries, data flow, deployment topology — C4 or 4+1 views); ADRs (one short record per significant decision: context, options, choice, consequences); threat model where the product has a meaningful attack surface.
- **L3:** technical design doc / RFC per component (data models, algorithms, failure modes, alternatives rejected); interface contracts (API specs, schemas — the machine-checkable layer; the most frozen documents in the stack); data dictionary; UX design spec wherever a human-facing interface exists.
- **L4:** test plan; acceptance test plan (the definition of done); traceability matrix (requirement → design element → verification); for agent-built products, the **stranger test** (`05-GATES.md`).
- **L5:** runbook (setup, credentials, cadence, failure modes and responses, monitoring); SLO definitions where availability matters.
- **L6:** SOW (deliverables, milestones, acceptance, payment) when a contractual relationship exists; license and attribution always; WBS/plan when sequencing is nontrivial.

## 3. The selection protocol (the taxonomy is a menu, not a mandate)

Operationalized as the decision procedure in `03-SELECTION.md`. The rules, from the canon:

1. **Layers 1 and 4 are unconditional.** Every stack contains a requirements-bearing document with numbered IDs and non-goals, and a verification document mapping every requirement. No product shape exempts these.
2. **Layer 0 earns a standalone document** when anyone other than the operator must be convinced or aligned; for a solo operator it collapses into a section of the L1 document.
3. **Layer 2 earns standalone documents** when the system has ≥3 interacting components, any persistent data, or any external integration; below that, architecture collapses into the L3 design doc. ADRs are cheap — include whenever a decision was genuinely contested.
4. **Layer 3 scales with the builder:** agent-built products need the *most* L3 precision (the document is the only channel — no meetings); human teams can leave more to convention. Interface contracts become mandatory the moment two components (or two builders) meet. UX spec exists iff a human interface exists.
5. **Layer 5 earns a runbook** when the product runs continuously or on a schedule; one-shot deliverables collapse L5 into the L4 acceptance procedure.
6. **Layer 6:** SOW iff money or formal obligation crosses a boundary; license/attribution always; plan/WBS iff multiple workstreams must interleave.
7. **Record every inclusion and every exclusion with its triggering interview answer.** The exclusion record makes the stack auditable and the selection improvable.
8. **Merging is allowed; splitting the questions is not.** Small products may merge L0+L1 or L2+L3 into single files, provided each merged file keeps internally separated sections per layer question and the stack's precedence declaration still resolves conflicts.

## 4. Generation standards (enforced on every document — see `04-GENERATION-STANDARDS.md`)

- Numbered requirements with stable, namespaced IDs; requirements state *what*, never *how*.
- Explicit non-goals in every requirements-bearing document — scope is defined by both edges.
- Precedence declared across the stack (which document wins on conflict); within contracts, "the config/schema is law; code conforms."
- Every requirement maps to a named verification; every verification maps back (no orphans either direction).
- Assumptions and open questions are first-class sections with owners — never absorbed silently into prose.
- **Dependency verification:** every named tool, package, API, dataset, or service is confirmed to exist (and its access model confirmed) before it is written into a document; the check and date are logged. Fabricated dependencies are the single most common stack-poisoning failure.
- **Anti-staleness:** any fact imported from outside the interview (dates, versions, settings, prior-product assumptions) is marked with its source and verification date; carryover without re-verification is a named failure mode.
- Write for the stranger: zero conversation context; terms defined in-stack; no "as discussed."
- Gates over prose polish: when effort must be rationed, the verification layer wins.

## 5. Why the discipline is worth it (teach, don't just assert)

1. **Why big-design-up-front died for human teams:** the marginal cost of specifying the last stretch of ambiguity exceeded the cost of a conversation, and requirements drifted faster than documents — so iterative methods rationally displaced it.
2. **Why it revived for agent builders:** the agent's only channel *is* the document; conversations don't persist; regeneration from spec is nearly free — so specification completeness became cheap to exploit and expensive to skip. Consequence: the stranger test (once unaffordable — strangers were expensive) is now the natural ship gate, because a fresh model instance *is* a stranger at negligible cost.

These reversals justify the selection protocol's **builder-sensitivity** (§3.4): a stack's required precision is a function of *who builds*, not how large the product is.
