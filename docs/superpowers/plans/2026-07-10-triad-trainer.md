# CIA Triad Trainer Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add the CIA Triad Trainer — a scenario-classification drill for Confidentiality / Integrity / Availability — as the second live tool in McFly Academy's Security section.

**Architecture:** One new self-contained page (`triad-trainer.html`, inline CSS/JS, no external deps beyond Google Fonts) plus converting the existing "CIA Triad Trainer" coming-soon card on `index.html` into a live card. Drill logic mirrors `linux-trainer.html` (shuffled bank, score/streak, wrap-and-reshuffle); page chrome mirrors `hash-lab.html` (wrap/header/panel styling).

**Tech Stack:** Plain HTML5, CSS, vanilla JavaScript. Google Fonts (Orbitron, Share Tech Mono) only external request.

**Spec:** `docs/superpowers/specs/2026-07-10-triad-trainer-design.md`

## Global Constraints

- Repo: `/Users/mattmccullough/Desktop/Academy`, branch `main`, remote `McFlyATCC/Academy`. **Never touch `~/Desktop/DrGopher9-Academy`** — read-only reference.
- Zero-build: no package.json, no build tools, no test framework. Verification is manual via the checklists in each task.
- Page fully self-contained: inline `<style>` and `<script>`. Only external requests: Google Fonts.
- Shared BTTF palette: background `#0d0d10`→`#1a1c22`; panels `#2a2d33`, borders `#3a3e46`; orange `#ff8c00`; red `#e03616`; flux blue `#4dc8ff`; terminal green `#39ff14`; text `#f5f5f5` / `#a8adb8`.
- Pillar accent mapping (used consistently in explainer cards AND answer buttons): Confidentiality = `#4dc8ff`, Integrity = `#39ff14`, Availability = `#ff8c00`. Page title accent = security red `#e03616`.
- Fonts link on the page: `<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Share+Tech+Mono&display=swap" rel="stylesheet">`.
- Home button in header: `<a href="index.html" class="home-btn">← Hub</a>`.
- Preview: `cd ~/Desktop/Academy && python3 -m http.server 8000`, open `http://localhost:8000/triad-trainer.html`. (No Web Crypto on this page, but keep the localhost habit.)
- Commit after every task with trailer: `Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>`

## File Structure

```
Academy/
├── triad-trainer.html   # NEW — explainer strip + scenario drill (Task 1)
├── index.html           # MODIFIED — coming-soon card → live card (Task 2)
└── README.md            # MODIFIED — tool table row (Task 3)
```

---

### Task 1: Create `triad-trainer.html`

**Files:**
- Create: `triad-trainer.html`

**Interfaces:**
- Consumes: nothing (standalone page).
- Produces: the page linked from `index.html` by exact filename `triad-trainer.html` (Task 2 depends on this name).

- [ ] **Step 1: Write `triad-trainer.html`**

