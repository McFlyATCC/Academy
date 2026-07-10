# McFly Academy

Interactive learning tools for IT students — Linux Administration, Security, and Windows/Cloud/Virtualization — with a Back to the Future twist.

A zero-build static site: every page is a self-contained HTML file (inline CSS + vanilla JS). No frameworks, no dependencies, no build step. Push to publish via GitHub Pages.

**Live site:** https://mcflyatcc.github.io/Academy/

## Structure

- `index.html` — the hub: a dashboard of feature cards organized by course area
- One standalone HTML file per interactive tool
- `docs/superpowers/specs/` — design specs
- `docs/superpowers/plans/` — implementation plans

Inspired by the structure of [DrGopher9-Academy](https://github.com/DrGopher9).

## Tools

| Tool | File | What it teaches |
|------|------|-----------------|
| Linux Command Trainer | `linux-trainer.html` | Files, permissions, users, processes — type-the-command drills |
| Hash Lab | `hash-lab.html` | Hashing vs. encoding, password strength, hash matching |
| VM Console | `vm-console.html` | VM lifecycle, service management, Shared Responsibility Model |
