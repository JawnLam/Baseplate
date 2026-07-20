---
Item_ID: gridlock-ux-spec
type: BASEPLATE_Design_Document
title: "Gridlock — UX Design Spec"
baseplate_Product_Slug: "GL"
baseplate_Doc_Class: ux-spec
baseplate_Layer: 3
baseplate_Document_Status: shipped
baseplate_Precedence_Rank: 12
Date_Added: 2026-07-20
Date_Modified: 2026-07-20
Needs_Processing: false
---

# Gridlock — UX Design Spec

> **Flows, states, components, accessibility** for the portrait, one-thumb PWA (PRD GL-FR-11). The visual language is generic football — chalkboard X's and O's, field turf, stadium night — with zero licensed iconography (PRD GL-NFR-4). The reveal ("the snap") is the signature beat the whole presentation invests in (`adr-002`). Interface truth: the client renders exactly what the protocol delivers (`interface-contracts.md`); it never infers concealed state.

## 1. Screen flow

```
First launch → Handle card (auto-generated, editable) → HOME
HOME → [Play vs AI] → Playbook select → MATCH
HOME → [Find opponent] → Playbook select → Queue (backfill countdown) → MATCH
MATCH → Result screen → [Rematch same setup] / [Home]
HOME → Settings (motion/haptics) · How-to-play (static, 5 cards)
```

No other screens exist in the prototype. No login, no store, no collection (PRD §4).

## 2. The match screen (the product)

Portrait, three fixed zones, thumb-reach ordered:

- **Zone A — field strip (top, ~25%):** stylized side-view field with LOS marker and first-down line; score, possession count of total ("Drive 2 of 4"), down & distance ("3rd & 4"), opponent handle + CP + hand count. Updates only at phase boundaries (GL-RS-11 — the UI *cannot* leak commit status because the protocol never sends it).
- **Zone B — the stage (middle, ~40%):** where committed cards sit face-down and the snap happens. States: empty → own card slides in face-down (commit locked) → **SNAP**: both cards flip simultaneously with a single hard impact frame (one haptic pulse), clause "trap springs" flare if fired (GL-RS-25) → outcome banner (yards gained/lost, event name in football speech: "SACKED!", "PICKED OFF!", "BREAKAWAY!") → chains/LOS animate on the field strip.
- **Zone C — the hand (bottom, ~35%):** fan of up to 4 cards (GL-RS-18) in thumb arc. Card face: play name, category icon + color + text label, tier pips, CP cost badge. Unaffordable cards are desaturated with the cost badge highlighted (GL-RS-9). Tap → card raises with a one-line tactical hint and CANCEL/COMMIT buttons (two-step commit; no drag required). Scout button (binocular icon, CP price tag) sits left of the fan; disabled state shows the reason on tap (GL-RS-23). Shot clock: a ring around the committed slot, final 5 seconds pulse + haptic (GL-RS-12).

**4th-down declaration (GL-RS-30):** before the commit UI, a full-width three-button sheet — GO FOR IT / PUNT / FIELD GOAL (with live success % from the distance table, GL-RS-32; FG disabled out of range with the reason). This sheet is the press-your-luck moment; it gets its own tension treatment (crowd hush audio cue if sound is on).

## 3. Component inventory

`FieldStrip · ScoreBug · DownDistanceChip · StageSlot (own/opponent) · SnapReveal · OutcomeBanner · TrapFlare · HandFan · PlayCard · CPMeter · ScoutButton · ShotClockRing · DeclarationSheet · QueueCard · PlaybookCard (archetype art + strategy blurb + O/D category histogram) · ResultCard (score, per-drive summary strip) · ReconnectToast · HowToCards`

Each component's data needs map 1:1 to `phase` / `reveal` / `resolution` / `possession_change` / `match_end` protocol payloads — no component requires data the protocol does not deliver.

## 4. States & edge presentations (every one has a defined face)

| State | Presentation |
|---|---|
| Opponent thinking | Neutral "opponent's sideline" shimmer — **identical whether or not they have committed** (information discipline, GL-RS-8) |
| Auto-commit fired (own) | "Clock! Play sent in." toast + flagged card glow (GL-RS-12) |
| No-legal-card redraw | "Shuffling the call sheet…" beat, both players see it (GL-RS-10 public shuffle) |
| Scout result | Private card peek overlay, 3 s, "SCOUTED" stamp (GL-RS-23) |
| Turnover / safety / TD / FG | Full-zone-B takeover banners, ≤ 1.5 s, skippable by tap |
| Possession change | Field strip flips orientation; "YOUR BALL" / "DEFEND" banner (GL-RS-33) |
| Tie-break round | "OVERTIME" slate (GL-RS-6) |
| Disconnect / resume | ReconnectToast with countdown (resume window, `technical-design.md` §6); on resume the current `phase` re-renders fully — the UI is stateless across reconnects |
| Opponent forfeit / concede | Result screen with "by forfeit" tag (GL-RS-14/36/37) |
| Match void (server crash) | "Match abandoned — no result recorded" (technical-design §6 crash policy) |

## 5. Accessibility (requirements, not suggestions)

1. Touch targets ≥ 44×44 px; the entire commit flow reachable in the bottom 60% of the screen (one-thumb rule).
2. Category identification is **triple-coded**: icon shape + color + text label — never color alone. The six offensive and five defensive category colors must pass WCAG 2.1 AA contrast (≥ 4.5:1 for text, ≥ 3:1 for icons) on their card backgrounds in both the default and desaturated (unaffordable) states.
3. `prefers-reduced-motion` (and the in-app toggle): snap reveal becomes a crossfade, banners static, shot-clock pulse becomes a color step. No information is motion-only.
4. Haptics are enhancement-only and toggleable; sound is entirely optional (the game is fully playable muted — no audio-only cues).
5. Shot clock is generous by design (default 20 s ⚙) and every timed action has a safe automatic outcome (GL-RS-12) — time pressure may never soft-lock a player.
6. All text real text (no text-in-images); system font stack; minimum 14 px body, 12 px only for card metadata.

## 6. PWA behaviors

Installable (manifest: portrait orientation lock, standalone display, generic football-chalk icon set — original artwork). Service worker caches the app shell for instant load; **no offline play** (server-authoritative; `technical-design.md` TD-A-3). On network loss mid-match: ReconnectToast per §4.

## 7. Alternatives rejected

| Alternative | Why rejected |
|---|---|
| Drag-to-commit interaction | Two-step tap is faster one-thumbed, more accessible, and less error-prone on small screens. |
| Landscape "field view" board | Fights one-thumb portrait play and the card-fan ergonomics; the field is context, not the play surface — Gridlock is a card game, not a field simulator (`prd.md` §1). |
| Showing "opponent has committed" indicator | Would leak tempo information the rules deliberately conceal (GL-RS-8/11). |
| Rich play-diagram animations per card | Prototype scope: static chalk diagrams on card faces suffice; animation budget is spent on the snap. |

## 8. Assumptions & open questions

| # | Item | Owner | Status |
|---|---|---|---|
| UX-A-1 | Exact category colorway and icon set are build-time art tasks bounded by §5.2's triple-coding + contrast rules; no further sign-off gate. | Builder | Bounded, open |
| UX-A-2 | "How-to-play" card copy is drafted at build from `game-rules-spec.md` §2–§9 summaries; the rules spec is the source of truth. | Builder | Bounded, open |
