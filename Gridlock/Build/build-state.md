# Gridlock — Build State

> **The build's memory. Update this file at the end of every working session** — a future session (you or another AI) must be able to resume cold from this file alone. Rules: `../AI-BOOTSTRAP.md` §6.

## Phase

`not-started` — no build session has occurred yet.

Suggested phase sequence (from the architecture's dependency order; not binding): `engine+sim` → `server+protocol` → `client+deploy` → `acceptance`.

## Done

*(nothing yet)*

## Next

1. Read `../AI-BOOTSTRAP.md` in full; verify `../Construction/MANIFEST.md`.
2. Begin phase 1: repo skeleton, balance data as JSON, the pure engine, the sim harness (see `../Construction/technical-design.md` §2–§3).

## Decisions made during build

*(record each with date + rationale; anything that contradicts a construction document goes in `deviations.md` instead)*
