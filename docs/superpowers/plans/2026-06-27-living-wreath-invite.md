# The Living Wreath — Invite Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking. This is a single self-contained visual artifact; "verification" means serving the file and checking behavior in a browser, not unit tests.

**Goal:** Replace `public/index.html` with a ground-up "Living Wreath" garden wedding invite built around the SS WaxSeal monogram, deploying on Vercel exactly as today.

**Architecture:** One self-contained `public/index.html` — inline `<style>`, inline `<script>`, the monogram embedded as a base64 data URI, fonts from Google Fonts. A full-screen Opening (the blooming seal) that, on tap, reveals a vertical scroll-story of seven scenes. Vanilla JS for the bloom-open transition and the live countdown. No framework, no build step.

**Tech Stack:** HTML5, CSS (custom properties, keyframe animation, `mix-blend-mode`), vanilla JS, Google Fonts (Great Vibes, Cormorant Garamond, Outfit). Local preview via `npx serve public -l 3456`.

## Global Constraints

- Single file: everything lives in `public/index.html`. No external assets except Google Fonts.
- Deploy unchanged: `vercel.json` keeps `outputDirectory: public`. No new config/tooling/deps.
- Mobile-first and viewport-locked (no pinch-zoom / overscroll); must still center legibly on desktop.
- Embed the real seal: `SS-Monogram-WaxSeal.png` as a base64 data URI (no network image request).
- Palette tokens (verbatim): `--cream:#faf6ef` `--cream2:#f2eee2` `--blush:#e3c4c0` `--blush-l:#f0dcd8` `--sage:#c2cdac` `--sage-d:#7f8d63` `--gold:#b08a4f` `--gold-l:#cdae78` `--ink:#33312b` `--soft:#6f6a5f`.
- Fonts: Great Vibes (names/signature), Cormorant Garamond (display/verse), Outfit (labels/UI).
- Countdown target: `2027-05-28T18:00:00` ("until the celebrations begin").
- RSVP button is a styled placeholder (`href="#"`) — links nowhere.
- Content is fixed (see spec table); no venue/time/location/map. Copy must match the spec verbatim.
- Honor `prefers-reduced-motion`: disable drift/parallax, keep all content legible and reachable.
- Source of truth for layout/style/copy: the approved spec (`docs/superpowers/specs/2026-06-27-living-wreath-wedding-invite-design.md`) and the committed brainstorm walkthrough.

---

### Task 1: Scaffold — document shell, tokens, fonts, embedded seal

**Files:**
- Create: `public/index.html` (new file, replacing the old invite wholesale)
- Reference asset: `/Users/sohabwaheed/Documents/Claude/Projects/Wedding Planning Sohab/SS-Monogram-WaxSeal.png`

**Interfaces:**
- Produces: a CSS `:root` token block; a reusable `.seal` element backed by an embedded data URI (CSS var `--seal-url` or an `<img>` whose `src` is the data URI); the four `@import`-free Google Fonts `<link>`; a fixed viewport meta. Later tasks consume `--seal-url`, the tokens, and the font families.

- [ ] **Step 1: Generate the base64 data URI for the seal into a temp file** (do not paste the base64 into the plan or chat — pipe it straight into the build)

```bash
SEAL="/Users/sohabwaheed/Documents/Claude/Projects/Wedding Planning Sohab/SS-Monogram-WaxSeal.png"
OUT="/private/tmp/claude-501/-Users-sohabwaheed-Library-CloudStorage-OneDrive-Personal-Wedding-Invites/51ddd22b-c6a7-473a-a3d6-2ec17df35765/scratchpad/seal-datauri.txt"
printf 'data:image/png;base64,' > "$OUT"
base64 -i "$SEAL" | tr -d '\n' >> "$OUT"
wc -c "$OUT"   # expect ~122KB
```

- [ ] **Step 2: Write the `<head>` shell** — charset, locked viewport, title, font links, and the `:root` tokens. The seal data URI from Step 1 is injected as `--seal-url: url("data:image/png;base64,…")` inside `:root` (build step concatenates it; never hand-typed).

