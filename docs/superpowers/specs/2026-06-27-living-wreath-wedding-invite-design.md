# The Living Wreath — Wedding Invite Redesign

**Date:** 2026-06-27
**Status:** Approved (design) — ready for implementation plan
**Project:** `wedding-invite` (Sarah & Sohab)

## Summary

Replace the current navy/blue scratch-off wedding invitation with a ground-up
reimagining called **The Living Wreath**: a single, luminous *lush romantic
garden* experience built around the couple's redesigned calligraphic **SS**
monogram (the "WaxSeal" variant). The seal's own botanical oval frame is the hero
— it draws itself in, blooms, and opens like a garden gate, then the guest drifts
down through a sequence of garden "clearings" that carry the invitation's content.

This is a **blank-slate concept**, not a restyle. The previous experience
(envelope → name calligraphy → six-card scratch-off → countdown → dress code →
RSVP → verse) and its blue palette are retired. The emotional beats are
re-expressed in a new visual and motion language.

## Goals

- A distinctly new, cohesive *garden* art direction with the new monogram leading.
- Replace the scratch-off reveal with a single, elegant **bloom-to-open** gesture.
- Keep the invitation's real content intact (names, two events, countdown, dress
  code, RSVP, blessing).
- Ship as a single self-contained `index.html` that deploys on Vercel exactly as
  today (static, `public/` output directory). No build pipeline, no new tooling.

## Non-goals (explicitly out of scope)

- **Functional RSVP.** The RSVP button stays a styled placeholder linking nowhere
  (`href="#"`), same as today. Wiring it to a form/service is deferred.
- **Venue / time / location / map.** Decision: *dates + experience only*. No
  "when & where" details, no address, no directions. Mystery is intentional.
- **Content/fact changes.** All facts carry over unchanged (see below).
- Multi-page or framework rewrite. It remains one HTML file.

## Confirmed content (carry over unchanged)

| Element | Value |
|---|---|
| Couple | **Sarah & Sohab** |
| Prelude line | "Together with their families, and with the blessings of Allah" + "invite you to celebrate their wedding" |
| Mehndi | **May 28, 2027** (Friday) |
| Shaadi | **May 29, 2027** (Saturday) |
| Countdown target | May 28, 2027 (Mehndi) — "until the celebrations begin" |
| Dress code | **Formal · Black Tie · Traditional** — for both Mehndi and Shaadi |
| RSVP by | **April 15, 2027** |
| Blessing | **Quran 30:21** — "And among His signs is that He created for you mates from among yourselves, that you may dwell in tranquility with them, and He has put love and mercy between your hearts." |
| Signature / footer | "Sarah & Sohab" · **sarahandsohab.com** |

## Experience flow (7 scenes)

The invite is a single mobile-first vertical experience. Scene 01 is a full-screen
gate; tapping the seal reveals scenes 02–07 as a calm scroll-story of "clearings."

1. **Opening — The Bloom.** Full-screen daylight garden. The monogram's botanical
   line-art draws itself in and the wreath gently "breathes"; soft petals drift.
   Copy: "You are invited to our special day" + "Tap the seal to open." The seal is
   the only interactive target.
2. **The Couple.** Small seal, then **"Sarah & Sohab"** in script, framed by the
   families/blessings prelude and "invite you to celebrate their wedding."
3. **The Dates.** "Two days of celebration" — **Mehndi · May 28, 2027 (Fri)** and
   **Shaadi · May 29, 2027 (Sat)** as two garden plaques/cards.
4. **Countdown.** Live ticker (Days / Hrs / Min / Sec) counting to May 28, 2027,
   captioned "until the celebrations begin."
5. **Dress Code.** "Please dress to celebrate with us" — Mehndi and Shaadi each
   labeled **Formal · Black Tie · Traditional**.
6. **RSVP.** "Kindly Respond" + "We would be honored by your presence." A styled
   **RSVP** button (placeholder) + "Kindly respond by April 15, 2027."
7. **Blessing.** Small seal, **Quran 30:21** in italic serif, the **"Sarah & Sohab"**
   script signature, and **sarahandsohab.com**.

## Motion language

- **Draw-in on load:** botanical line-art (echoing the seal's strokes) animates in;
  the wreath settles and breathes (slow scale pulse).
- **Bloom-to-open:** tapping the seal opens the wreath like a garden gate (frame
  parts / light blooms) and transitions into the scroll-story. This single gesture
  *is* the reveal — it replaces the six-card scratch-off entirely.
- **Drift:** soft petals/leaves drift continuously across scenes, low opacity.
- **On scroll:** foliage parts and each clearing settles with a gentle bloom +
  light parallax. Tone is slow, warm, alive — never flashy or bouncy.
- Respect `prefers-reduced-motion`: reduce drift/parallax to static, keep content
  fully legible and reachable.

## Visual system

**Palette (design tokens):**

- `--cream: #faf6ef`, `--cream2: #f2eee2` (backgrounds / paper)
- `--blush: #e3c4c0`, `--blush-l: #f0dcd8` (soft florals)
- `--sage: #c2cdac`, `--sage-d: #7f8d63` (greenery)
- `--gold: #b08a4f`, `--gold-l: #cdae78` (accent / labels / button)
- `--ink: #33312b`, `--soft: #6f6a5f` (text)

**Type:**

- `Great Vibes` — the couple's names and signature (script).
- `Cormorant Garamond` — display headings, prelude, verse (serif).
- `Outfit` — small uppercase labels, countdown labels, UI (sans).

Loaded from Google Fonts, as today.

**The seal:** the real **SS-Monogram-WaxSeal** asset, embedded directly in the HTML
(base64 data URI) so the file stays self-contained — no external image request.
On light backgrounds it sits via `mix-blend-mode: multiply` so the line-art reads
cleanly without a white box. Used at hero scale (opening), small (couple, blessing).

## Technical architecture

- **One self-contained file:** `public/index.html` — inline `<style>` and `<script>`,
  embedded seal. No external assets except Google Fonts (matching current setup).
- **Deploy:** unchanged. `vercel.json` keeps `outputDirectory: public`; the file
  drops into `public/` and ships on Vercel as it does today.
- **Mobile-first**, viewport-locked like the current invite (no zoom / overscroll),
  but must remain legible and centered on desktop.
- **Countdown** in vanilla JS targeting `2027-05-28T18:00:00` (carry the current
  evening start; exact time can be tuned).
- **No new dependencies, no framework, no bundler.** The existing
  `wedding-invite/.claude/launch.json` (`npx serve … public -l 3456`) still works
  for local preview.
- The current `index.html` is replaced wholesale; git history preserves the old
  version.

## What's removed vs. the current invite

- The blue/navy/champagne palette → garden palette above.
- The envelope + medallion opening → the blooming wreath.
- The six-card **scratch-to-reveal** date mechanic → the opening bloom *is* the
  reveal; dates are presented directly in Scene 03.
- The old embedded PNG textures/medallion → the new monogram seal.

## Success criteria

- Opens to the blooming seal; tapping it reveals the full invite.
- All confirmed content present and accurate; nothing from the table above lost.
- Reads beautifully on a phone (primary) and remains presentable on desktop.
- Single `index.html`, self-contained, deploys on Vercel with no config change.
- Motion is smooth and calm; reduced-motion users get a clean static experience.

## Open items (tunable during build, not blockers)

- Exact countdown start time (currently 18:00 on the Mehndi date).
- How literal the garden gets (illustrative line-art vs. richer florals).
- Final petal/parallax density and whether the wreath "gate" parts in two halves
  or dissolves into light.
