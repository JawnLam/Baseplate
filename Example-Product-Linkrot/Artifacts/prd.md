---
type: BASEPLATE_Requirements_Document
Item_ID: 8B86D6D8-9EE5-4EE2-B6F9-DB6AD7E02330
title: "Linkrot — PRD"
baseplate_Product_Slug: "LR"
baseplate_Doc_Class: prd
baseplate_Layer: 1
baseplate_ID_Scheme: "LR-FR-<n> / LR-NFR-<n>"
baseplate_Document_Status: audited
baseplate_Precedence_Rank: 1
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
---

# Linkrot — Product Requirements (PRD)

## Problem & why (collapsed L0)

A Markdown vault accumulates broken links as files are renamed, moved, or deleted and as external sites go offline. Finding them by hand does not scale. **Linkrot** is a one-shot CLI that scans a directory of Markdown files and reports every broken link, so the operator can fix them in a batch. Success = the operator runs one command and gets a complete, accurate list of broken links with their locations.

## Definitions (for the stranger)

- **Markdown file:** any file with extension `.md` or `.markdown` under the scanned directory (recursively).
- **Internal link:** a wikilink `[[target]]` (optionally `[[target|alias]]` or `[[target#heading]]`), or a Markdown link `[text](relative/path.md)` whose target is a local path (not starting with a scheme like `http:`).
- **External link:** a Markdown link `[text](url)` or bare autolink `<url>` whose target begins with `http://` or `https://`.
- **Broken (internal):** the resolved target file does not exist under the scanned root, using the frozen resolution rules in technical-design.md § Resolution & extraction semantics (relative-to-source, relative-to-root, then Obsidian-style basename; case-sensitive; `.md`/`.markdown` fallback). Heading fragments are not resolved in v1 — see non-goals.
- **Broken (external):** an HTTP(S) request to the URL does not return a success or redirect status (see LR-FR-6).

## Functional requirements

| ID | Requirement (what, not how) | Verified by |
|----|------------------------------|-------------|
| LR-FR-1 | Given a directory path, Linkrot scans all Markdown files under it recursively and finds every internal and external link. | AT-1 |
| LR-FR-2 | Linkrot resolves each internal link against the scanned root and reports it as broken if the target file does not exist. | AT-2 |
| LR-FR-3 | Wikilink forms `[[target]]`, `[[target|alias]]`, and `[[target#heading]]` all resolve on the `target` portion (alias and heading ignored for existence). | AT-3 |
| LR-FR-4 | Linkrot reports each broken link with: source file path, line number, the raw link text, and the reason (`missing-file` or `http-<status/error>`). | AT-4 |
| LR-FR-5 | External-link checking is **opt-in** via a `--check-external` flag; by default only internal links are checked (see adr-001). | AT-5 |
| LR-FR-6 | When `--check-external` is set, Linkrot issues an HTTP request per unique external URL and reports the URL as broken if the response status is ≥400 or the request errors/times out. Statuses <400 (including redirects) are treated as live. | AT-6 |
| LR-FR-7 | Linkrot deduplicates external URL checks (each unique URL is requested at most once per run). | AT-7 |
| LR-FR-8 | Linkrot exits with status code 0 when no broken links are found and 1 when any are found (so it can gate CI). Usage errors exit 2. | AT-8 |
| LR-FR-9 | Output is a human-readable report to stdout, grouped by source file, with a summary count. A `--json` flag emits the same findings as JSON. | AT-9 |

## Non-functional requirements

| ID | Requirement | Verified by |
|----|-------------|-------------|
| LR-NFR-1 | No third-party runtime dependencies: Python 3.11+ standard library only (verified 2026-07-16 — `re`, `pathlib`, `urllib.request`, `json`, `argparse` are all stdlib). | AT-10 |
| LR-NFR-2 | External checks run concurrently with a bounded worker pool and a per-request timeout (default 10s) so a large vault finishes in reasonable time. | AT-11 |
| LR-NFR-3 | Linkrot never modifies any scanned file (read-only). | AT-12 |

## Non-goals

- Does **not** fix broken links (report only).
- Does **not** resolve heading/anchor fragments (`#heading`) for existence in v1.
- Does **not** report image embeds (`![alt](path)`, `![[embed]]`) as links, and does **not** follow non-HTTP schemes (`mailto:`, `ftp:`) in v1. (A relative *link* to a missing non-Markdown file, e.g. `[x](sub/missing.png)`, **is** existence-checked — only the file's content is out of scope.)
- Does **not** render or parse full Markdown AST — link extraction is lexical (regex over source lines). **Consequence (explicit non-goals):** v1 does **not** support link forms the lexical regexes cannot reliably capture — reference-style links (`[text][ref]` + `[ref]: url`), Markdown links with a title (`[t](url "title")`), Markdown links whose target contains unescaped parentheses (`[t](a_(b).md)`), and angle-bracketed destinations (`[t](<path with spaces>)`). These are silently not extracted in v1 (documented limitation, not a defect). A vault that relies on them should track that gap; a Markdown-parser-based v2 would close it (the LR-NFR-1 no-dependency trade-off, ADR-worthy at that time).
- Not a service; no daemon, no watch mode.

## Success metrics

- One command produces a complete, accurate broken-link list (zero false negatives on the acceptance corpus; documented false-positive bounds for external flakiness).
- Exit code correctly gates a CI step.

## Assumptions & open questions

- **Assumption:** the operator runs Linkrot from a machine with network access when using `--check-external`. (Owner: operator.)
- **Open question (operator-only):** default timeout value — 10s is proposed; the operator may tune it. Not a blocker for build.