```html
<!DOCTYPE html><html lang="en"><head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Sarah & Sohab — You're Invited</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,500;0,600;1,300;1,400;1,500&family=Great+Vibes&family=Outfit:wght@200;300;400;500;600&display=swap" rel="stylesheet">
<style>
  :root{
    --cream:#faf6ef;--cream2:#f2eee2;--blush:#e3c4c0;--blush-l:#f0dcd8;
    --sage:#c2cdac;--sage-d:#7f8d63;--gold:#b08a4f;--gold-l:#cdae78;
    --ink:#33312b;--soft:#6f6a5f;--line:rgba(120,108,84,.2);
    --script:'Great Vibes',cursive;--disp:'Cormorant Garamond',serif;--sans:'Outfit',sans-serif;
    --seal-url:url("PASTE_DATA_URI");
  }
  *{margin:0;padding:0;box-sizing:border-box}
  html{height:100%}
  body{font-family:var(--sans);color:var(--ink);height:100%;
    -webkit-tap-highlight-color:transparent;user-select:none;-webkit-user-select:none;
    background:radial-gradient(900px 500px at 50% -8%,#fffdf9,rgba(255,255,255,0) 60%),linear-gradient(170deg,#eef0e6,#f3efe6 60%,#efe7da);}
  .seal{background:var(--seal-url) center/contain no-repeat;mix-blend-mode:multiply}
</style></head>
```

- [ ] **Step 3: Build the file** by substituting the data URI placeholder, then add a minimal `<body>` containing one centered `.seal` (200px square) so the embed is visually confirmable.

```bash
DIR="/Users/sohabwaheed/Library/CloudStorage/OneDrive-Personal/Wedding Invites/wedding-invite"
# (Use the editor to write the file; replace PASTE_DATA_URI with the contents of seal-datauri.txt)
```

- [ ] **Step 4: Verify it loads and the seal renders** — serve and view.

```bash
cd "/Users/sohabwaheed/Library/CloudStorage/OneDrive-Personal/Wedding Invites/wedding-invite"
npx serve public -l 3456
```
Expected: page at `http://localhost:3456` shows the SS seal centered on a warm garden background, fonts loaded, no console errors, page does not scroll/zoom.

- [ ] **Step 5: Commit**

```bash
git add public/index.html
git commit -m "feat(invite): scaffold Living Wreath shell — tokens, fonts, embedded seal"
```

---

### Task 2: Opening scene — the blooming seal (the gate)

**Files:**
- Modify: `public/index.html` (replace the placeholder body with `#openScreen`)

**Interfaces:**
- Produces: `#openScreen` (a full-viewport fixed layer, `z-index:200`) containing the hero `.seal`, the "tap" prompt, and drifting petals; an `#openSeal` tap target. Task 3 consumes `#openScreen` and `#openSeal` to drive the transition.

- [ ] **Step 1: Add the opening markup** inside `<body>`.

```html
<div id="openScreen">
  <div class="petals"><span class="petal p1"></span><span class="petal p2"></span><span class="petal p3"></span><span class="petal p4"></span></div>
  <p class="invited">You are invited to our special day</p>
  <div id="openSeal" class="seal hero" role="button" aria-label="Open the invitation"></div>
  <p class="tap">Tap the seal to open</p>
</div>
```

- [ ] **Step 2: Style the opening** — full-screen daylight garden radial, hero seal ~min(70vw,300px), `breathe` pulse, `draw-in` fade/scale on load, pulsing gold "tap" label, and drifting petals (blush + sage, `fall` keyframes). Use the palette tokens. Match the walkthrough's Opening scene proportions.

