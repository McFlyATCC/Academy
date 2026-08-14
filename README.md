# McFly Academy

Interactive learning tools for IT students — Linux Administration, Security, and Windows/Cloud/Virtualization — with a Back to the Future twist.

A zero-build static site: every page is a self-contained HTML file (inline CSS + vanilla JS). No frameworks, no dependencies, no build step. Push to publish via GitHub Pages.

**Live site:** https://mcflyatcc.github.io/Academy/

## Structure

- `index.html` — instructor introduction; the front door of the site
- `tools.html` — the hub: a dashboard of feature cards organized by course area
- One standalone HTML file per interactive tool
- `assets/` — images for the introduction page
- `docs/superpowers/specs/` — design specs
- `docs/superpowers/plans/` — implementation plans

Inspired by the structure of [DrGopher9-Academy](https://github.com/DrGopher9). The
introduction page follows the layout and accessibility patterns of
[Steven Van Voorhis's instructor page](https://drgopher9.github.io/instructor-introduction/).

### Images

`assets/` holds each photo twice: the original PNG as the source of truth, and a
resized, compressed `-web.jpg` that the page actually loads. Together the three
web copies are about 340 KB, down from 7.3 MB of originals.

| Loaded by the page | Source | Used for |
|--------------------|--------|----------|
| `atcc-logo-horizontal.png` | — | Header logo |
| `headshotatcc-web.jpg` | `headshotatcc.png` | Hero headshot |
| `p1-web.jpg` | `p1.png` | "Travel" photo |
| `p2-web.jpg` | `p2.png` | "Hiking" photo |

Re-crop an original, then regenerate its web copy:

```sh
sips -Z 700 -s format jpeg -s formatOptions 82 assets/p1.png --out assets/p1-web.jpg
```

Use `-Z 600` for the square headshot. A missing image renders as a dashed box
naming the file it wants, rather than a broken-image icon.

## Tools

| Tool | File | What it teaches |
|------|------|-----------------|
| Linux Command Trainer | `linux-trainer.html` | Files, permissions, users, processes — type-the-command drills |
| Hash Lab | `hash-lab.html` | Hashing vs. encoding, password strength, hash matching |
| VM Console | `vm-console.html` | VM lifecycle, service management, Shared Responsibility Model |
| CIA Triad Trainer | `triad-trainer.html` | Classifying incidents as confidentiality, integrity, or availability violations |
