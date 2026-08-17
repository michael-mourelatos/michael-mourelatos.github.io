# michael-mourelatos.github.io

Personal portfolio for Michael Mourelatos — data and analytics, Calgary. A
single-page scroll experience: static HTML served by GitHub Pages, no build
step, no framework.

## What it is

`index.html` is deliberately one file — markup, CSS and JavaScript inline.
The page is a scripted scroll journey rather than a set of routes, so the
markup, the styles that gate each animation state, and the timelines that
drive them are tightly coupled; keeping them in one file means one place to
read, one artefact to deploy, and no build tooling to maintain. The only
external dependencies are Google Fonts and GSAP + ScrollTrigger (3.12.5,
loaded from cdnjs).

The scroll flow, top to bottom:

1. **Intro loader** — name lockup between two rules. Plays once per session
   (`sessionStorage`), skippable with any key or click.
2. **Hero** — pinned. The supporting copy peels away, the name scales up, and
   a black curtain rises into the About section.
3. **About** — the longest pin. Two statements type in character by character
   (and untype when scrolling back). A signal-yellow disc floods the room and
   retints the fixed chrome via CSS variables. A 3D photo turntable follows:
   the portrait at the centre, six captioned photo plates on a rotating ring
   with a detent pause at each. At the apex, gravity: plates unhook and fall
   one by one (the dog last), and the portrait flips toward the camera as
   blue floods back in.
4. **Experience** — pinned. A year rail (2026 → 2020) fills while four roles
   cross-fade. Behind them, the background canvas gathers its flowing lines
   into an industry scene per employer: an oil-and-gas skyline with a lit
   flare, a classroom still life, a circuit board.
5. **Work** — pinned on desktop. Three case cards sweep past the camera on a
   parabolic depth curve. Each opens a native `<dialog>` case-study sheet
   (content currently redacted placeholders).
6. **Skills** — pinned. A three-entry capability manifest stamps in — label,
   word-by-word line, rule, tick, tally — while a blue fill rises behind it.
7. **Contact** — email marquees, a small canvas of vertical lines that bend
   away from the pointer, and a letter-magnet "Say hello." mailto link.

A fixed frame, four HUD corners (identity, section nav, current-section
readout, a deadpan status console) and a sticky "Let's work" tag persist over
everything; their colours are swapped per background via CSS variables and a
`body.on-dark` class.

## Repo map

| Path | Purpose |
|---|---|
| `index.html` | The entire site — markup, CSS and JS in one file |
| `photos/` | Six photos for the About turntable (rugby, suit, pasta, dog, golf, stampede) |
| `portrait.jpg` | Headshot, 720×900, used in the turntable core and OG image |
| `resume.pdf` | Linked from the hero and Contact |
| `file.html` | Previous design (a "personnel file" page). Unlinked; kept for reference |
| `README.md` | This file |

## Design system

Everything derives from five colours and two typefaces, declared as custom
properties on `:root`.

| Token | Value | Role |
|---|---|---|
| `--blue` | `#0022EE` | Working blue — the page ground, hard shadows, link accents |
| `--black` | `#0E0B08` | Near-black ink — dark sections, the frame, all canvas line work |
| `--cream` | `#FBF8F0` | Paper cream — text on dark, card stock |
| `--signal` | `#F2FF49` | Signal yellow — highlights, the yellow room, anything that should pop |
| `--red` | `#E4342B` | Sparingly: thumbnail strokes and the GRAVITY stamp |

| Token | Stack | Used for |
|---|---|---|
| `--serif` | Newsreader (optical sizing), Georgia fallback | Display and body copy |
| `--mono` | IBM Plex Mono | HUD, kickers, labels, captions, stacks |

A second layer of variables (`--hud-ink`, `--hud-accent`, `--lets-bg`,
`--lets-ink`, and per-section ones like `--aside-ink`) exists so the
animation code can retint the fixed chrome and the About room by tweening
CSS variables rather than touching individual elements.

## Animation architecture

Two systems share the work:

- **A single fixed `<canvas id="terrain">`** sits behind the page and draws
  the black line field: pointer-reactive lines with wakes in the hero, a
  small yellow circle riding a hill arc that tracks overall scroll progress,
  beach lines mid-journey, and the Experience industry scenes — seven flowing
  lines pulled toward a per-scene height profile (`profOil`, `profSchool`,
  `profChip`) with detail strokes on top. It repaints only on scroll or
  pointer movement through a single `requestAnimationFrame` gate; there is
  no free-running loop.
- **GSAP ScrollTrigger** drives everything in the DOM. Each section is a
  scrubbed pin. Breakpoint behaviour uses `gsap.matchMedia` with a 701px
  boundary: desktop and mobile get different timelines (the turntable is a
  true 3D ring on desktop, a two-column grid with a scrolling pass on
  mobile), and the context's cleanup function restores split text and
  removes listeners when the breakpoint flips. The hero, About, Experience
  and Work timelines are created inside one `matchMedia` callback in DOM
  order, so each pin's start is computed with the previous pin-spacer
  already applied.

Hot paths are kept cheap: the turntable spin is one proxy tween whose
`onUpdate` fans out through `quickSetter`s (one rotation, six opacities, six
shadow variables per frame), pointer parallax uses `quickTo`, and
`will-change` is only applied while the About pin is active. The Work
corridor is driven by a single `--progress` custom property per card,
consumed by one CSS `transform` expression. The Experience timeline does not
draw anything itself — it publishes its pin progress to the canvas code,
which cross-fades the three industry scenes from it.

## Accessibility and degradation

The page is gated by an `html.js` class that is added only after GSAP and
ScrollTrigger have actually loaded and `prefers-reduced-motion` is off. All
scroll choreography styles hang off `html.js`; `html:not(.js)` rules render
the same content as a plain static document.

- **No JavaScript**: static page — roles stacked, photos in a grid on a
  yellow panel, intro and curtain hidden, case-study buttons removed.
- **JS but CDN blocked**: same static layout; reveal-on-scroll elements are
  shown immediately. The canvas backdrop still renders.
- **Reduced motion**: the script exits before any timeline is built and a
  `prefers-reduced-motion` CSS block mirrors the static layout; marquees,
  caret blink and cursor effects are disabled. The backdrop only repaints on
  scroll and ignores the pointer.

Both canvases and all decorative SVG are `aria-hidden`. There is a skip
link, visible focus outlines, alt text on every photo, a labelled group on
the carousel and labelled `<dialog>` elements for the case studies. Content
order in the markup matches reading order, so the no-JS and screen-reader
experience is the same document.

## Local development

No build step. Serve the folder over HTTP (the fonts and GSAP CDN need a
real origin for preconnect, and `sessionStorage` behaves better off `file://`):

```bash
python -m http.server 8000
# http://localhost:8000
```

## Deploy

Push to `main`. GitHub Pages serves the repository root directly — no
Actions workflow, no build — and the site updates within a minute or two.