```css
#openScreen{position:fixed;inset:0;z-index:200;display:flex;flex-direction:column;
  align-items:center;justify-content:center;overflow:hidden;
  background:radial-gradient(460px 380px at 50% 42%,#fffdf8 0%,#f3efe2 55%,#e7ead9 100%)}
.seal.hero{width:min(70vw,300px);height:min(70vw,300px);cursor:pointer;
  opacity:0;animation:drawIn 1.1s .2s forwards, breathe 6s 1.3s ease-in-out infinite}
.invited{font-family:var(--disp);font-style:italic;font-size:clamp(15px,4.4vw,19px);color:var(--soft);
  margin-bottom:8px;opacity:0;animation:fadeUp 1s .1s forwards}
.tap{margin-top:22px;font-size:11px;letter-spacing:.32em;text-transform:uppercase;color:var(--gold);
  font-weight:500;opacity:0;animation:fadeUp 1s .8s forwards, pulse 2.6s 1.8s ease-in-out infinite}
@keyframes drawIn{from{opacity:0;transform:scale(.92)}to{opacity:1;transform:scale(1)}}
@keyframes breathe{0%,100%{transform:scale(1)}50%{transform:scale(1.035)}}
@keyframes fadeUp{from{opacity:0;transform:translateY(8px)}to{opacity:1;transform:translateY(0)}}
@keyframes pulse{0%,100%{opacity:.45}50%{opacity:1}}
.petal{position:absolute;width:9px;height:9px;border-radius:50% 0 50% 50%;background:var(--blush-l);opacity:.8}
.p1{left:20%;top:14%;animation:fall 9s linear infinite}
.p2{left:72%;top:10%;background:var(--sage);animation:fall 11s linear infinite 2s}
.p3{left:48%;top:20%;animation:fall 8s linear infinite 1s}
.p4{left:84%;top:34%;background:var(--blush);animation:fall 12s linear infinite 3s}
@keyframes fall{0%{transform:translateY(0) rotate(0);opacity:0}10%{opacity:.85}100%{transform:translateY(70vh) rotate(220deg);opacity:0}}
```

- [ ] **Step 3: Verify** — reload `http://localhost:3456`. Expected: seal draws in and breathes, "Tap the seal to open" pulses, petals drift downward, daylight garden fills the screen, nothing scrolls.

- [ ] **Step 4: Commit**

```bash
git add public/index.html
git commit -m "feat(invite): opening bloom scene — breathing seal, drifting petals"
```

---

### Task 3: Bloom-to-open transition + scroll-story container

**Files:**
- Modify: `public/index.html` (add `#invite` container + transition JS)

**Interfaces:**
- Consumes: `#openScreen`, `#openSeal` (Task 2).
- Produces: `#invite` (the scrollable story wrapper, hidden until opened) and a `.scene` block convention (each scene = one full-bleed section). Tasks 4–6 append `.scene` sections into `#invite`. Produces JS function `openInvite()` bound to `#openSeal` click.

- [ ] **Step 1: Add the scroll container** after `#openScreen`.

```html
<main id="invite" aria-hidden="true"><!-- scenes appended in Tasks 4–6 --></main>
```

- [ ] **Step 2: Style the open/reveal states** — `#invite` hidden (opacity 0, not scrollable) until `body.opened`; on open, `#openScreen` blooms outward (scale + fade + brightness, like light flooding) and `#invite` fades up and becomes the scroll surface.

```css
body.opened{overflow-y:auto;overflow-x:hidden;height:auto}
#invite{opacity:0;transition:opacity 1.1s ease .35s}
body.opened #invite{opacity:1}
#openScreen{transition:opacity .9s ease, transform 1.1s cubic-bezier(.2,.7,.2,1), filter .9s ease}
body.opened #openScreen{opacity:0;transform:scale(1.7);filter:brightness(1.6) blur(2px);pointer-events:none}
.scene{position:relative;min-height:100vh;display:flex;flex-direction:column;align-items:center;
  justify-content:center;text-align:center;padding:64px 30px;overflow:hidden}
```

- [ ] **Step 3: Wire the tap** — add the script before `</body>`.

```html
<script>
  function openInvite(){
    if(document.body.classList.contains('opened'))return;
    document.body.classList.add('opened');
    document.getElementById('invite').setAttribute('aria-hidden','false');
    window.scrollTo(0,0);
  }
  document.getElementById('openSeal').addEventListener('click',openInvite);
</script>
```

