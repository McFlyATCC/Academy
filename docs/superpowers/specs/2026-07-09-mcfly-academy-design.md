# McFly Academy — Design Spec

**Date:** 2026-07-09
**Status:** Approved
**Author:** Matt McCullough (McFlyATCC), with Claude

## Purpose

A hub of interactive, in-browser learning tools for Matt's IT courses, covering three areas at launch: **Linux Administration**, **Security**, and **Windows / Cloud / Virtualization**. Modeled on the structure of DrGopher9-Academy (Steven's Academy) but with its own brand: **McFly Academy**, themed on Back to the Future.

Audience: IT/networking program students. Tools are drills, games, simulators, and visualizations — not articles.

## Architecture (decided)

**Zero-build static site of fully self-contained pages.**

- Every page carries its own inline `<style>` and `<script>`. No shared CSS/JS files, no framework, no build step, no package.json.
- Only permitted external dependency: Google Fonts.
- Each tool is one HTML file plus one feature card on the hub. Tools can be created, themed, and edited in complete isolation; nothing can break site-wide.
- Rejected alternatives: shared stylesheet (pages stop being standalone; brand changes ripple across tools), static site generator (tooling overkill for ~10 pages).

## Repo & hosting

- Local repo: `~/Desktop/Academy`, branch `main`.
- GitHub: `McFlyATCC/Academy`, public.
- GitHub Pages serving from `main` branch root → `https://mcflyatcc.github.io/Academy/`.
- Publish workflow: commit, push. No CI needed.

## The hub — `index.html`

Title: **McFly Academy**. Same dashboard structure as Steven's Academy: header with clickable icon and title, category sections, color-coded feature cards in a responsive grid (`grid-template-columns: repeat(auto-fit, minmax(350px, 1fr))`), each card with a colored top border, one-line pitch, and an emoji launch button.

### Visual identity — Back to the Future

- Dark base with the movie palette: OUTATIME-plate orange/red, flux-capacitor blue glow, DeLorean brushed-steel grays.
- Header includes a **time-circuit LED readout**: DESTINATION TIME / PRESENT TIME (live clock) / LAST TIME DEPARTED, in seven-segment-style type (Google Fonts: e.g., Orbitron or Share Tech Mono).
- Subtitle: "Roads? Where we're going, we don't need roads."
- Favicon: inline SVG emoji (⚡ or 🚗), same data-URI technique as the original.

### Sections and cards at launch

1. **Linux Administration** — Linux Command Trainer (working) + 1–2 "coming soon" cards.
2. **Security** — Hash Lab (working) + coming-soon cards.
3. **Windows, Cloud & Virtualization** — VM Console (working) + coming-soon cards.

"Coming soon" cards are styled as a time circuit that hasn't been set (dim LED readout, disabled button).

### Easter eggs (hub)

BTTF-flavored equivalents of the original's cheat system, driven by the same key-buffer listener pattern:

- Typing `88mph` → speed/fire-trails animation across the cards.
- Typing `flux` → toggle flux-capacitor glow mode (blue pulsing borders).
- Typing `1955` → toggle sepia retro mode.
- Typing `biff` → insult toast ("Hello? Hello? Anybody home? Think, McFly, think!").
- Konami code → kept, "GREAT SCOTT!" toast + container spin.
- Clicking the title → clock-tower lightning strike flash animation.
- Console ASCII art: DeLorean, plus a hint that cheats exist.
- Toast notification element (`#egg-toast`) announces each activation, as in the original.

## The three starter tools

Each: standalone HTML file, own visual theme, home button (`← Hub`) back to `index.html`, at least one easter egg, self-contained data as in-page JS arrays.

### `linux-trainer.html` — Linux Command Trainer

- Drill loop: show a task prompt ("make `script.sh` executable", "find all files owned by user marty"), student types the command in a fake prompt, instant right/wrong feedback with a short explanation of the correct answer.
- Accept reasonable answer variants (e.g., `chmod +x script.sh` and `chmod u+x script.sh`) via a per-question array of accepted patterns.
- Question bank: JS array in-page; launch with ~25 questions covering files/navigation, permissions, users/groups, processes, searching.
- Score + streak tracking (in-memory; localStorage persistence optional).
- Theme: green-on-black CRT terminal, styled as the DeLorean's onboard computer.

### `hash-lab.html` — Hash Lab

- Side-by-side live panels: SHA-256 hashing (Web Crypto API `crypto.subtle.digest`) vs. Base64 encoding (`btoa`), updating as the student types — teaches hashing ≠ encoding (one-way vs. reversible, fixed vs. variable length).
- Password strength auditor: entropy-based crack-time estimate with themed verdicts ("Biff would guess this in 2 seconds").
- Hash-matching mini-challenge: given a hash and a small wordlist, find which word produced it.
- Theme: security-lab dark UI with flux-blue accents.

### `vm-console.html` — VM Console ("Hill Valley Cloud Services")

- Fake cloud dashboard: deploy VMs (choose OS and size), start/stop/delete, simulated provisioning status transitions (pending → running) with timers.
- Shared Responsibility Model quiz panel: given a duty ("patching the guest OS"), pick provider vs. customer responsibility.
- Basic service management on a VM: start/stop/restart services with visible consequences (e.g., stopping the web service takes the fake status page down).
- All state in-memory (page refresh resets — acceptable for v1).
- Theme: cloud-portal UI, "Hill Valley Cloud Services" branding.

## Error handling & testing

- No backend, no network calls (except fonts): failure modes are JS bugs only. Each tool must fail soft — invalid input shows feedback, never a dead page.
- Web Crypto requires a secure context: fine on GitHub Pages (HTTPS) and `localhost`; note in dev docs to preview via `python3 -m http.server`, not `file://`.
- Testing is manual: a per-page checklist in the implementation plan (each interaction, each easter egg, mobile-width grid collapse). No test framework — consistent with the zero-build choice.

## Out of scope for v1

- Networking/CCNA section (future).
- User accounts, progress persistence across devices, backend of any kind.
- The remaining "coming soon" tools — each gets its own future spec/plan cycle, one file + one card per iteration.

## Success criteria

- Site live at the GitHub Pages URL; hub renders correctly on desktop and phone widths.
- All three tools playable end-to-end with correct feedback.
- All listed easter eggs fire.
- A new tool can be added by writing one HTML file and one card — no other file edits required.
