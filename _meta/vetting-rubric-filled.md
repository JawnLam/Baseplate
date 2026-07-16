---
type: Fleeting
timestamp: "2026-07-16T00:00:00Z"
Item_ID: baseplate-vetting-rubric
title: "Baseplate — Vetting Rubric (filled)"
Date_Added: 2026-07-16
Date_Modified: 2026-07-16
Needs_Processing: false
---

# Baseplate — Vetting Rubric (0–3 scorecard)

> Rendered from `_meta/posture.yaml` through the standalone-sufficiency vetting rubric. Scale: 0 (absent) / 1 (weak) / 2 (solid) / 3 (strong).

## Gating rule (veto)

All five T0 hard gates ship as `met`. **Gating-rule veto: does not fire.**

## Tier scores

| Tier | Focus | Score | Notes |
|------|-------|-------|-------|
| T0 — Table stakes | Parity, boundaries, persistence, first-value | 3 | All five T0 gates met with evidence |
| T1 — Sufficiency drivers | State, workflow, accountability | 3 | Per-cartridge state + gates + revision protocol |
| T2 — Durability | Moat items | 2 | Two moats committed (REQ-M1 flywheel, REQ-M2 switching cost); flywheel proves out only as the portfolio accumulates (v0.x) |
| TG — Conditional (high-stakes) | Calibration, escalation, governance | n-a | domain_stakes: low |

## Weighted result

- T0: pass (veto clear).
- Moat items committed: 2 of 5, both with concrete schema-feature pointers.
- Coverage: T1 strong, T2 solid-and-growing.

## Verdict band

**Defensible specialist.** The moat rests on accumulated proprietary portfolio state (the failure catalog) and gates enforced as ship blocks — not on promptable behavior the platform absorbs. The REQ-M1 flywheel is committed but literally empty at v0.1 (only the five seed entries); it earns its "strong" rating as real cartridges accumulate. Re-score after the shakedown and the first few real products.