- [ ] **Step 4: Verify** — reload, tap the seal. Expected: the opening blooms/brightens away, the (currently empty) invite area fades in and the page becomes vertically scrollable. Add a temporary colored `.scene` to confirm scroll, then remove it.

- [ ] **Step 5: Commit**

```bash
git add public/index.html
git commit -m "feat(invite): bloom-to-open transition reveals scroll-story"
```

---

### Task 4: Scenes 02–03 — The Couple + The Dates

**Files:**
- Modify: `public/index.html` (append two `.scene` sections in `#invite`)

**Interfaces:**
- Consumes: `.scene` convention, tokens, `.seal`.
- Produces: scene markup; a shared `.orn` ornamental divider and `.lbl` label class reused by later scenes.

- [ ] **Step 1: Append the Couple scene** (verbatim copy).

```html
<section class="scene s-couple">
  <div class="seal sm"></div>
  <p class="pre">Together with their families, and with the blessings of Allah</p>
  <h1 class="names">Sarah <span class="amp">&amp;</span> Sohab</h1>
  <p class="post">invite you to celebrate their wedding</p>
  <div class="orn"><span></span><i></i><span></span></div>
</section>
```

- [ ] **Step 2: Append the Dates scene** (verbatim copy).

```html
<section class="scene s-dates">
  <p class="lbl">Save the dates</p>
  <h2 class="dhead">Two days of celebration</h2>
  <div class="dcard"><div class="ev">Mehndi</div><div class="dt">May 28, 2027</div><div class="dy">Friday</div></div>
  <div class="dcard"><div class="ev">Shaadi</div><div class="dt">May 29, 2027</div><div class="dy">Saturday</div></div>
</section>
```

- [ ] **Step 3: Style both scenes + shared classes** — `.seal.sm` (~80px), `.names` in Great Vibes clamp(54px,16vw,76px) with `.amp` in gold, `.pre`/`.post` in Cormorant, `.dcard` cream cards with hairline border + soft shadow, `.ev` script gold, `.dt` Cormorant ink, `.dy` Outfit uppercase. `.orn` = two hairline gradient rules flanking a 7px rotated gold square. `.lbl` = 10.5px gold uppercase tracking .34em. Backgrounds: faint cream→sage / sage washes per the walkthrough.

- [ ] **Step 4: Verify** — open the invite, scroll. Expected: "Sarah & Sohab" in script with the prelude/post lines; then the two date plaques with correct dates and weekdays. Copy matches the spec exactly.

- [ ] **Step 5: Commit**

```bash
git add public/index.html
git commit -m "feat(invite): couple + dates scenes"
```

---

### Task 5: Scene 04 — Countdown (live ticker)

**Files:**
- Modify: `public/index.html` (append countdown scene + extend the `<script>`)

**Interfaces:**
- Consumes: `.scene`, `.lbl`, tokens.
- Produces: `#cdD #cdH #cdM #cdS` value nodes; JS `tick()` on a 1s interval driving them from target `2027-05-28T18:00:00`.

- [ ] **Step 1: Append the countdown scene.**

```html
<section class="scene s-cd">
  <p class="lbl">The wait</p>
  <h2 class="dhead">Until we celebrate</h2>
  <div class="cdrow">
    <div class="cdbox"><b id="cdD">--</b><span>Days</span></div>
    <div class="cdbox"><b id="cdH">--</b><span>Hrs</span></div>
    <div class="cdbox"><b id="cdM">--</b><span>Min</span></div>
    <div class="cdbox"><b id="cdS">--</b><span>Sec</span></div>
  </div>
  <p class="until">until the celebrations begin</p>
</section>
```

- [ ] **Step 2: Add the countdown JS** (extend the existing script).

