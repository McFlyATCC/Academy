# CIA Triad Trainer — Design

**Date:** 2026-07-10
**Status:** Approved
**Parent spec:** 2026-07-09-mcfly-academy-design.md (this is one of the "coming soon" tools, built in its own spec/plan cycle)

## What we're building

A scenario-classification drill for the CIA triad (Confidentiality, Integrity, Availability), as the second tool in McFly Academy's **Security** section. Students read a short incident scenario and pick which pillar was primarily violated.

Deliverable follows the site's one-tool recipe:

1. New self-contained page: `triad-trainer.html` — inline CSS/JS, vanilla JS, Google Fonts as the only external dependency.
2. Convert the existing "CIA Triad Trainer" coming-soon card on `index.html` into a live card (Security accent, working button).

## Theme

Security-section styling, matching Hash Lab's accent treatment. Scenarios use Back to the Future framing: Biff, Marty, Doc, Hill Valley businesses (e.g. "Biff shoulder-surfs Marty's password at the Café 80's kiosk").

## Page layout (top to bottom)

1. **Header** — title, tagline, back-to-hub link; consistent with the other tool pages.
2. **Explainer strip** — three small cards, one per pillar:
   - **Confidentiality** — one-line definition + example controls: encryption, access control, least privilege.
   - **Integrity** — definition + controls: hashing, digital signatures, checksums, version control.
   - **Availability** — definition + controls: backups, RAID, redundancy, UPS.
   - Each card has a distinct accent color that carries into the matching drill answer button.
3. **Drill panel:**
   - Scoreboard: SCORE / STREAK, same pattern as Linux Command Trainer.
   - One scenario at a time from a shuffled bank of **~30 scenarios**.
   - Three large answer buttons — C / I / A — color-matched to the explainer cards.
   - Keyboard shortcuts: 1 / 2 / 3.
   - **Single correct answer per scenario.** Scenarios are written so one pillar is clearly primary; the "why" feedback line names secondary impacts where relevant (e.g. ransomware: "primarily Availability — integrity is also hit since files were altered").
   - Feedback: ✔/✘ + why line; streak-fire message at 3+ streak; Next button advances.

## Data model

Inline JS array, same pattern as the Linux Trainer drill bank:

```js
const SCENARIOS = [
  { text: "…", answer: "C" | "I" | "A", why: "…" },
];
```

Shuffled once per page load; wraps around (reshuffle) when exhausted.

## Error handling & testing

No async work and no external data, so the failure surface is minimal. Verification is a browser pass on `triad-trainer.html` (drill flow, keyboard shortcuts, streak/score behavior) and the updated hub card, before commit.

## Out of scope (YAGNI)

- Multi-select / multi-pillar answers.
- Persistence (localStorage scores).
- Timers or countdown mechanics.
- Any changes to other tool pages.
