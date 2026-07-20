---
type: BASEPLATE_Design_Document
Item_ID: C6330152-80C5-45E0-B96B-936DA79C7412
title: "Linkrot — Technical Design & CLI Interface Contract"
baseplate_Product_Slug: "LR"
baseplate_Doc_Class: tech-design
baseplate_Layer: 3
baseplate_Document_Status: audited
baseplate_Precedence_Rank: 2
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
---

# Linkrot — Technical Design & CLI Interface Contract

> Realizes the PRD (prd.md). The interface contract is the frozen surface; the algorithm section is guidance the builder may optimize as long as the observable behavior in the PRD and this contract holds. Language: Python 3.11+, standard library only (LR-NFR-1).

## CLI interface contract (frozen)

```
linkrot <directory> [--check-external] [--json] [--timeout <seconds>] [--workers <n>]
```

- `<directory>` (required): root to scan recursively for `*.md` and `*.markdown`.
- `--check-external`: also check external HTTP(S) URLs (default: off — internal only). *(LR-FR-5)*
- `--json`: emit findings as JSON instead of the human report. *(LR-FR-9)*
- `--timeout <seconds>`: per-request timeout for external checks (default 10). *(LR-NFR-2)*
- `--workers <n>`: max concurrent external requests (default 8). *(LR-NFR-2)*

**Exit codes** *(LR-FR-8):* `0` = no broken links; `1` = one or more broken links found; `2` = usage error (bad path, bad flag).

**Human report format** (stdout), grouped by source file:

```
<source-file-relative-path>
  L<line>: <raw link text>  ->  <reason>
...
Summary: <N> broken link(s) in <M> file(s) (<K> external checked).
```

**JSON format** (`--json`): `{"findings": [{"file": "...", "line": 12, "link": "[[missing]]", "reason": "missing-file"}], "summary": {"broken": N, "files": M, "external_checked": K}}`.

`reason` is `missing-file` (internal) or `http-<status>` / `http-timeout` / `http-error` (external).

## Algorithm

1. **Discover** files: walk `<directory>` with `pathlib`, collect `*.md`/`*.markdown`.
2. **Extract** links per file, per line (lexical; LR-FR-1/3). Regexes:
   - Wikilink: `\[\[([^\]|#]+)(?:[#|][^\]]*)?\]\]` → capture group 1 = target.
   - Markdown link: `\[[^\]]*\]\(([^)]+)\)` → capture group 1 = target (URL or path).
   - Autolink: `<((?:https?://)[^>]+)>`.
   Record `(source_file, line_number, raw_text, target)`.
3. **Classify** each target: external if it matches `^https?://`; else internal.
4. **Resolve internal** (LR-FR-2/3): strip `|alias` and `#heading`; resolve the target relative to the source file's directory *and* to the scanned root (accept either). If a wikilink target has no extension, try `<target>.md`. Broken if no candidate path exists.
5. **Check external** (only with `--check-external`, LR-FR-6/7): dedupe URLs; for each unique URL, issue an HTTP `GET` (with a `HEAD`-first optimization allowed) via `urllib.request` with the timeout; status ≥400 → broken (`http-<status>`); timeout → `http-timeout`; connection/other error → `http-error`. Run in a bounded `ThreadPoolExecutor(max_workers=<workers>)`.
6. **Report** (LR-FR-4/9): group findings by source file; print the human or JSON format; set the exit code.

## Resolution & extraction semantics (frozen)

Precise behavior for the cases lexical extraction and internal resolution must handle:

