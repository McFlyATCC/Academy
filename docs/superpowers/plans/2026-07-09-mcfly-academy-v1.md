# McFly Academy V1 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build and publish the McFly Academy hub (`index.html`) plus three interactive learning tools (Linux Command Trainer, Hash Lab, VM Console) as a zero-build static site on GitHub Pages.

**Architecture:** Every page is a fully self-contained HTML file with inline `<style>` and `<script>` — no shared CSS/JS, no framework, no build step. The hub is a card dashboard linking to standalone tool pages; each tool page links back to the hub. Adding a future tool = one new HTML file + one card on the hub.

**Tech Stack:** Plain HTML5, CSS (grid/flex, keyframe animations), vanilla JavaScript. Web Crypto API (`crypto.subtle`) for hashing. Google Fonts (Orbitron, Share Tech Mono) — the only permitted external dependency.

## Global Constraints

- Repo: `/Users/mattmccullough/Desktop/Academy`, branch `main`, remote `McFlyATCC/Academy`. **Never touch `~/Desktop/DrGopher9-Academy`** — it is read-only reference material.
- Zero-build: no package.json, no node_modules, no build tools, no test framework. Verification is manual via the checklists in each task.
- Every page self-contained: inline `<style>` and `<script>` only. Only external requests allowed: Google Fonts (`fonts.googleapis.com` / `fonts.gstatic.com`).
- Site name: **McFly Academy**. Hub subtitle: `Roads? Where we're going, we don't need roads.`
- Shared BTTF palette (repeat these exact hexes in every page):
  - Base background gradient: `#0d0d10` → `#1a1c22`
  - Steel gray panels: `#2a2d33`, borders `#3a3e46`
  - OUTATIME orange: `#ff8c00`; alert red: `#e03616`
  - Flux blue: `#4dc8ff`; terminal green: `#39ff14`
  - Text: `#f5f5f5` primary, `#a8adb8` secondary
- Fonts on every page: `<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Share+Tech+Mono&display=swap" rel="stylesheet">`. Orbitron for headings/LED readouts, Share Tech Mono for terminal/mono text, system sans (`'Segoe UI', Tahoma, sans-serif`) for body copy.
- Favicon technique (every page, swap the emoji per page): `<link rel="icon" href="data:image/svg+xml,<svg xmlns=%22http://www.w3.org/2000/svg%22 viewBox=%220 0 100 100%22><text y=%22.9em%22 font-size=%2290%22>⚡</text></svg>">`
- Every tool page has a home button in its header: `<a href="index.html" class="home-btn">← Hub</a>`.
- Preview during development: `cd ~/Desktop/Academy && python3 -m http.server 8000`, open `http://localhost:8000`. **Never preview via `file://`** — Web Crypto (`crypto.subtle`) requires a secure context (localhost or HTTPS).
- Fail soft: invalid user input shows inline feedback; no interaction may leave a page dead or blank.
- Commit after every task with the trailer: `Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>`

## File Structure

```
Academy/
├── index.html          # Hub: header + time circuits, 3 category sections, cards, easter eggs (Tasks 1–2)
├── linux-trainer.html  # Linux Command Trainer drill (Task 3)
├── hash-lab.html       # Hashing vs. encoding lab + password auditor + hash challenge (Task 4)
├── vm-console.html     # Hill Valley Cloud Services VM simulator + SRM quiz (Task 5)
├── README.md           # Already exists; updated in Task 6
└── docs/superpowers/   # Specs and this plan (already committed)
```

---

### Task 1: Hub page — layout, theme, time circuits, cards

**Files:**
- Create: `index.html`

**Interfaces:**
- Produces: card grid CSS classes (`.feature-card`, `.grid-layout`, `.category-header`, `.btn-main`) and the page skeleton that Task 2 adds scripts into. Tool pages created in Tasks 3–5 are linked by exact filenames `linux-trainer.html`, `hash-lab.html`, `vm-console.html`.
- Produces: element IDs Task 2 relies on: `#main-title`, `#main-icon`, `#egg-toast`, `#present-time`, `.container`.

- [ ] **Step 1: Write `index.html`**

