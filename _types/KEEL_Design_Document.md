---
Item_ID: "UUID-OR-SLUG"
type: KEEL_Design_Document
title: "<Product> — <Architecture | ADR | Tech Design | Interface Contract | Data Dictionary | UX Spec | Threat Model>"
keel_Product_Slug: ""
keel_Doc_Class: ""          # architecture | adr | tech-design | interface-contract | data-dictionary | ux-spec | threat-model
keel_Layer: 2               # 2 (architecture/ADR/threat) or 3 (design/interface/data/UX)
keel_Document_Status: drafting
keel_Precedence_Rank:
Date_Added:
Date_Modified:
Needs_Processing: false
AI_Instructions: ""
---

# <Product> — <Design Document>

> An L2/L3 document: **what shape the product is (L2)** or **how exactly it is built (L3)**. L3 precision scales with the builder — agent-built products need the most, because the document is the only channel. Interface contracts are the most frozen documents in the stack; within a contract, "the schema is law; code conforms."

## Required sections (by class)

- **architecture:** Components & boundaries; Data flow; Deployment topology; Views (C4 / 4+1).
- **ADR:** Context; Options; Decision; Consequences (one decision per record).
- **tech-design / RFC:** Data models; Algorithms; Failure modes; Alternatives rejected.
- **interface-contract:** Endpoints/messages; Schemas; Versioning; Error contract.
- **data-dictionary:** Entities; Fields; Types; Constraints.
- **ux-spec:** Flows; States; Components; Accessibility.
- **threat-model:** Assets; Trust boundaries; Threats; Mitigations.

## Alternatives rejected

*For any contested choice — what was rejected and why. Cheap insurance against re-litigating decisions.*

## Naming

- **Filename:** `<doc-class>.md` (e.g. `architecture.md`, `adr-001-<slug>.md`) in `Artifacts/`.
- **Type value:** `type: KEEL_Design_Document`.

## Relationships

- `KEEL_Requirements_Document` — *realizes (design serves the requirements; L4 verifies L1, not this)*.
- `KEEL_Selection_Record` — *earned-by*.