- **Internal resolution order (LR-FR-2/3).** For a wikilink or relative Markdown link, try in order: (1) the target as a path relative to the source file's directory; (2) the target as a path relative to the scanned root; (3) **Obsidian-style shortest-unique basename** — if the target has no slash, match any file anywhere under the root whose stem (or filename) equals the target. If none of the three resolves, the link is broken. If a wikilink target has no extension, try the target with `.md` and `.markdown` appended at each step.
- **Case sensitivity.** Matching is **case-sensitive regardless of the host filesystem** — `[[B]]` does not resolve `b.md`. (Deterministic across macOS/Windows/Linux; a case-only mismatch is reported broken.)
- **Non-Markdown internal targets.** A relative *link* (`[x](sub/missing.png)`, `[y](doc.pdf)`) is existence-checked like any internal link — broken if the file is absent. Only the *content* of non-Markdown files is out of scope, not their existence.
- **Image embeds are excluded from findings.** `![alt](path)` and `![[embed]]` are image/embed syntax, not links; the extractor must not report them (strip a leading `!` before the link/wikilink pattern).
- **Code is excluded.** Links inside fenced code blocks (``` ``` ``` / `~~~`) and inline code spans (`` `...` ``) are **not** extracted — they are examples, not live links. Track fenced state per file; strip inline-code spans per line before matching.
- **URL-decoding.** Percent-decode internal targets before resolving (`sub/my%20file.md` → `sub/my file.md`).
- **Same-file anchor links** (`[t](#heading)` — no path) are ignored in v1 (headings unresolved; non-goal).
- **Encoding.** Files are read as UTF-8; a file that fails to decode is treated as unreadable (skip + stderr warning, per failure modes) — it is not a broken-link finding.
- **Directory walk.** Skip dot-directories (`.git`, `.obsidian`, any `.`-prefixed dir) by default; do not follow symlinks out of the root.

## External-check semantics (frozen; LR-FR-6)

- **HTTP method:** issue `HEAD` first; if the server returns 405/501 (or an error that isn't a clean status), retry that URL once with `GET`.
- **Redirects:** follow them (urllib default for GET; enable for HEAD) and report the **final** status. A 3xx that ultimately reaches <400 is live; one that ends ≥400 is broken with the final status.
- **User-Agent:** send `User-Agent: linkrot/0.1 (+https://github.com/JawnLam/Linkrot)` — the default `Python-urllib` UA is 403'd by many hosts and would produce false positives.
- **TLS/cert errors and connection errors** are reported `http-error`; timeouts `http-timeout`. No insecure/skip-verify mode. No built-in retry beyond the HEAD→GET fallback (the operator re-runs for transient conditions, per AT-6).

## Output semantics (frozen)

- **Zero findings:** print exactly one line to stdout — `Summary: 0 broken link(s) in 0 file(s)` (append ` (<K> external checked)` only when `--check-external` was set) — and exit 0.
- **`--check-external` off:** the summary omits the `external checked` clause entirely (no `K` shown).
- **`--json`:** stdout is **pure JSON** (nothing else); all warnings (unreadable files, wholesale network failure) go to stderr as plain text, never into stdout.
- **Ordering:** findings are sorted by source-file relative path, then by line number (stable output for diffs/CI). Paths are forward-slash-normalized on all OSes; the base for the relative path is the scanned `<directory>` argument.
- **Counting (frozen).** A **finding is one broken link occurrence** — the same broken target appearing on three lines is three findings (each with its own line number). The summary's `<N>` = total findings (occurrences). `<M>` = the number of **distinct source files that contain at least one finding** (not files scanned). `<K>` (external checked, only when `--check-external`) = the number of **unique external URLs requested**.
- **External dedup equivalence (frozen).** Two external URLs are "the same" for request-dedup (LR-FR-7) iff their strings are byte-identical **after** stripping any `#fragment`. No other normalization (trailing slash, host case, query order) is applied in v1 — `http://x.com/` and `http://x.com` are treated as distinct. Each *occurrence* of a dead URL is still reported as its own finding (checking is deduped; reporting is per-occurrence).

## Failure modes & responses

- **Unreadable file** (permissions/encoding): skip it, emit a warning line to stderr, do not abort the run; the file counts as scanned-with-warning.
- **Network unavailable** with `--check-external`: each external check errors → reported as `http-error`; the run still completes (does not crash). A leading stderr note tells the operator external checks are failing wholesale.
- **Ambiguous internal target** (matches multiple files): treat as resolved (not broken); note in v-next backlog. Not a v1 error.
- **Huge vault:** memory is bounded by streaming per-file; external concurrency is capped by `--workers`.

## Alternatives rejected

- **Full Markdown AST parse** (e.g., a parser library): rejected — adds a third-party dependency (violates LR-NFR-1) for precision v1 does not need; lexical extraction covers the link forms in the PRD.
- **Checking external links by default:** rejected — see adr-001 (latency, flakiness, privacy).