Full page below — copy verbatim, then sanity-read once:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>CIA Triad Trainer — McFly Academy</title>
<link rel="icon" href="data:image/svg+xml,<svg xmlns=%22http://www.w3.org/2000/svg%22 viewBox=%220 0 100 100%22><text y=%22.9em%22 font-size=%2290%22>🛡️</text></svg>">
<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Share+Tech+Mono&display=swap" rel="stylesheet">
<style>
  * { box-sizing: border-box; }
  body {
    font-family: 'Segoe UI', Tahoma, sans-serif;
    background: linear-gradient(135deg, #0d0d10 0%, #1a1c22 100%);
    min-height: 100vh; margin: 0; color: #f5f5f5;
    display: flex; justify-content: center;
    padding: 24px 16px 60px;
  }
  .wrap { width: 100%; max-width: 760px; }
  header {
    display: flex; justify-content: space-between; align-items: center;
    border-bottom: 1px solid #3a3e46; padding-bottom: 16px; margin-bottom: 22px;
    flex-wrap: wrap; gap: 10px;
  }
  h1 {
    font-family: 'Orbitron', sans-serif; font-size: 1.35em; margin: 0;
    letter-spacing: 1px; color: #f5f5f5; text-shadow: 0 0 10px rgba(224,54,22,0.5);
  }
  h1 span { color: #e03616; }
  .home-btn {
    color: #a8adb8; text-decoration: none; border: 1px solid #3a3e46;
    padding: 6px 14px; border-radius: 8px; font-size: 0.9em; transition: all 0.2s; white-space: nowrap;
  }
  .home-btn:hover { color: #e03616; border-color: #e03616; }

  /* --- PILLAR EXPLAINER STRIP --- */
  .pillars { display: flex; gap: 14px; margin-bottom: 20px; flex-wrap: wrap; }
  .pillar {
    flex: 1 1 200px; min-width: 0; background: #2a2d33; border: 1px solid #3a3e46;
    border-top: 3px solid #3a3e46; border-radius: 10px; padding: 14px 16px;
  }
  .pillar h3 { font-family: 'Orbitron', sans-serif; font-size: 0.9em; margin: 0 0 6px; letter-spacing: 0.5px; }
  .pillar p { color: #a8adb8; font-size: 0.82em; line-height: 1.45; margin: 0 0 8px; }
  .pillar .controls { font-family: 'Share Tech Mono', monospace; font-size: 0.75em; color: #6b7080; line-height: 1.5; }
  .pillar-c { border-top-color: #4dc8ff; } .pillar-c h3 { color: #4dc8ff; }
  .pillar-i { border-top-color: #39ff14; } .pillar-i h3 { color: #39ff14; }
  .pillar-a { border-top-color: #ff8c00; } .pillar-a h3 { color: #ff8c00; }

  /* --- DRILL PANEL --- */
  .panel { background: #2a2d33; border: 1px solid #3a3e46; border-radius: 12px; padding: 22px; }
  .panel h2 { font-family: 'Orbitron', sans-serif; font-size: 1.05em; margin: 0 0 14px; color: #e03616; letter-spacing: 0.5px; }
  .scoreboard {
    display: flex; gap: 24px; justify-content: center; margin-bottom: 16px;
    font-size: 0.95em; color: #a8adb8; font-family: 'Share Tech Mono', monospace; flex-wrap: wrap;
  }
  .scoreboard b { color: #e03616; }
  .scenario-box {
    background: #16171b; border: 1px solid #3a3e46; border-radius: 10px;
    padding: 18px; margin-bottom: 16px; min-height: 64px; font-size: 1.05em; line-height: 1.55;
  }
  .answer-row { display: flex; gap: 12px; flex-wrap: wrap; }
  .answer-btn {
    flex: 1 1 160px; font-family: 'Orbitron', sans-serif; font-size: 0.85em; letter-spacing: 0.5px;
    background: #16171b; border: 1px solid #3a3e46; border-radius: 10px; padding: 14px 10px;
    cursor: pointer; transition: all 0.15s; color: #f5f5f5;
  }
  .answer-btn small { display: block; font-family: 'Share Tech Mono', monospace; color: #6b7080; margin-top: 4px; font-size: 0.8em; }
  #btn-c { border-color: #4dc8ff; } #btn-c:hover:not(:disabled) { background: #4dc8ff; color: #0d0d10; }
  #btn-i { border-color: #39ff14; } #btn-i:hover:not(:disabled) { background: #39ff14; color: #0d0d10; }
  #btn-a { border-color: #ff8c00; } #btn-a:hover:not(:disabled) { background: #ff8c00; color: #0d0d10; }
  .answer-btn:disabled { opacity: 0.45; cursor: default; }
  /* .reveal must come AFTER :disabled so the correct button stays lit once answered */
  .answer-btn.reveal { opacity: 1; }
  #btn-c.reveal { box-shadow: 0 0 16px rgba(77,200,255,0.6); }
  #btn-i.reveal { box-shadow: 0 0 16px rgba(57,255,20,0.6); }
  #btn-a.reveal { box-shadow: 0 0 16px rgba(255,140,0,0.6); }

  #feedback { margin-top: 16px; min-height: 60px; padding: 12px 14px; border-radius: 8px; line-height: 1.5; font-size: 0.95em; }
  #feedback.ok  { background: rgba(57,255,20,0.08); border: 1px solid #1f7a1f; color: #39ff14; }
  #feedback.bad { background: rgba(224,54,22,0.10); border: 1px solid #7a1d0c; color: #ff9c8a; }

  #next-btn {
    margin-top: 14px; font-family: 'Share Tech Mono', monospace; background: #16171b;
    color: #f5f5f5; border: 1px solid #3a3e46; border-radius: 8px; padding: 10px 18px;
    cursor: pointer; font-size: 0.95em; visibility: hidden;
  }
  #next-btn:hover { background: #3a3e46; }

  footer { text-align: center; color: #6b7080; font-size: 0.8em; margin-top: 10px; }

  @media (max-width: 480px) {
    .pillars, .answer-row { flex-direction: column; }
    h1 { font-size: 1.1em; }
  }
</style>
</head>
<body>
<div class="wrap">
  <header>
    <h1>🛡️ <span>CIA TRIAD TRAINER</span></h1>
    <a href="index.html" class="home-btn">← Hub</a>
  </header>

  <div class="pillars">
    <div class="pillar pillar-c">
      <h3>CONFIDENTIALITY</h3>
      <p>Only authorized people can see the data. Broken when secrets get exposed.</p>
      <div class="controls">encryption · access control · least privilege</div>
    </div>
    <div class="pillar pillar-i">
      <h3>INTEGRITY</h3>
      <p>Data stays accurate and unaltered. Broken when information is tampered with.</p>
      <div class="controls">hashing · signatures · checksums · version control</div>
    </div>
    <div class="pillar pillar-a">
      <h3>AVAILABILITY</h3>
      <p>Systems and data are there when needed. Broken when access is lost.</p>
      <div class="controls">backups · RAID · redundancy · UPS</div>
    </div>
  </div>

  <div class="panel">
    <h2>Incident Drill — Name the Pillar</h2>
    <div class="scoreboard">
      <span>SCORE <b id="score">0</b></span>
      <span>STREAK <b id="streak">0</b></span>
      <span>SCENARIO <b id="qnum">1</b>/<b id="qtotal">?</b></span>
    </div>
    <div class="scenario-box" id="scenario-text"></div>
    <div class="answer-row">
      <button class="answer-btn" id="btn-c" data-pillar="C">CONFIDENTIALITY<small>press 1</small></button>
      <button class="answer-btn" id="btn-i" data-pillar="I">INTEGRITY<small>press 2</small></button>
      <button class="answer-btn" id="btn-a" data-pillar="A">AVAILABILITY<small>press 3</small></button>
    </div>
    <div id="feedback"></div>
    <button id="next-btn">Next ▶ (Enter)</button>
  </div>

  <footer>Which side of the triad took the hit? Everything runs locally in your browser.</footer>
</div>

<script>
const PILLAR_NAMES = { C: "Confidentiality", I: "Integrity", A: "Availability" };

// Each scenario has exactly ONE primary pillar; the why line names secondary
// impacts where relevant. 10 × C, 10 × I, 10 × A.
const SCENARIOS = [
  // --- CONFIDENTIALITY ---
  { text: "Biff shoulder-surfs Marty's PIN as he logs in at the Café 80's kiosk.",
    answer: "C", why: "A secret (the PIN) was exposed to someone unauthorized. Privacy screens and shielding the keypad help." },
  { text: "A USB drive holding unencrypted student records is lost in the Hill Valley High parking lot.",
    answer: "C", why: "Anyone who finds the drive can read the records. Encryption at rest would have contained the loss." },
  { text: "Doc emails the flux capacitor schematics to the wrong recipient.",
    answer: "C", why: "Sensitive data was disclosed to an unauthorized party — accidents count too." },
  { text: "An attacker on Lou's Café free Wi-Fi sniffs unencrypted traffic and captures customer logins.",
    answer: "C", why: "Data in transit was readable. TLS or a VPN prevents eavesdropping." },
  { text: "A Hill Valley Telegraph clerk looks up a celebrity's records with no business reason.",
    answer: "C", why: "Having system access doesn't mean need-to-know. Least privilege and audit logs address insider snooping." },
  { text: "Griff guesses Jennifer's weak password and browses her private cloud photos.",
    answer: "C", why: "Unauthorized viewing of private data. Strong passwords and MFA protect accounts." },
  { text: "A misconfigured cloud storage bucket lets anyone on the internet read the payroll files.",
    answer: "C", why: "Data exposed to the world through broken access control." },
  { text: "Old office computers from the clock tower are donated without wiping the drives.",
    answer: "C", why: "Recoverable data leaves with the hardware. Sanitize media before disposal." },
  { text: "Spyware on the school office PC quietly uploads copies of staff documents to an attacker.",
    answer: "C", why: "Exfiltration — nothing was changed or taken offline, the data was silently copied out." },
  { text: "Biff taps the McFly phone line and records private conversations.",
    answer: "C", why: "Interception and eavesdropping expose private information." },

  // --- INTEGRITY ---
  { text: "Biff logs into the gradebook and quietly changes his D to an A.",
    answer: "I", why: "Data was altered without authorization. Access controls and audit trails protect integrity." },
  { text: "During an online bank transfer, a man-in-the-middle changes the destination account number.",
    answer: "I", why: "Data modified in transit. Digital signatures and TLS integrity checks catch tampering." },
  { text: "Malware silently corrupts rows in the sports almanac database — scores no longer match reality.",
    answer: "I", why: "The data is still online and still 'secret' — but it can no longer be trusted." },
  { text: "Hill Valley High's homepage is defaced with a fake announcement.",
    answer: "I", why: "Unauthorized modification of content. (Availability suffers too if the real page is gone, but the core violation is tampering.)" },
  { text: "A downloaded installer's SHA-256 doesn't match the hash published by the vendor.",
    answer: "I", why: "The file changed somewhere between vendor and you — exactly what checksums exist to detect." },
  { text: "An employee overwrites this year's inventory sheet with last year's copy by mistake.",
    answer: "I", why: "Accuracy was lost — integrity violations don't require an attacker. Version control and validation help." },
  { text: "Someone edits Doc's lab notebook entries and forges the timestamps.",
    answer: "I", why: "Tampered records can no longer be trusted as a source of truth." },
  { text: "An attacker sends a forged memo that looks like it came from the principal, with altered instructions.",
    answer: "I", why: "Authenticity of origin and content is part of integrity. Digital signatures counter forgery." },
  { text: "A file transferred over a flaky link arrives with garbled bytes — and nothing checked for errors.",
    answer: "I", why: "Accidental corruption still breaks trustworthiness. Checksums and CRCs detect it." },
  { text: "An intruder edits the security logs to erase evidence of the break-in.",
    answer: "I", why: "Tampered logs can't be trusted. Write-once storage and log forwarding protect them." },

  // --- AVAILABILITY ---
  { text: "A DDoS flood knocks the Hill Valley Telegraph's web server offline.",
    answer: "A", why: "Legitimate users can't reach the service. Rate limiting, CDNs, and DDoS protection mitigate this." },
  { text: "Lightning hits the clock tower and the power surge fries the only web server — no UPS, no spare.",
    answer: "A", why: "A single point of failure took the service down. Redundancy and surge protection are availability controls." },
  { text: "Ransomware encrypts the school's shared drive and there are no backups — all work stops.",
    answer: "A", why: "Primarily Availability — nobody can use the files. (Integrity is also hit, since the files were altered.) Backups are the #1 defense." },
  { text: "A backhoe cuts the fiber line to city hall; every office loses internet for a day.",
    answer: "A", why: "Physical infrastructure failure. Redundant links keep services reachable." },
  { text: "An admin accidentally drops the production database; the restore takes two days.",
    answer: "A", why: "The data will come back, but for two days nobody can work. Tested backups shrink that window." },
  { text: "The ticketing server's disk fills up and the app stops accepting new requests.",
    answer: "A", why: "Resource exhaustion — no attacker needed. Monitoring and capacity planning prevent it." },
  { text: "Nobody renewed the TLS certificate, and browsers now refuse to load the store's site.",
    answer: "A", why: "The service is effectively down for its users. Expiry monitoring is an availability control." },
  { text: "A botched update leaves every register at the Café 80's unbootable during the lunch rush.",
    answer: "A", why: "Systems are down exactly when needed. Staged rollouts and rollback plans reduce the blast radius." },
  { text: "A sprinkler mishap soaks the server room and the domain controllers power off.",
    answer: "A", why: "Environmental damage takes systems offline. Environmental controls and offsite redundancy help." },
  { text: "An attacker deliberately fails logins on every staff account until lockout policy freezes them all.",
    answer: "A", why: "The accounts' data is intact and secret — but nobody can get in. Denial of service via account lockout." },
];

let order = [...SCENARIOS].sort(() => Math.random() - 0.5);
let idx = 0, score = 0, streak = 0, answered = false;

const $ = id => document.getElementById(id);
const buttons = [...document.querySelectorAll(".answer-btn")];

function render() {
  $("scenario-text").textContent = order[idx].text;
  $("qnum").textContent = idx + 1;
  $("qtotal").textContent = order.length;
  $("score").textContent = score;
  $("streak").textContent = streak;
  const f = $("feedback");
  f.className = ""; f.textContent = "";
  buttons.forEach(b => { b.disabled = false; b.classList.remove("reveal"); });
  $("next-btn").style.visibility = "hidden";
  answered = false;
}

function answer(pillar) {
  if (answered) return;
  answered = true;
  const s = order[idx];
  const f = $("feedback");
  buttons.forEach(b => { b.disabled = true; if (b.dataset.pillar === s.answer) b.classList.add("reveal"); });
  if (pillar === s.answer) {
    score++; streak++;
    f.className = "ok";
    f.textContent = `✔ CORRECT — ${s.why}` + (streak >= 3 ? `  🔥 ${streak} streak!` : "");
  } else {
    streak = 0;
    f.className = "bad";
    f.textContent = `✘ Not quite — this one is ${PILLAR_NAMES[s.answer]}. ${s.why}`;
  }
  $("score").textContent = score;
  $("streak").textContent = streak;
  $("next-btn").style.visibility = "visible";
}

function next() {
  if (!answered) return;
  idx = (idx + 1) % order.length;
  if (idx === 0) order.sort(() => Math.random() - 0.5);
  render();
}

buttons.forEach(b => b.addEventListener("click", () => answer(b.dataset.pillar)));
$("next-btn").addEventListener("click", next);
document.addEventListener("keydown", e => {
  if (e.key === "1") answer("C");
  else if (e.key === "2") answer("I");
  else if (e.key === "3") answer("A");
  else if (e.key === "Enter" && answered) next();
});

render();
</script>
</body>
</html>
```

- [ ] **Step 2: Verify in browser**

Run: `cd ~/Desktop/Academy && python3 -m http.server 8000` (background), open `http://localhost:8000/triad-trainer.html`.

Checklist:
- Three pillar cards render with blue/green/orange top borders; drill panel below with scoreboard reading `SCORE 0 / STREAK 0 / SCENARIO 1/30`.
- A scenario is shown; clicking the correct pillar → green ✔ feedback with why line, SCORE and STREAK increment, correct button glows, all three buttons disabled, Next visible.
- Clicking a wrong pillar → red ✘ feedback naming the correct pillar, STREAK resets to 0, correct button glows.
- Keys 1/2/3 answer; Enter advances only after answering; pressing 1/2/3 again after answering does nothing.
- Three correct in a row → 🔥 streak message.
- Click through 30 scenarios: counter wraps back to 1/30 with a reshuffled order, no console errors.
- Narrow the window below 480px: pillars and answer buttons stack vertically, nothing overflows.
- `← Hub` link returns to `index.html`.

- [ ] **Step 3: Commit**

```bash
cd ~/Desktop/Academy
git add triad-trainer.html
git commit -m "feat: add CIA Triad Trainer — scenario classification drill

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 2: Convert the hub's coming-soon card to a live card

**Files:**
- Modify: `index.html` (the Security section's coming-soon card, ~line 165)

**Interfaces:**
- Consumes: `triad-trainer.html` (exact filename, Task 1) and existing hub CSS classes `.feature-card`, `.cat-security`, `.btn-main`, `.btn-security`.
- Produces: live hub card linking to the new tool.

- [ ] **Step 1: Replace the coming-soon card**

In `index.html`, find:

```html
    <div class="feature-card coming-soon">
      <h2>CIA Triad Trainer</h2>
      <p>Confidentiality, Integrity, Availability drills. Destination not yet set.</p>
      <span class="led-unset">-- -- ---- --:--</span>
    </div>
```

Replace with:

```html
    <div class="feature-card cat-security">
      <h2>CIA Triad Trainer</h2>
      <p>Read the incident, name the pillar — Confidentiality, Integrity, or Availability. Don't let Biff win.</p>
      <a href="triad-trainer.html" class="btn-main btn-security">Run Drills 🛡️</a>
    </div>
```

- [ ] **Step 2: Verify in browser**

Open `http://localhost:8000/` (server from Task 1).

Checklist:
- Security section shows TWO live cards (Hash Lab, CIA Triad Trainer), both with red top border and working red buttons; the other sections' coming-soon cards are unchanged.
- `Run Drills 🛡️` navigates to the trainer; its `← Hub` returns.
- No console errors on the hub (easter eggs / clock still work).

- [ ] **Step 3: Commit**

```bash
cd ~/Desktop/Academy
git add index.html
git commit -m "feat: light up CIA Triad Trainer card on the hub

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 3: README row, push, live verification

**Files:**
- Modify: `README.md` (tools table)

**Interfaces:**
- Consumes: tools table added in the v1 docs commit.
- Produces: published site with the new tool live.

- [ ] **Step 1: Add the tool table row**

In `README.md`, after the `| VM Console | ... |` row, add:

```markdown
| CIA Triad Trainer | `triad-trainer.html` | Classifying incidents as confidentiality, integrity, or availability violations |
```

- [ ] **Step 2: Commit and push**

```bash
cd ~/Desktop/Academy
git add README.md
git commit -m "docs: add CIA Triad Trainer to README tool table

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
git push
```

- [ ] **Step 3: Verify live (GitHub Pages)**

Pages deploys take a minute or two. Then:

```bash
curl -s -o /dev/null -w "%{http_code}" https://mcflyatcc.github.io/Academy/triad-trainer.html
```

Expected: `200`. Then load `https://mcflyatcc.github.io/Academy/` in a browser: hub card live, trainer drill works end-to-end (answer, streak, next, wrap not required — one full answer cycle is enough).
