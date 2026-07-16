---
type: BASEPLATE_Verification_Document
Item_ID: linkrot-acceptance-test-plan
title: "Linkrot — Acceptance Test Plan"
baseplate_Product_Slug: "LR"
baseplate_Doc_Class: acceptance-test-plan
baseplate_Layer: 4
baseplate_Document_Status: audited
baseplate_Precedence_Rank: 4
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
---

# Linkrot — Acceptance Test Plan

> The definition of done. Each AT verifies one or more requirements. Absorbs the one-shot run procedure (no separate runbook; PI-8). Build a small fixture vault as described, then run each test.

## Fixture vault (`test-vault/`)

- `a.md` — contains a valid wikilink `[[b]]`, a broken wikilink `[[nope]]`, a valid relative link `[c](sub/c.md)`, a broken relative link `[x](sub/missing.md)`, an external link `[ok](https://example.com)`, and a broken external `[dead](https://example.com/definitely-404-xyz)`.
- `b.md`, `sub/c.md` — exist (targets of the valid links).

## Acceptance tests

| ID | Verifies | Procedure | Pass condition |
|----|----------|-----------|----------------|
| AT-1 | LR-FR-1 | `linkrot test-vault` | Scans `a.md`, `b.md`, `sub/c.md`; finds all links |
| AT-2 | LR-FR-2 | (same run) | Reports `[x](sub/missing.md)` as `missing-file`; does not report `[c](sub/c.md)` |
| AT-3 | LR-FR-3 | (same run) | Reports `[[nope]]` as `missing-file`; does not report `[[b]]` |
| AT-4 | LR-FR-4 | (same run) | Each finding shows source file, line number, raw link text, reason |
| AT-5 | LR-FR-5 | `linkrot test-vault` (no flag) | External links are NOT checked (no `http-*` reasons appear) |
| AT-6 | LR-FR-6 | `linkrot test-vault --check-external` | Reports `https://example.com/definitely-404-xyz` as `http-404` (re-run once if a transient network error occurs); does not report `https://example.com` |
| AT-7 | LR-FR-7 | run with `--check-external` on a vault repeating a URL 3× | The URL is requested at most once (verify via a local stub server or request log) |
| AT-8 | LR-FR-8 | run on a clean vault, then the fixture | Exit 0 on clean; exit 1 on the fixture; exit 2 on `linkrot /nonexistent-path` |
| AT-9 | LR-FR-9 | `linkrot test-vault --json` | Emits valid JSON matching the schema in technical-design.md; human format otherwise |
| AT-10 | LR-NFR-1 | inspect imports / run in a clean Python 3.11 venv with no pip installs | Runs with standard library only |
| AT-11 | LR-NFR-2 | `--check-external --timeout 1` against a slow/blackhole URL | Completes within a bounded time; slow URL reported `http-timeout` |
| AT-12 | LR-NFR-3 | checksum the fixture vault before/after a run | No scanned file is modified |

## Additional behavioral checks (frozen semantics, technical-design.md)

Extend the fixture vault and confirm:

- **AT-13 (basename resolution):** `[[c]]` (no path) in `a.md` resolves to `sub/c.md` (Obsidian-style basename), not reported broken.
- **AT-14 (case sensitivity):** `[[B]]` is reported broken even though `b.md` exists (case-sensitive matching).
- **AT-15 (code excluded):** a broken link inside a fenced code block and one inside an inline-code span are **not** reported.
- **AT-16 (embeds excluded):** `![[b]]` and `![alt](sub/c.md)` are not reported as links; a `[x](missing.png)` link **is** reported.
- **AT-17 (dot-dirs skipped):** a broken link inside `.obsidian/x.md` is not scanned.
- **AT-18 (clean output / json purity):** a clean vault prints exactly the zero-findings summary and exits 0; `--json` stdout parses as pure JSON with warnings only on stderr.
- **AT-19 (counting):** a file with the same broken link `[[nope]]` on 3 lines yields 3 findings (N counts occurrences); the summary's `M` counts distinct files-with-findings, not files scanned.
- **AT-20 (external dedup):** with `--check-external`, a dead URL appearing 4× (with a `#frag` variant) is requested once (fragment stripped for the equivalence class); each of the 4 occurrences is still reported as its own finding.
- **AT-21 (unsupported forms silently skipped):** a reference-style link `[t][ref]` and a titled link `[t](https://dead.example "title")` produce **no** finding (documented v1 non-goal) — confirming the lexical scope boundary is deliberate, not a crash.

## Definition of done

All AT-1..AT-18 pass. The stranger test (Gate 2, `_baseplate-engine/05-GATES.md`) is then the ship gate; its record is kept at the cartridge level (`../stranger-test-log.md`), outside this buildable folder.