```js
const TARGET=new Date('2027-05-28T18:00:00').getTime();
function pad(n){return String(n).padStart(2,'0')}
function tick(){
  const d=TARGET-Date.now();
  if(d<=0){['cdD','cdH','cdM','cdS'].forEach(id=>document.getElementById(id).textContent='00');return}
  document.getElementById('cdD').textContent=Math.floor(d/86400000);
  document.getElementById('cdH').textContent=pad(Math.floor(d/3600000)%24);
  document.getElementById('cdM').textContent=pad(Math.floor(d/60000)%60);
  document.getElementById('cdS').textContent=pad(Math.floor(d/1000)%60);
}
tick();setInterval(tick,1000);
```

- [ ] **Step 3: Style** — `.cdrow` flex of four `.cdbox` cream cards (border + radius), `.cdbox b` Cormorant ~28px ink, label 8.5px Outfit uppercase, `.until` Cormorant italic soft.

- [ ] **Step 4: Verify** — scroll to the countdown. Expected: four boxes showing a sensible Days/Hrs/Min/Sec to 28 May 2027, the seconds visibly ticking each second.

- [ ] **Step 5: Commit**

```bash
git add public/index.html
git commit -m "feat(invite): live countdown to the celebrations"
```

---

### Task 6: Scenes 05–07 — Dress Code, RSVP (placeholder), Blessing

**Files:**
- Modify: `public/index.html` (append three `.scene` sections)

**Interfaces:**
- Consumes: `.scene`, `.orn`, `.lbl`, `.seal`, tokens.
- Produces: the final three scenes; `.btn` styled RSVP placeholder.

- [ ] **Step 1: Append Dress Code** (both events identical, verbatim).

```html
<section class="scene s-dress">
  <h2 class="dhead">Dress Code</h2>
  <p class="note">Please dress to celebrate with us</p>
  <div class="drow"><div class="ev">Mehndi</div><div class="codes">Formal · Black Tie · Traditional</div></div>
  <div class="drow"><div class="ev">Shaadi</div><div class="codes">Formal · Black Tie · Traditional</div></div>
  <div class="orn"><span></span><i></i><span></span></div>
</section>
```

- [ ] **Step 2: Append RSVP** — button is a placeholder (`href="#"`, no handler).

```html
<section class="scene s-rsvp">
  <h2 class="dhead">Kindly Respond</h2>
  <p class="note">We would be honored by your presence.</p>
  <a class="btn" href="#" aria-label="RSVP (coming soon)">RSVP</a>
  <p class="by">Kindly respond by April 15, 2027</p>
</section>
```

- [ ] **Step 3: Append Blessing** (verse verbatim).

```html
<section class="scene s-verse">
  <div class="seal sm"></div>
  <p class="q">"And among His signs is that He created for you mates from among yourselves, that you may dwell in tranquility with them, and He has put love and mercy between your hearts."</p>
  <p class="src">Quran 30:21</p>
  <p class="sig">Sarah &amp; Sohab</p>
  <p class="url">sarahandsohab.com</p>
</section>
```

- [ ] **Step 4: Style** — `.drow .ev` script gold + `.codes` Cormorant ink; `.btn` gold pill, white uppercase tracking, soft shadow, hover lift; `.by` small soft; `.q` Cormorant italic ~18px max-width ~280px; `.src` gold uppercase; `.sig` Great Vibes ~40px ink; `.url` small soft tracked.

- [ ] **Step 5: Verify** — scroll through. Expected: dress code (both events), RSVP pill (clicking does nothing/stays on page), respond-by date, then the verse + signature + domain. All copy matches the spec exactly.

- [ ] **Step 6: Commit**

```bash
git add public/index.html
git commit -m "feat(invite): dress code, RSVP placeholder, blessing scenes"
```

---

### Task 7: Motion polish, reduced-motion, responsive

**Files:**
- Modify: `public/index.html`

**Interfaces:**
- Consumes: all scenes.
- Produces: scroll-reveal behavior; a `@media (prefers-reduced-motion: reduce)` block; desktop centering rules.

- [ ] **Step 1: Add gentle scroll-reveal** — scenes fade/rise as they enter the viewport via `IntersectionObserver` toggling a `.in` class.