Full page. Structure and all CSS below are complete — copy verbatim, then sanity-read once:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>McFly Academy</title>
<link rel="icon" href="data:image/svg+xml,<svg xmlns=%22http://www.w3.org/2000/svg%22 viewBox=%220 0 100 100%22><text y=%22.9em%22 font-size=%2290%22>⚡</text></svg>">
<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Share+Tech+Mono&display=swap" rel="stylesheet">
<style>
  body {
    font-family: 'Segoe UI', Tahoma, sans-serif;
    background: linear-gradient(135deg, #0d0d10 0%, #1a1c22 100%);
    min-height: 100vh; color: #f5f5f5; margin: 0; padding-bottom: 50px;
    transition: background 0.5s ease, filter 0.3s;
  }

  /* --- HEADER --- */
  .header-section {
    background: rgba(0,0,0,0.55); padding: 36px 20px 28px; text-align: center;
    border-bottom: 2px solid #ff8c00; margin-bottom: 40px; user-select: none;
  }
  .main-icon { font-size: 4em; display: inline-block; cursor: pointer; transition: transform 0.2s; }
  .main-icon:active { transform: scale(0.9); }
  h1 {
    font-family: 'Orbitron', sans-serif; font-size: 2.8em; margin: 10px 0 4px;
    color: #ff8c00; letter-spacing: 3px; text-transform: uppercase; cursor: pointer;
    text-shadow: 0 0 18px rgba(255,140,0,0.45); transition: color 0.3s;
  }
  p.subtitle { color: #a8adb8; font-size: 1.15em; margin: 0 0 24px; font-style: italic; }

  /* --- TIME CIRCUITS --- */
  .time-circuits {
    display: inline-flex; flex-direction: column; gap: 6px;
    background: #111; border: 2px solid #3a3e46; border-radius: 10px;
    padding: 12px 18px; box-shadow: inset 0 0 20px #000;
  }
  .circuit-row { display: flex; align-items: center; gap: 14px; }
  .circuit-label {
    font-family: 'Orbitron', sans-serif; font-size: 0.55em; letter-spacing: 2px;
    color: #a8adb8; width: 150px; text-align: right; text-transform: uppercase;
  }
  .circuit-value {
    font-family: 'Share Tech Mono', monospace; font-size: 1.25em; letter-spacing: 3px;
    padding: 2px 10px; border-radius: 4px; background: #000; min-width: 220px; text-align: center;
  }
  .circuit-red   { color: #e03616; text-shadow: 0 0 8px rgba(224,54,22,0.8); }
  .circuit-green { color: #39ff14; text-shadow: 0 0 8px rgba(57,255,20,0.8); }
  .circuit-amber { color: #ff8c00; text-shadow: 0 0 8px rgba(255,140,0,0.8); }

  /* --- LAYOUT --- */
  .container { width: 100%; max-width: 1200px; margin: 0 auto; padding: 0 20px; }
  .category-header {
    font-family: 'Orbitron', sans-serif; font-size: 1.3em; color: #4dc8ff;
    border-bottom: 2px solid #3a3e46; padding-bottom: 10px; margin: 40px 0 20px;
    text-transform: uppercase; letter-spacing: 3px;
  }
  .grid-layout { display: grid; grid-template-columns: repeat(auto-fit, minmax(350px, 1fr)); gap: 25px; }

  /* --- CARDS --- */
  .feature-card {
    background: linear-gradient(145deg, #232529, #2a2d33);
    border: 1px solid #3a3e46; border-top: 4px solid #555; border-radius: 12px;
    padding: 25px; box-shadow: 0 4px 15px rgba(0,0,0,0.4);
    transition: transform 0.2s ease, box-shadow 0.2s ease;
    display: flex; flex-direction: column; justify-content: space-between;
  }
  .feature-card:hover { transform: translateY(-5px); box-shadow: 0 10px 25px rgba(0,0,0,0.6); }
  .feature-card h2 { margin: 0 0 10px; color: #fff; font-size: 1.35em; font-family: 'Orbitron', sans-serif; letter-spacing: 1px; }
  .feature-card p { color: #a8adb8; margin-bottom: 25px; line-height: 1.5; font-size: 0.95em; flex-grow: 1; }
  .btn-main {
    display: inline-block; padding: 10px 20px; background: #444; color: #fff;
    text-decoration: none; border-radius: 6px; font-weight: bold; text-align: center; transition: all 0.2s;
  }
  .btn-main:hover { filter: brightness(1.25); box-shadow: 0 4px 12px rgba(0,0,0,0.4); }

  /* Category accents */
  .cat-linux { border-top-color: #39ff14; }
  .btn-linux { background: linear-gradient(135deg, #1f7a1f 0%, #0e3d0e 100%); border: 1px solid #39ff14; }
  .cat-security { border-top-color: #e03616; }
  .btn-security { background: linear-gradient(135deg, #e03616 0%, #7a1d0c 100%); }
  .cat-cloud { border-top-color: #4dc8ff; }
  .btn-cloud { background: linear-gradient(135deg, #1c7fb8 0%, #0d3f5c 100%); }

  /* Coming-soon: an unset time circuit */
  .feature-card.coming-soon { opacity: 0.55; border-top-color: #3a3e46; }
  .coming-soon .led-unset {
    font-family: 'Share Tech Mono', monospace; color: #e03616; background: #000;
    padding: 2px 10px; border-radius: 4px; letter-spacing: 3px;
    animation: led-flicker 2.4s infinite;
  }
  @keyframes led-flicker { 0%,92% { opacity: 1; } 94% { opacity: 0.2; } 96% { opacity: 1; } 98% { opacity: 0.3; } 100% { opacity: 1; } }
  .coming-soon .btn-main { background: #222; color: #666; pointer-events: none; border: 1px solid #3a3e46; }

  /* --- TOAST (used by Task 2 scripts) --- */
  #egg-toast {
    position: fixed; top: 20px; left: 50%; transform: translateX(-50%);
    background: #111; color: #fff; padding: 10px 22px; border-radius: 50px;
    box-shadow: 0 5px 15px rgba(0,0,0,0.6); display: none; z-index: 1000;
    font-weight: bold; border: 1px solid #3a3e46; animation: slideDown 0.5s ease;
  }
  @keyframes slideDown { from { top: -50px; } to { top: 20px; } }
</style>
</head>
<body>

<div id="egg-toast">Easter Egg Activated!</div>

<div class="header-section">
  <div class="main-icon" id="main-icon">🚗</div>
  <h1 id="main-title">McFly Academy</h1>
  <p class="subtitle">Roads? Where we're going, we don't need roads.</p>
  <div class="time-circuits">
    <div class="circuit-row"><span class="circuit-label">Destination Time</span><span class="circuit-value circuit-red">OCT 26 1985 09:00</span></div>
    <div class="circuit-row"><span class="circuit-label">Present Time</span><span class="circuit-value circuit-green" id="present-time">--- -- ---- --:--</span></div>
    <div class="circuit-row"><span class="circuit-label">Last Time Departed</span><span class="circuit-value circuit-amber">NOV 05 1955 06:15</span></div>
  </div>
</div>

<div class="container">

  <div class="category-header">Linux Administration</div>
  <div class="grid-layout">
    <div class="feature-card cat-linux">
      <h2>Linux Command Trainer</h2>
      <p>The DeLorean's onboard computer. Type the right command for each task — permissions, files, users, processes.</p>
      <a href="linux-trainer.html" class="btn-main btn-linux">Power Up 🖥️</a>
    </div>
    <div class="feature-card coming-soon">
      <h2>Filesystem Navigator</h2>
      <p>Explore a simulated directory tree. Destination not yet set.</p>
      <span class="led-unset">-- -- ---- --:--</span>
    </div>
  </div>

  <div class="category-header">Security</div>
  <div class="grid-layout">
    <div class="feature-card cat-security">
      <h2>Hash Lab</h2>
      <p>Hashing vs. encoding, live. Audit password strength — would Biff crack yours in 2 seconds?</p>
      <a href="hash-lab.html" class="btn-main btn-security">Enter Lab 🔐</a>
    </div>
    <div class="feature-card coming-soon">
      <h2>CIA Triad Trainer</h2>
      <p>Confidentiality, Integrity, Availability drills. Destination not yet set.</p>
      <span class="led-unset">-- -- ---- --:--</span>
    </div>
  </div>

  <div class="category-header">Windows, Cloud &amp; Virtualization</div>
  <div class="grid-layout">
    <div class="feature-card cat-cloud">
      <h2>VM Console</h2>
      <p>Hill Valley Cloud Services. Deploy VMs, manage services, and master the Shared Responsibility Model.</p>
      <a href="vm-console.html" class="btn-main btn-cloud">Launch Portal ☁️</a>
    </div>
    <div class="feature-card coming-soon">
      <h2>Active Directory Sim</h2>
      <p>Users, groups, and OUs in a fake domain. Destination not yet set.</p>
      <span class="led-unset">-- -- ---- --:--</span>
    </div>
  </div>

</div>

<script>
  // Present Time live clock — e.g. "JUL 09 2026 14:32"
  const MONTHS = ["JAN","FEB","MAR","APR","MAY","JUN","JUL","AUG","SEP","OCT","NOV","DEC"];
  function tickClock() {
    const n = new Date();
    const pad = v => String(v).padStart(2, "0");
    document.getElementById("present-time").textContent =
      `${MONTHS[n.getMonth()]} ${pad(n.getDate())} ${n.getFullYear()} ${pad(n.getHours())}:${pad(n.getMinutes())}`;
  }
  tickClock();
  setInterval(tickClock, 1000);
</script>
</body>
</html>
```

- [ ] **Step 2: Verify in browser**

Run: `cd ~/Desktop/Academy && python3 -m http.server 8000` (background), open `http://localhost:8000`.

Checklist:
- Header shows 🚗, orange "MCFLY ACADEMY" title, italic subtitle.
- Time circuits: red destination row, green PRESENT TIME showing the actual current date/time and updating each minute, amber departed row.
- Three category sections, each with one live card and one dimmed coming-soon card showing a flickering red `-- -- ---- --:--` LED and a dead button.
- The three live buttons point at `linux-trainer.html`, `hash-lab.html`, `vm-console.html` (404 until Tasks 3–5 — expected).
- Narrow the window below ~400px: cards stack one per row, no horizontal scroll.
- Browser console: no errors.

- [ ] **Step 3: Commit**

```bash
cd ~/Desktop/Academy
git add index.html
git commit -m "feat: add McFly Academy hub with time circuits and tool cards

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 2: Hub easter eggs

**Files:**
- Modify: `index.html` (extend the existing `<style>` and `<script>` blocks)

**Interfaces:**
- Consumes: `#egg-toast`, `#main-title`, `#main-icon`, `.container`, `.feature-card` from Task 1.
- Produces: nothing consumed later — the tool pages each carry their own eggs.

- [ ] **Step 1: Add easter-egg CSS**

Append inside the existing `<style>` block of `index.html`:

```css
/* --- EASTER EGG MODES --- */
/* flux: pulsing blue glow */
@keyframes flux-pulse {
  0%,100% { border-top-color: #4dc8ff; box-shadow: 0 0 8px rgba(77,200,255,0.4); }
  50%     { border-top-color: #bfeaff; box-shadow: 0 0 24px rgba(77,200,255,0.9); }
}
body.flux-mode .feature-card { animation: flux-pulse 1.6s ease-in-out infinite; }
body.flux-mode h1 { color: #4dc8ff; text-shadow: 0 0 18px rgba(77,200,255,0.7); }

/* 1955: sepia retro */
body.retro-mode { filter: sepia(0.85) contrast(1.05); }

/* 88mph: fire trails sweep */
@keyframes fire-trail {
  0%   { box-shadow: -40px 0 30px -10px rgba(255,140,0,0.9), -80px 0 50px -20px rgba(224,54,22,0.7); transform: translateX(30px); }
  100% { box-shadow: 0 0 0 0 rgba(255,140,0,0), 0 0 0 0 rgba(224,54,22,0); transform: translateX(0); }
}
body.speed-mode .feature-card { animation: fire-trail 0.9s ease-out; }

/* lightning strike flash */
@keyframes lightning { 0% { filter: brightness(1); } 10% { filter: brightness(3); } 20% { filter: brightness(1); } 30% { filter: brightness(2.4); } 45% { filter: brightness(1); } }
body.lightning { animation: lightning 0.8s linear; }
```

- [ ] **Step 2: Add easter-egg JS**

Append inside the existing `<script>` block:

```javascript
// --- CONSOLE ART ---
console.log(String.raw`
        __---~~~~--__
   __--~   MCFLY    ~--__
  |[]___________________[]|
  |  ACADEMY  --  88 MPH  |
   \______________________/
    (o)              (o)

GREAT SCOTT! Hidden cheats exist.
Try typing things a time traveler would type...`);

// --- TOAST ---
function showToast(msg, color) {
  const t = document.getElementById("egg-toast");
  t.innerText = msg; t.style.borderColor = color; t.style.color = color;
  t.style.display = "block";
  clearTimeout(showToast._h);
  showToast._h = setTimeout(() => { t.style.display = "none"; }, 5000);
}

// --- KEY BUFFER CHEATS ---
let keyBuffer = "";
document.addEventListener("keydown", (e) => {
  if (e.key.length === 1) keyBuffer += e.key.toLowerCase();
  if (keyBuffer.length > 20) keyBuffer = keyBuffer.slice(-20);

  if (keyBuffer.endsWith("88mph")) {
    document.body.classList.remove("speed-mode");
    void document.body.offsetWidth;            // restart animation
    document.body.classList.add("speed-mode");
    showToast("🔥 88 MILES PER HOUR! TEMPORAL DISPLACEMENT ACTIVATED", "#ff8c00");
    keyBuffer = "";
  }
  if (keyBuffer.endsWith("flux")) {
    document.body.classList.toggle("flux-mode");
    showToast(document.body.classList.contains("flux-mode")
      ? "⚡ FLUX CAPACITOR... FLUXING" : "Flux capacitor disengaged", "#4dc8ff");
    keyBuffer = "";
  }
  if (keyBuffer.endsWith("1955")) {
    document.body.classList.toggle("retro-mode");
    showToast(document.body.classList.contains("retro-mode")
      ? "📻 WELCOME TO 1955. Watch out for your parents." : "Back to the future.", "#ff8c00");
    keyBuffer = "";
  }
  if (keyBuffer.endsWith("biff")) {
    showToast("👊 Hello? Hello? Anybody home? Think, McFly, THINK!", "#e03616");
    keyBuffer = "";
  }
});

// --- KONAMI CODE ---
const KONAMI = ["ArrowUp","ArrowUp","ArrowDown","ArrowDown","ArrowLeft","ArrowRight","ArrowLeft","ArrowRight","b","a"];
let kPos = 0;
document.addEventListener("keydown", (e) => {
  if (e.key === KONAMI[kPos]) {
    if (++kPos === KONAMI.length) {
      showToast("🌟 GREAT SCOTT! 1.21 GIGAWATTS! 🌟", "#39ff14");
      const c = document.querySelector(".container");
      c.style.transition = "transform 1s"; c.style.transform = "rotate(360deg)";
      setTimeout(() => { c.style.transform = "none"; }, 1000);
      kPos = 0;
    }
  } else { kPos = 0; }
});

// --- LIGHTNING (click title) ---
document.getElementById("main-title").addEventListener("click", () => {
  document.body.classList.remove("lightning");
  void document.body.offsetWidth;
  document.body.classList.add("lightning");
  showToast("⛈️ CLOCK TOWER STRUCK — 1.21 GW DELIVERED", "#f5f5f5");
});

// --- ICON WIGGLE (click car) ---
document.getElementById("main-icon").addEventListener("click", () => {
  showToast("🚗 If my calculations are correct... you'll see some serious 💩", "#4dc8ff");
});
```

- [ ] **Step 3: Verify in browser**

Reload `http://localhost:8000`. Checklist:
- Console shows the DeLorean ASCII art and hint.
- Type `88mph` → orange toast + one-shot fire-trail sweep on cards. Type it again → repeats.
- Type `flux` → toast + blue pulsing card borders and blue title; type `flux` again → mode off, "disengaged" toast.
- Type `1955` → sepia page; again → normal.
- Type `biff` → insult toast.
- Konami code (↑↑↓↓←→←→ b a) → toast + container spin, page settles back to normal.
- Click the title → brightness lightning flash + toast. Click the car → toast.
- Toasts never stack; the newest replaces the old and disappears after ~5s.
- No console errors.

- [ ] **Step 4: Commit**

```bash
cd ~/Desktop/Academy
git add index.html
git commit -m "feat: add BTTF easter eggs to hub (88mph, flux, 1955, biff, konami, lightning)

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 3: `linux-trainer.html` — Linux Command Trainer

**Files:**
- Create: `linux-trainer.html`

**Interfaces:**
- Consumes: nothing (fully standalone). Hub already links here from Task 1.
- Produces: nothing consumed later.

- [ ] **Step 1: Write the page**

Green-on-black CRT theme, framed as the DeLorean's onboard computer. Full skeleton with **all** the data and logic:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Linux Command Trainer — McFly Academy</title>
<link rel="icon" href="data:image/svg+xml,<svg xmlns=%22http://www.w3.org/2000/svg%22 viewBox=%220 0 100 100%22><text y=%22.9em%22 font-size=%2290%22>🖥️</text></svg>">
<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Share+Tech+Mono&display=swap" rel="stylesheet">
<style>
  body {
    font-family: 'Share Tech Mono', monospace;
    background: linear-gradient(135deg, #0d0d10 0%, #0a1a0a 100%);
    min-height: 100vh; display: flex; justify-content: center; align-items: flex-start;
    padding: 30px 20px; color: #39ff14; margin: 0;
  }
  .console {
    width: 100%; max-width: 760px; background: #050805;
    border: 2px solid #39ff14; border-radius: 14px; padding: 30px;
    box-shadow: 0 0 40px rgba(57,255,20,0.15), inset 0 0 60px rgba(0,0,0,0.8);
  }
  /* subtle CRT scanlines */
  .console { background-image: repeating-linear-gradient(0deg, rgba(57,255,20,0.03) 0 1px, transparent 1px 3px); }
  .header-row { display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid #1f7a1f; padding-bottom: 14px; margin-bottom: 20px; }
  h1 { font-family: 'Orbitron', sans-serif; font-size: 1.3em; margin: 0; letter-spacing: 2px; text-shadow: 0 0 8px rgba(57,255,20,0.6); }
  .home-btn { color: #a8adb8; text-decoration: none; border: 1px solid #3a3e46; padding: 6px 14px; border-radius: 8px; transition: all 0.2s; font-size: 0.9em; }
  .home-btn:hover { color: #39ff14; border-color: #39ff14; }
  .scoreboard { display: flex; gap: 24px; justify-content: center; margin-bottom: 18px; font-size: 0.95em; color: #a8adb8; }
  .scoreboard b { color: #39ff14; }
  .task-box { background: #0a120a; border: 1px solid #1f7a1f; border-radius: 10px; padding: 18px; margin-bottom: 16px; min-height: 48px; font-size: 1.05em; line-height: 1.5; }
  .prompt-row { display: flex; align-items: center; gap: 8px; background: #000; border: 1px solid #1f7a1f; border-radius: 8px; padding: 10px 14px; }
  .prompt-row .ps1 { color: #ff8c00; white-space: nowrap; }
  #cmd-input { flex: 1; background: transparent; border: none; outline: none; color: #39ff14; font-family: inherit; font-size: 1.05em; caret-color: #39ff14; }
  #feedback { margin-top: 16px; min-height: 70px; padding: 12px; border-radius: 8px; line-height: 1.5; }
  #feedback.ok  { background: rgba(57,255,20,0.08); border: 1px solid #1f7a1f; color: #39ff14; }
  #feedback.bad { background: rgba(224,54,22,0.10); border: 1px solid #7a1d0c; color: #ff9c8a; }
  .btn-row { margin-top: 16px; display: flex; gap: 10px; }
  button { font-family: inherit; background: #0e3d0e; color: #39ff14; border: 1px solid #39ff14; border-radius: 8px; padding: 10px 18px; cursor: pointer; font-size: 0.95em; }
  button:hover { background: #1f7a1f; }
  button:disabled { opacity: 0.4; cursor: default; }
</style>
</head>
<body>
<div class="console">
  <div class="header-row">
    <h1>🖥️ ONBOARD COMPUTER — COMMAND TRAINER</h1>
    <a href="index.html" class="home-btn">← Hub</a>
  </div>
  <div class="scoreboard">
    <span>SCORE <b id="score">0</b></span>
    <span>STREAK <b id="streak">0</b></span>
    <span>QUESTION <b id="qnum">1</b>/<b id="qtotal">?</b></span>
  </div>
  <div class="task-box" id="task-text"></div>
  <div class="prompt-row">
    <span class="ps1">marty@delorean:~$</span>
    <input id="cmd-input" autocomplete="off" spellcheck="false" autofocus>
  </div>
  <div id="feedback"></div>
  <div class="btn-row">
    <button id="submit-btn">Execute ⏎</button>
    <button id="skip-btn">Show Answer</button>
  </div>
</div>

<script>
// Each question: task text, accepted answer variants (compared after normalization),
// canonical answer to display, and a one-line explanation.
const QUESTIONS = [
  { task: "Print the full path of the directory you're currently in.",
    accept: ["pwd"], answer: "pwd", why: "pwd = print working directory." },
  { task: "List ALL files in the current directory, including hidden ones.",
    accept: ["ls -a", "ls -la", "ls -al", "ls --all", "ls -A"], answer: "ls -a",
    why: "-a shows dotfiles; -la adds the long listing." },
  { task: "Jump straight to your home directory.",
    accept: ["cd", "cd ~", "cd $HOME"], answer: "cd ~", why: "cd with no argument (or ~) goes home." },
  { task: "Make the file script.sh executable.",
    accept: ["chmod +x script.sh", "chmod u+x script.sh", "chmod a+x script.sh"],
    answer: "chmod +x script.sh", why: "chmod +x adds the execute bit." },
  { task: "Create a directory named timeline.",
    accept: ["mkdir timeline"], answer: "mkdir timeline", why: "mkdir makes directories." },
  { task: "Create nested directories 1985/hill-valley in one command.",
    accept: ["mkdir -p 1985/hill-valley"], answer: "mkdir -p 1985/hill-valley",
    why: "-p creates parent directories as needed." },
  { task: "Copy plutonium.txt to a new file named backup.txt.",
    accept: ["cp plutonium.txt backup.txt"], answer: "cp plutonium.txt backup.txt",
    why: "cp source destination." },
  { task: "Move draft.txt into the docs directory.",
    accept: ["mv draft.txt docs/", "mv draft.txt docs"], answer: "mv draft.txt docs/",
    why: "mv moves (or renames) files." },
  { task: "Delete the file oldnews.txt.",
    accept: ["rm oldnews.txt"], answer: "rm oldnews.txt", why: "rm removes files — no trash, no undo." },
  { task: "Delete the directory junk and everything inside it.",
    accept: ["rm -r junk", "rm -rf junk", "rm -r junk/", "rm -rf junk/"], answer: "rm -r junk",
    why: "-r recurses into directories; -f skips prompts." },
  { task: "Display the entire contents of notes.txt in the terminal.",
    accept: ["cat notes.txt"], answer: "cat notes.txt", why: "cat concatenates files to stdout." },
  { task: "Show only the FIRST 5 lines of log.txt.",
    accept: ["head -5 log.txt", "head -n 5 log.txt", "head -n5 log.txt"], answer: "head -n 5 log.txt",
    why: "head shows the top of a file; -n sets the line count." },
  { task: "Show only the LAST 5 lines of log.txt.",
    accept: ["tail -5 log.txt", "tail -n 5 log.txt", "tail -n5 log.txt"], answer: "tail -n 5 log.txt",
    why: "tail shows the end of a file." },
  { task: "Watch log.txt live as new lines are appended.",
    accept: ["tail -f log.txt"], answer: "tail -f log.txt", why: "-f follows the file as it grows." },
  { task: "Find all files under /home owned by the user marty.",
    accept: ["find /home -user marty"], answer: "find /home -user marty",
    why: "find <path> -user <name> filters by owner." },
  { task: "Search cars.txt for lines containing the text delorean.",
    accept: ["grep delorean cars.txt", "grep \"delorean\" cars.txt", "grep 'delorean' cars.txt"],
    answer: "grep delorean cars.txt", why: "grep pattern file." },
  { task: "Search parts.txt for the text flux, ignoring upper/lower case.",
    accept: ["grep -i flux parts.txt", "grep -i \"flux\" parts.txt", "grep -i 'flux' parts.txt"],
    answer: "grep -i flux parts.txt", why: "-i = case-insensitive." },
  { task: "Show ALL running processes on the system (BSD style).",
    accept: ["ps aux", "ps -ef"], answer: "ps aux", why: "ps aux (BSD) or ps -ef (System V)." },
  { task: "Politely terminate the process with PID 1985.",
    accept: ["kill 1985"], answer: "kill 1985", why: "kill sends SIGTERM by default." },
  { task: "Force-kill the unresponsive process with PID 1955.",
    accept: ["kill -9 1955", "kill -KILL 1955", "kill -SIGKILL 1955"], answer: "kill -9 1955",
    why: "-9 = SIGKILL, cannot be caught or ignored." },
  { task: "Create a new user account named biff (you have admin rights).",
    accept: ["sudo useradd biff", "useradd biff", "sudo adduser biff", "adduser biff"],
    answer: "sudo useradd biff", why: "useradd is the low-level tool; adduser is the friendly wrapper." },
  { task: "Set a new password for the user marty (as admin).",
    accept: ["sudo passwd marty", "passwd marty"], answer: "sudo passwd marty",
    why: "passwd <user> changes that user's password." },
  { task: "Add marty to the wheel group WITHOUT removing his other groups.",
    accept: ["sudo usermod -ag wheel marty", "usermod -ag wheel marty", "sudo usermod -a -g wheel marty", "usermod -a -g wheel marty"],
    answer: "sudo usermod -aG wheel marty", why: "-aG appends a supplementary group; forgetting -a REPLACES groups." },
  { task: "Show disk space usage for all filesystems in human-readable units.",
    accept: ["df -h"], answer: "df -h", why: "df = disk free; -h = human-readable (G/M/K)." },
  { task: "Show a long listing so you can read each file's permissions.",
    accept: ["ls -l", "ls -la", "ls -al"], answer: "ls -l", why: "The first column of ls -l is the permission string." },
  { task: "Print the username you are currently logged in as.",
    accept: ["whoami"], answer: "whoami", why: "whoami prints the effective user." },
];

let order = [...QUESTIONS].sort(() => Math.random() - 0.5);
let idx = 0, score = 0, streak = 0, answered = false;

// Normalize: trim, collapse runs of whitespace, lowercase.
// (Real Linux is case-sensitive, but lowercasing forgives shift-key slips;
// every accept-variant is already lowercase.)
const norm = s => s.trim().replace(/\s+/g, " ").toLowerCase();

function render() {
  document.getElementById("task-text").textContent = order[idx].task;
  document.getElementById("qnum").textContent = idx + 1;
  document.getElementById("qtotal").textContent = order.length;
  document.getElementById("score").textContent = score;
  document.getElementById("streak").textContent = streak;
  const f = document.getElementById("feedback");
  f.className = ""; f.textContent = "";
  const inp = document.getElementById("cmd-input");
  inp.value = ""; inp.focus();
  document.getElementById("submit-btn").textContent = "Execute ⏎";
  answered = false;
}

function next() { idx = (idx + 1) % order.length; if (idx === 0) order.sort(() => Math.random() - 0.5); render(); }

function submit() {
  if (answered) { next(); return; }          // second press = next question
  const q = order[idx];
  const given = norm(document.getElementById("cmd-input").value);
  const f = document.getElementById("feedback");

  // Easter egg
  if (given === "sudo make me a sandwich") { f.className = "ok"; f.textContent = "🥪 Okay. (xkcd 149 — with great power comes great sandwiches.)"; return; }
  if (given === "") { f.className = "bad"; f.textContent = "Type a command first, McFly."; return; }

  if (q.accept.map(norm).includes(given)) {
    score++; streak++;
    f.className = "ok";
    f.textContent = `✔ CORRECT — ${q.why}` + (streak >= 3 ? `  🔥 ${streak} streak!` : "");
  } else {
    streak = 0;
    f.className = "bad";
    f.textContent = `✘ Not quite. Expected: ${q.answer}\n${q.why}`;
  }
  document.getElementById("score").textContent = score;
  document.getElementById("streak").textContent = streak;
  document.getElementById("submit-btn").textContent = "Next ▶";
  answered = true;
}

document.getElementById("submit-btn").addEventListener("click", submit);
document.getElementById("cmd-input").addEventListener("keydown", e => { if (e.key === "Enter") submit(); });
document.getElementById("skip-btn").addEventListener("click", () => {
  const q = order[idx], f = document.getElementById("feedback");
  streak = 0;
  f.className = "bad"; f.textContent = `Answer: ${q.answer}\n${q.why}`;
  document.getElementById("streak").textContent = streak;
  document.getElementById("submit-btn").textContent = "Next ▶";
  answered = true;
});

render();
</script>
</body>
</html>
```

Note on the `usermod` question: accepted variants are matched **after lowercasing**, so `-aG` from the student normalizes to `-ag` and matches the lowercase variant in `accept`. The displayed canonical answer keeps the correct `-aG` capitalization.

- [ ] **Step 2: Verify in browser**

Open `http://localhost:8000/linux-trainer.html`. Checklist:
- CRT green console renders with scanlines; `← Hub` returns to the hub.
- A random task appears; question counter shows `1/26`.
- Correct answer (`pwd` etc.) → green ✔ + explanation, score and streak increment; button flips to "Next ▶"; Enter or click advances.
- Variant answers accepted: for the hidden-files task, try `ls -la` and `LS -A` (uppercase forgiven).
- Wrong answer → red ✘ showing the expected command; streak resets to 0.
- Empty submit → "Type a command first, McFly." and the question does not advance.
- "Show Answer" reveals the answer, kills the streak, advances on next press.
- 3 correct in a row → 🔥 streak flair.
- Type `sudo make me a sandwich` → xkcd toast-line, question not consumed.
- Complete 26 questions → deck reshuffles, counter wraps to 1/26, no crash.
- No console errors.

- [ ] **Step 3: Commit**

```bash
cd ~/Desktop/Academy
git add linux-trainer.html
git commit -m "feat: add Linux Command Trainer with 26-question drill bank

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 4: `hash-lab.html` — Hash Lab

**Files:**
- Create: `hash-lab.html`

**Interfaces:**
- Consumes: nothing (standalone). Requires secure context for `crypto.subtle` — localhost or the Pages HTTPS URL.
- Produces: nothing consumed later.

- [ ] **Step 1: Write the page**

Dark security-lab theme with flux-blue (`#4dc8ff`) accents and red (`#e03616`) alerts. Three panels stacked: (1) hashing vs. encoding side-by-side, (2) password auditor, (3) hash-match challenge. Reuse the page scaffolding conventions from the Global Constraints (fonts link, favicon 🔐, `.home-btn` linking to `index.html`, body gradient `#0d0d10`→`#1a1c22`). Layout CSS is at the implementer's discretion within the palette; the logic below is required verbatim:

```html
<!-- Panel 1 markup -->
<div class="panel">
  <h2>Hashing ≠ Encoding</h2>
  <input id="ht-input" placeholder="Type anything..." autocomplete="off">
  <div class="duo">
    <div class="half">
      <h3>SHA-256 (one-way hash)</h3>
      <code id="sha-out">—</code>
      <p>Fixed 64 hex chars, no matter the input. Cannot be reversed.</p>
    </div>
    <div class="half">
      <h3>Base64 (reversible encoding)</h3>
      <code id="b64-out">—</code>
      <p>Grows with input. Anyone can decode it — this is NOT security.</p>
    </div>
  </div>
</div>

<!-- Panel 2 markup -->
<div class="panel">
  <h2>Password Auditor</h2>
  <input id="pw-input" placeholder="Test a password (nothing is sent anywhere)" autocomplete="off">
  <div id="pw-verdict">—</div>
  <div id="pw-time">—</div>
</div>

<!-- Panel 3 markup -->
<div class="panel">
  <h2>Hash Match Challenge</h2>
  <p>One of these words produced the hash below. Which one?</p>
  <code id="challenge-hash">—</code>
  <div id="word-buttons"></div>
  <div id="challenge-result"></div>
  <button id="new-challenge">New Challenge 🔁</button>
</div>
```

```javascript
// --- SHARED: SHA-256 hex via Web Crypto ---
async function sha256Hex(text) {
  const buf = await crypto.subtle.digest("SHA-256", new TextEncoder().encode(text));
  return [...new Uint8Array(buf)].map(b => b.toString(16).padStart(2, "0")).join("");
}

// --- PANEL 1: live hash vs. encode ---
document.getElementById("ht-input").addEventListener("input", async (e) => {
  const v = e.target.value;
  document.getElementById("sha-out").textContent = v ? await sha256Hex(v) : "—";
  // btoa fails on non-Latin1 chars (emoji etc.) — encode to UTF-8 bytes first
  document.getElementById("b64-out").textContent =
    v ? btoa(String.fromCharCode(...new TextEncoder().encode(v))) : "—";
});

// --- PANEL 2: password auditor ---
// Entropy estimate: charset size ^ length, at 10 billion guesses/second (offline GPU attack).
function auditPassword(pw) {
  if (!pw) return { verdict: "—", time: "—", color: "#a8adb8" };
  let charset = 0;
  if (/[a-z]/.test(pw)) charset += 26;
  if (/[A-Z]/.test(pw)) charset += 26;
  if (/[0-9]/.test(pw)) charset += 10;
  if (/[^a-zA-Z0-9]/.test(pw)) charset += 33;
  const seconds = Math.pow(charset, pw.length) / 1e10;

  const COMMON = ["password","123456","letmein","qwerty","monkey","dragon","baseball","111111","abc123","biff"];
  if (COMMON.includes(pw.toLowerCase()))
    return { verdict: "☠️ IT'S IN EVERY WORDLIST. Biff doesn't even need to guess.", time: "instant", color: "#e03616" };

  const t = seconds < 1 ? "instantly"
    : seconds < 60 ? `${Math.ceil(seconds)} seconds`
    : seconds < 3600 ? `${Math.ceil(seconds/60)} minutes`
    : seconds < 86400 ? `${Math.ceil(seconds/3600)} hours`
    : seconds < 31557600 ? `${Math.ceil(seconds/86400)} days`
    : seconds < 3.16e10 ? `${Math.ceil(seconds/31557600)} years`
    : "longer than the age of the universe";

  if (seconds < 60)        return { verdict: "☠️ Biff would guess this in 2 seconds.", time: `Crack time: ${t}`, color: "#e03616" };
  if (seconds < 86400)     return { verdict: "⚠️ Crackable over lunch. Add length and symbols.", time: `Crack time: ${t}`, color: "#ff8c00" };
  if (seconds < 3.16e9)    return { verdict: "🙂 Decent — but longer is stronger.", time: `Crack time: ${t}`, color: "#4dc8ff" };
  return { verdict: "🛡️ GREAT SCOTT! Even Doc's supercomputer gives up.", time: `Crack time: ${t}`, color: "#39ff14" };
}
document.getElementById("pw-input").addEventListener("input", (e) => {
  const r = auditPassword(e.target.value);
  const v = document.getElementById("pw-verdict"), t = document.getElementById("pw-time");
  v.textContent = r.verdict; v.style.color = r.color; t.textContent = r.time;
});

// --- PANEL 3: hash match challenge ---
const WORDLIST = ["delorean","plutonium","hoverboard","almanac","gigawatt","clocktower","hillvalley","fluxcapacitor","biffco","einstein","calvin","tannen"];
let challengeWord = "";
async function newChallenge() {
  const pool = [...WORDLIST].sort(() => Math.random() - 0.5).slice(0, 4);
  challengeWord = pool[Math.floor(Math.random() * pool.length)];
  document.getElementById("challenge-hash").textContent = await sha256Hex(challengeWord);
  document.getElementById("challenge-result").textContent = "";
  const box = document.getElementById("word-buttons");
  box.innerHTML = "";
  pool.forEach(w => {
    const b = document.createElement("button");
    b.textContent = w;
    b.addEventListener("click", async () => {
      const r = document.getElementById("challenge-result");
      if (w === challengeWord) { r.textContent = `✔ Correct — sha256("${w}") matches. Same input, same hash, every time.`; r.style.color = "#39ff14"; }
      else { r.textContent = `✘ Nope — sha256("${w}") starts ${(await sha256Hex(w)).slice(0,12)}… which doesn't match.`; r.style.color = "#e03616"; }
    });
    box.appendChild(b);
  });
}
document.getElementById("new-challenge").addEventListener("click", newChallenge);
newChallenge();

// --- EASTER EGG: hashing the word "password" anywhere in panel 1 ---
document.getElementById("ht-input").addEventListener("input", (e) => {
  if (e.target.value.toLowerCase() === "password")
    document.getElementById("pw-verdict").textContent = "👀 Really? REALLY, McFly?";
});
```

- [ ] **Step 2: Verify in browser**

Open `http://localhost:8000/hash-lab.html`. Checklist:
- Typing in panel 1 updates both outputs live. `hello` → SHA-256 starts `2cf24dba5fb0`; Base64 is `aGVsbG8=`. Emoji input does not throw (UTF-8-safe btoa path).
- Clearing the input resets both outputs to `—`.
- Password auditor: `123456` → wordlist verdict, red; `Summer25` → lunch-crackable tier; a 16+ char mixed passphrase → green "GREAT SCOTT" tier. Verdict colors change accordingly.
- Hash challenge: 4 word buttons; clicking the right one → green confirmation; wrong one → red with the mismatching hash prefix; "New Challenge" reshuffles.
- Typing exactly `password` in panel 1 → "Really? REALLY, McFly?" line.
- `← Hub` returns to the hub. No console errors.

- [ ] **Step 3: Commit**

```bash
cd ~/Desktop/Academy
git add hash-lab.html
git commit -m "feat: add Hash Lab — hashing vs encoding, password auditor, hash challenge

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 5: `vm-console.html` — VM Console ("Hill Valley Cloud Services")

**Files:**
- Create: `vm-console.html`

**Interfaces:**
- Consumes: nothing (standalone). All state in-memory; refresh resets (accepted in spec).
- Produces: nothing consumed later.

- [ ] **Step 1: Write the page**

Cloud-portal theme: steel panels (`#2a2d33`), flux-blue (`#4dc8ff`) accents, "HILL VALLEY CLOUD SERVICES ☁️" header with `.home-btn`. Favicon ☁️. Three panels: deploy form + VM list, per-VM service manager, SRM quiz. Required markup skeleton and complete logic:

```html
<div class="panel">
  <h2>Deploy a Virtual Machine</h2>
  <input id="vm-name" placeholder="vm name (lowercase, digits, dashes)" autocomplete="off">
  <select id="vm-os">
    <option>Ubuntu 24.04</option><option>Windows Server 2025</option><option>Rocky Linux 9</option>
  </select>
  <select id="vm-size">
    <option value="S">S — 1 vCPU / 1 GB</option><option value="M">M — 2 vCPU / 4 GB</option><option value="L">L — 4 vCPU / 16 GB</option>
  </select>
  <button id="deploy-btn">Deploy 🚀</button>
  <div id="deploy-error"></div>
  <div id="vm-list"></div>
</div>

<div class="panel">
  <h2>Service Manager</h2>
  <p>Select a running VM above, then manage its services. Actions have consequences.</p>
  <div id="svc-vm-label">No VM selected.</div>
  <div id="svc-list"></div>
  <div id="status-page"></div>
</div>

<div class="panel">
  <h2>Shared Responsibility Model — Who Owns It?</h2>
  <div id="srm-duty"></div>
  <button id="srm-provider">Cloud Provider 🏢</button>
  <button id="srm-customer">Customer (You) 🧑‍💻</button>
  <div id="srm-result"></div>
  <div id="srm-score"></div>
</div>
```

```javascript
// --- STATE ---
let vms = [];          // {id, name, os, size, state: 'pending'|'running'|'stopped', services: {web,ssh,dns: bool}}
let nextId = 1;
let selectedVm = null;

// --- DEPLOY ---
document.getElementById("deploy-btn").addEventListener("click", () => {
  const name = document.getElementById("vm-name").value.trim().toLowerCase();
  const err = document.getElementById("deploy-error");
  if (!/^[a-z][a-z0-9-]{1,29}$/.test(name)) { err.textContent = "⚠️ Name must start with a letter; lowercase letters, digits, dashes only (2–30 chars)."; return; }
  if (vms.some(v => v.name === name)) { err.textContent = "⚠️ A VM with that name already exists."; return; }
  err.textContent = "";
  const vm = { id: nextId++, name, os: document.getElementById("vm-os").value,
               size: document.getElementById("vm-size").value, state: "pending",
               services: { web: true, ssh: true, dns: true } };
  vms.push(vm);
  renderVms();
  // Simulated provisioning: pending -> running after 3s
  setTimeout(() => { vm.state = "running"; renderVms(); renderServices(); }, 3000);
  document.getElementById("vm-name").value = "";

  // EASTER EGG: deploying a VM named "delorean"
  if (name === "delorean") {
    document.body.classList.remove("speed-mode");
    void document.body.offsetWidth;
    document.body.classList.add("speed-mode");   // reuse the fire-trail keyframes from the hub, copied into this page's CSS
  }
});

// --- VM LIST (state dot: pending=amber pulse, running=green, stopped=gray) ---
function renderVms() {
  const box = document.getElementById("vm-list");
  box.innerHTML = vms.length ? "" : "<p class='muted'>No VMs deployed yet.</p>";
  vms.forEach(vm => {
    const row = document.createElement("div");
    row.className = "vm-row" + (selectedVm === vm ? " selected" : "");
    row.innerHTML = `<span class="dot ${vm.state}"></span>
      <b>${vm.name}</b> <span class="muted">${vm.os} · ${vm.size}</span>
      <span class="state">${vm.state.toUpperCase()}</span>`;
    const btns = document.createElement("span");
    if (vm.state === "running") btns.append(mkBtn("Stop", () => { vm.state = "stopped"; refresh(); }),
                                            mkBtn("Select", () => { selectedVm = vm; refresh(); }));
    if (vm.state === "stopped") btns.append(mkBtn("Start", () => { vm.state = "pending"; renderVms();
                                              setTimeout(() => { vm.state = "running"; refresh(); }, 1500); }),
                                            mkBtn("Delete", () => { vms = vms.filter(v => v !== vm);
                                              if (selectedVm === vm) selectedVm = null; refresh(); }));
    row.appendChild(btns);
    box.appendChild(row);
  });
}
function mkBtn(label, fn) { const b = document.createElement("button"); b.textContent = label; b.className = "mini"; b.addEventListener("click", fn); return b; }
function refresh() { renderVms(); renderServices(); }

// --- SERVICES (only on the selected, running VM) ---
const SVC_META = { web: "web (nginx) — serves the status page", ssh: "ssh (sshd) — remote admin access", dns: "dns (named) — name resolution" };
function renderServices() {
  const label = document.getElementById("svc-vm-label");
  const list = document.getElementById("svc-list");
  const page = document.getElementById("status-page");
  list.innerHTML = "";
  if (!selectedVm || selectedVm.state !== "running") {
    label.textContent = "No running VM selected."; page.innerHTML = ""; return;
  }
  label.textContent = `Managing: ${selectedVm.name}`;
  Object.keys(SVC_META).forEach(svc => {
    const on = selectedVm.services[svc];
    const row = document.createElement("div");
    row.className = "svc-row";
    row.innerHTML = `<span class="dot ${on ? "running" : "stopped"}"></span> ${SVC_META[svc]} `;
    row.append(
      mkBtn(on ? "Stop" : "Start", () => { selectedVm.services[svc] = !on; renderServices(); }),
      mkBtn("Restart", () => { selectedVm.services[svc] = false; renderServices();
                               setTimeout(() => { if (selectedVm) { selectedVm.services[svc] = true; renderServices(); } }, 1200); })
    );
    list.appendChild(row);
  });
  // CONSEQUENCE: the fake status page reflects the web service
  page.innerHTML = selectedVm.services.web
    ? `<div class="site-up">🌐 http://${selectedVm.name}.hillvalley.cloud — <b>200 OK</b> — "Save the clock tower!"</div>`
    : `<div class="site-down">🌐 http://${selectedVm.name}.hillvalley.cloud — <b>CONNECTION REFUSED</b> — the web service is down.</div>`;
}

// --- SRM QUIZ ---
const SRM = [
  { duty: "Physical security of the datacenter", owner: "provider", why: "You never touch the building. The provider guards racks, power, and cooling." },
  { duty: "Patching the guest OS on your IaaS VM", owner: "customer", why: "Inside the VM is yours — OS patches, packages, and configs are on you." },
  { duty: "Configuring security-group / firewall rules for your VM", owner: "customer", why: "You define what traffic reaches your workloads." },
  { duty: "Maintaining the physical hypervisor hosts", owner: "provider", why: "The virtualization layer and hardware beneath it belong to the provider." },
  { duty: "Classifying and protecting the data you upload", owner: "customer", why: "Your data is ALWAYS your responsibility, in every service model." },
  { duty: "Replacing failed disk drives", owner: "provider", why: "Hardware failures are invisible to you — the provider swaps parts." },
  { duty: "Managing IAM users and their permissions in your account", owner: "customer", why: "Who in YOUR org can do what is your call and your risk." },
  { duty: "Keeping the region's network backbone online", owner: "provider", why: "The provider owns connectivity between its datacenters." },
  { duty: "Enabling MFA on your admin accounts", owner: "customer", why: "Account hygiene inside your tenant is customer-side." },
  { duty: "Patching the hypervisor software itself", owner: "provider", why: "You can't even see the hypervisor — the provider patches it." },
];
let srmIdx = 0, srmScore = 0, srmSeen = 0;
let srmOrder = [...SRM].sort(() => Math.random() - 0.5);
function renderSrm() { document.getElementById("srm-duty").textContent = `"${srmOrder[srmIdx].duty}"`; document.getElementById("srm-result").textContent = ""; }
function answerSrm(choice) {
  const q = srmOrder[srmIdx], r = document.getElementById("srm-result");
  srmSeen++;
  if (choice === q.owner) { srmScore++; r.textContent = `✔ Right — ${q.why}`; r.style.color = "#39ff14"; }
  else { r.textContent = `✘ It's the ${q.owner}. ${q.why}`; r.style.color = "#e03616"; }
  document.getElementById("srm-score").textContent = `Score: ${srmScore}/${srmSeen}`;
  srmIdx = (srmIdx + 1) % srmOrder.length;
  if (srmIdx === 0) srmOrder.sort(() => Math.random() - 0.5);
  setTimeout(renderSrm, 2600);
}
document.getElementById("srm-provider").addEventListener("click", () => answerSrm("provider"));
document.getElementById("srm-customer").addEventListener("click", () => answerSrm("customer"));

renderVms(); renderServices(); renderSrm();
```

Also copy the `fire-trail` keyframes + `.speed-mode` rule from Task 2's CSS into this page's `<style>` (pages are self-contained — no sharing). Style `.dot.pending` as amber `#ff8c00` with a pulse animation, `.dot.running` green `#39ff14`, `.dot.stopped` gray `#555`; `.site-up` green-tinted panel, `.site-down` red-tinted panel.

- [ ] **Step 2: Verify in browser**

Open `http://localhost:8000/vm-console.html`. Checklist:
- Deploy with a bad name (`88vm`, `UPPER`, one char) → inline warning, nothing deployed. Duplicate name → warning.
- Valid deploy → row appears PENDING with pulsing amber dot; flips to RUNNING (green) after ~3s.
- Stop → STOPPED (gray) with Start/Delete buttons; Start → PENDING → RUNNING; Delete removes the row (and clears the service panel if it was selected).
- Select a running VM → Service Manager lists web/ssh/dns with green dots; status page shows `200 OK`.
- Stop `web` → status page flips to CONNECTION REFUSED; Start → back to 200 OK. Restart → dot goes gray then green ~1.2s later.
- SRM quiz: answering updates score `n/m`, shows the explanation, auto-advances after ~2.6s; deck reshuffles after 10 questions.
- Deploy a VM named `delorean` → fire-trail sweep.
- `← Hub` returns to hub. No console errors.

- [ ] **Step 3: Commit**

```bash
cd ~/Desktop/Academy
git add vm-console.html
git commit -m "feat: add VM Console — deploys, service management, SRM quiz

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 6: Publish and final verification

**Files:**
- Modify: `README.md` (tool list)

**Interfaces:**
- Consumes: everything above. GitHub Pages is already enabled on `main` root; publishing = pushing.

- [ ] **Step 1: Update README tool list**

Add under the Structure section of `README.md`:

```markdown
## Tools

| Tool | File | What it teaches |
|------|------|-----------------|
| Linux Command Trainer | `linux-trainer.html` | Files, permissions, users, processes — type-the-command drills |
| Hash Lab | `hash-lab.html` | Hashing vs. encoding, password strength, hash matching |
| VM Console | `vm-console.html` | VM lifecycle, service management, Shared Responsibility Model |
```

- [ ] **Step 2: Full local pass**

With the local server running, walk the entire site once: hub renders → every hub easter egg fires (88mph, flux, 1955, biff, konami, title-click lightning) → each of the three cards opens its tool → each tool's Task-step-2 checklist passes → each `← Hub` returns. Check at phone width (~390px): every page usable, no horizontal scroll.

- [ ] **Step 3: Commit and push**

```bash
cd ~/Desktop/Academy
git add README.md
git commit -m "docs: add tool table to README

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
git push
```

- [ ] **Step 4: Verify the live site**

Wait ~2 minutes for Pages to build, then:

Run: `curl -s -o /dev/null -w "%{http_code}" https://mcflyatcc.github.io/Academy/`
Expected: `200`

Open `https://mcflyatcc.github.io/Academy/` in a browser and spot-check: hub loads, one easter egg, each tool opens, **Hash Lab hashing works** (confirms Web Crypto on HTTPS). Done — success criteria from the spec are met.

---

## Future tools (not in this plan)

Each coming-soon card (Filesystem Navigator, CIA Triad Trainer, Active Directory Sim) and the future Networking/CCNA section gets its own spec → plan cycle. The recipe never changes: write one self-contained HTML file, flip its hub card from `coming-soon` to live, commit, push.
