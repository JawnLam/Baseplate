---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: baseplate-readme
title: "Baseplate — README"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
---

# Baseplate

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Version](https://img.shields.io/badge/version-v1.2.0-blue.svg)](VERSION.md)

Baseplate turns the seven-layer founding-document canon into an enforced discipline: point an AI at this folder, describe a product, and it interviews you, selects exactly the documents that product earns, generates them to a verifiable standard, and blocks the ship until a **stranger** — a fresh model instance handed only the folder — can build from it.

## Quick start

> **"Read `AI-BOOTSTRAP.md` and help me design the founding-document stack for my product."**

The AI will start by interviewing you, one question at a time. It will *not* start writing documents from a one-line brief — eliciting before generating is the whole point.

## What this is

- An operating volume (OV) — a self-contained markdown corpus an AI loads to run a kind of work. Built on [Operating-Volume-Engineering](https://github.com/JawnLam/Operating-Volume-Engineering).
- One **cartridge per product**. A cartridge's terminal artifact is that product's founding-document **stack** — the only thing a builder needs.
- A set of **gates**: a selection protocol with recorded rationale, a consistency audit, and a stranger test. Plus a **portfolio failure catalog** that makes product #4's stack benefit from what shipping #1–3 taught you.

## What this is not

- Not a document-template pack. Templates are commodity scaffolding; the durable value is the gates and the accumulating failure catalog.
- Not a product builder. The stack is the terminal artifact; Baseplate does not build the product.
- Not opinionated about your product. The engine is subject-agnostic; the product is a parameter of the cartridge.

## Folder structure

| Path | Contents |
|------|----------|
| `AI-BOOTSTRAP.md` | AI entry point |
| `_baseplate-engine/` | The canon + protocols: `00-START-HERE`, `01-THE-CANON`, `02-ELICITATION`, `03-SELECTION`, `04-GENERATION-STANDARDS`, `05-GATES`, `BOOTSTRAP-NEW-STACK` |
| `_types/` | The eight structural document Types |
| `_portfolio/` | The cross-cartridge failure catalog (Grows-Through-Use Zone) |
| `_meta/` | Posture, traceability matrix, golden-session log |
| `<Product-Name>/` | Your product cartridges |

The four OVE content zones plus Baseplate's Grows-Through-Use Zone are declared in [`CONTRIBUTING.md`](CONTRIBUTING.md) § Content zones.

## System requirements

- Any capable AI that reads markdown and parses YAML frontmatter (Claude, GPT-4-class+, Gemini 2.x+).
- **No runtime dependencies.** The OV form is plain markdown; a human and an AI run it with zero code execution.

**Tooling posture.** Baseplate ships **no validator by design** — the two gates (consistency audit, stranger test) are runnable by a human with a text editor and a second chat window, and the stranger test *cannot* be mechanized (it needs a fresh model instance). The manual walkthrough is `_baseplate-engine/_meta/VALIDATION-CHECKLIST.md`. Inherited from OVE's manual-first, no-runtime-dependency doctrine.

## License

CC-BY 4.0. See [`LICENSE.md`](LICENSE.md).

> Built on **Operating-Volume-Engineering** by Jawn Lam — https://github.com/JawnLam/Operating-Volume-Engineering. Licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

## Version

See [`VERSION.md`](VERSION.md). This is **v1.2.0** (schema frozen; passes its own consistency audit; substrate-agnosticism shown by a cross-family golden session; the flywheel demonstrated by two shipped worked examples — the invented `Example-Product-Linkrot` and the real, production-recovered `Example-Product-Datestamper`; and, new in 1.1.0, the **close-out packaging protocol** `06-CLOSE-OUT.md`: every shipped stack is packaged into a self-contained handoff folder before the engagement counts as done). `CHANGELOG.md` is the authoritative release history.