```js
const io=new IntersectionObserver((es)=>es.forEach(e=>{if(e.isIntersecting)e.target.classList.add('in')}),{threshold:.18});
document.querySelectorAll('.scene').forEach(s=>io.observe(s));
```
```css
.scene>*{opacity:0;transform:translateY(14px);transition:opacity .9s ease,transform .9s cubic-bezier(.2,.7,.2,1)}
.scene.in>*{opacity:1;transform:none}
.scene>*:nth-child(2){transition-delay:.08s}.scene>*:nth-child(3){transition-delay:.16s}.scene>*:nth-child(4){transition-delay:.24s}
```

- [ ] **Step 2: Add reduced-motion + desktop rules.**

```css
@media (prefers-reduced-motion: reduce){
  *{animation:none!important}
  .seal.hero{opacity:1}
  .scene>*{opacity:1!important;transform:none!important;transition:none!important}
  .petal{display:none}
}
@media (min-width:900px){
  #invite{max-width:560px;margin:0 auto}
}
```

- [ ] **Step 3: Verify motion** — reload, open, scroll. Expected: each scene settles in gently; petals drift only in the opening. Then emulate reduced motion (DevTools › Rendering › Emulate `prefers-reduced-motion: reduce`) and confirm everything is static, fully visible, and scrollable.

- [ ] **Step 4: Verify responsive** — narrow (~390px) and wide (~1280px) widths: content centered and legible at both; no horizontal scroll.

- [ ] **Step 5: Commit**

```bash
git add public/index.html
git commit -m "feat(invite): scroll-reveal motion, reduced-motion, responsive centering"
```

---

### Task 8: Final pass — content audit + deploy parity

**Files:**
- Modify: `public/index.html` (only if the audit finds issues)

- [ ] **Step 1: Content audit against the spec table** — confirm every item present and verbatim: names, prelude/post, Mehndi May 28 2027 (Fri), Shaadi May 29 2027 (Sat), countdown target, dress code (both events), RSVP-by April 15 2027, Quran 30:21 text, "Sarah & Sohab" signature, sarahandsohab.com. Confirm nothing from the old invite leaked in (no blue palette, no scratch-off, no envelope, no venue).

- [ ] **Step 2: Verify deploy parity** — `vercel.json` still `{"outputDirectory":"public"}`; the file is fully self-contained (only outbound request is Google Fonts); re-serve and confirm a cold load works with no console errors.

```bash
cd "/Users/sohabwaheed/Library/CloudStorage/OneDrive-Personal/Wedding Invites/wedding-invite"
grep -c "outputDirectory" vercel.json   # expect 1
npx serve public -l 3456
```

- [ ] **Step 3: Show the user** the finished invite in the browser for sign-off before any deploy.

- [ ] **Step 4: Commit any audit fixes.**

```bash
git add public/index.html
git commit -m "fix(invite): content audit + deploy parity pass"
```

---

## Self-Review

**Spec coverage:** Opening bloom → Task 2/3. Seven scenes/content → Tasks 2,4,5,6. Countdown target → Task 5. Motion language + reduced-motion → Tasks 2,7. Palette/type/seal-embed → Task 1 + per-scene styling. Single-file/Vercel parity → Tasks 1,8. RSVP placeholder → Task 6. Dates-only / no venue → enforced by content audit (Task 8). All spec sections map to a task.

**Placeholder scan:** No "TBD/TODO" left as work items. The one literal `PASTE_DATA_URI` token is a build-time substitution with an exact command (Task 1, Steps 1–3), not an unspecified gap. RSVP `href="#"` is an intentional product decision, not a plan placeholder.

**Type consistency:** `openInvite()`, `#openSeal`, `#openScreen`, `#invite`, `.scene`/`.scene.in`, `.seal`/`.seal.hero`/`.seal.sm`, countdown ids `#cdD/#cdH/#cdM/#cdS`, and shared `.orn`/`.lbl`/`.dhead`/`.btn` names are used consistently across tasks.
