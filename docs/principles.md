# Design Principles

The foundation every review in this repo is measured against. Each principle has a plain definition, what to do, and how to check it. IDs (e.g. `M6`, `U14`, `C3`, `A2`, `B9`, `T2`) are referenced by the [QC checklist](./qc-checklist.md).

Sections:

1. [UX & animation principles (M1–M20)](#1-ux--animation-principles)
2. [Unslop AI designs (U1–U20)](#2-unslop-ai-designs)
3. [CSS craft rules (C1–C10)](#3-css-craft-rules)
4. [Accessibility baseline, WCAG 2.2 AA (A1–A12)](#4-accessibility-baseline-wcag-22-aa)
5. [Background treatments (B1–B22, guardrails T1–T8)](#5-background-treatments)
6. [Credits](#6-credits)

> **Motion caveat that applies to all of section 1:** every non-essential animation must respect `prefers-reduced-motion: reduce` (see `A9`). Expressive motion is a layer on top of a UI that already works without it.

---

## 1. UX & animation principles

M1–M12 are Disney's 12 principles of animation adapted for interfaces. M13–M20 are UX laws.

### M1. Squash & stretch
**Definition:** things squish on impact and stretch in motion, which gives them weight.
**Do:** use a subtle scale on press (`scale(0.97)`) and release, or a small stretch on a toggle thumb as it travels. Keep deformation under ~5% in product UI; preserve the element's apparent volume.
**Check:** pressed states give physical feedback; no element distorts so much that text becomes unreadable.

### M2. Anticipation wind-up
**Definition:** a small pull back before the big move, so people see it coming.
**Do:** reserve for large, deliberate moves (a drawer opening, a card flying to a cart). A 2–4px or 2–3% counter-move in the first ~15% of the timeline is enough.
**Check:** anticipation never delays a response to direct input (it must not push the Doherty threshold, `M17`). Never on high-frequency actions (`M20`).

### M3. Staging the focus
**Definition:** direct the eye to one thing at a time.
**Do:** one primary focal point per view and per moment. Dim, blur or de-emphasise context when a modal or important change appears. Stagger reveals so they read in order.
**Check:** squint test — one element clearly dominates. Only one thing animates for attention at once.

### M4. Pose to pose
**Definition:** set the key poses and let the browser fill in the frames between.
**Do:** define start and end states (CSS classes, `@starting-style`, keyframes) and let CSS transitions / the Web Animations API interpolate. Animate `transform` and `opacity`, which the compositor handles cheaply.
**Check:** no JS frame-by-frame position updates where a transition would do; no animating `width`, `height`, `top`, `left` or `box-shadow` on large surfaces.

### M5. Follow through
**Definition:** parts keep moving after the main body stops, then settle.
**Do:** use a gentle overshoot (spring, or `cubic-bezier(0.34, 1.56, 0.64, 1)`) on things that arrive: toasts, popovers, dropped items. Child elements can settle slightly after their parent.
**Check:** overshoot is small (≤ ~4% / a few px) and settles quickly; no wobbling.

### M6. Slow in & slow out
**Definition:** speed up and slow down; never move at a constant speed.
**Do:** `ease-out` for entering (fast start, gentle stop), `ease-in` for exiting, `ease-in-out` for moving on screen. Prefer custom curves, e.g. `cubic-bezier(0.22, 1, 0.36, 1)`.
**Check:** no `linear` easing on spatial movement. `linear` is only fine for continuous things like spinners, progress and colour cycles.

### M7. Moving in arcs
**Definition:** move along curves, not straight lines.
**Do:** for elements travelling a long way diagonally (add-to-cart, shared element transitions), animate x and y with different easings, or use `offset-path`.
**Check:** long diagonal travel follows a natural curve. Short UI moves can stay straight.

### M8. Secondary action
**Definition:** a small flourish that backs up the main action.
**Do:** a checkmark drawing in after a save, a badge count bump after adding to a cart, a subtle confetti on a rare milestone.
**Check:** the secondary action supports the primary one and never competes with it or delays it.

### M9. Snappy timing
**Definition:** duration sets the feel, so keep UI animations under 300ms.
**Do:** micro-interactions (hover, press, toggle) 100–200ms; small enters/exits 150–250ms; large surfaces (sheets, page transitions) ≤ 300ms, up to ~400ms only for full-screen moves. Exits ~20–30% faster than enters.
**Check:** no UI transition over 300ms without a stated reason; nothing the user waits on is slowed by decoration.

### M10. Exaggerated feedback
**Definition:** push it past real so the moment lands.
**Do:** make key outcomes unmistakable: a wrong password field shakes, a success state pops slightly, a like heart bursts.
**Check:** exaggeration is reserved for meaningful moments (success, error, milestones), not routine actions.

### M11. Solid drawing in 3D
**Definition:** give flat things depth and volume.
**Do:** consistent light source; layered shadows (a tight contact shadow plus a soft ambient one); subtle highlights on top edges; `perspective` on 3D flips. In dark mode, express depth with lighter surfaces (`C8`).
**Check:** all shadows imply the same light direction; elevation levels are a defined scale, not ad-hoc values.

### M12. Overall appeal
**Definition:** the sum of every detail, done with care.
**Do:** sweat the details listed in this doc. Appeal is the result, not a separate step.
**Check:** a reviewer's gut-level "does this feel crafted?" — backed by the specific failures listed in the review.

### M13. Fitts's law
**Definition:** bigger, closer targets are faster to hit.
**Do:** make primary actions large and place them near where the user's attention or thumb already is. Put destructive actions away from frequent ones. Use screen edges and corners for important targets on desktop.
**Check:** primary CTA is the largest, most reachable control in its context; related controls sit near the content they act on.

### M14. Bigger hit areas
**Definition:** the clickable area can be bigger than it looks.
**Do:** extend hit areas with padding or a `::before` pseudo-element (`position:absolute; inset:-8px`). Target ≥ 44×44px on touch (Apple HIG), ≥ 24×24px absolute minimum (WCAG 2.5.8).
**Check:** small icons/links have expanded hit areas; adjacent targets' hit areas don't overlap.

### M15. Hick's law
**Definition:** more choices mean slower decisions.
**Do:** reduce visible options; highlight a recommended one; use progressive disclosure for advanced settings; split long flows into steps.
**Check:** no screen presents a flat list of many equal-weight choices without grouping, defaults or a recommendation.

### M16. Miller's law
**Definition:** we hold about 7 things in mind at once, so chunk information.
**Do:** group nav items, form fields and data into chunks of ~5–9; format long numbers (`+1 555 123 4567`, card numbers in groups of 4).
**Check:** long lists and forms are sectioned; nobody has to remember something from a previous screen to complete the current one.

### M17. Doherty threshold
**Definition:** respond within 400ms or it feels slow.
**Do:** give visual acknowledgement within ~100ms of any input. Use optimistic UI, skeletons and progress indicators for anything slower than 400ms (`U19`).
**Check:** every action produces immediate feedback; no dead click waiting on a network call.

### M18. Postel's law
**Definition:** accept messy input, give back clean output.
**Do:** accept phone numbers with or without spaces/dashes, dates in common formats, trim whitespace, accept pasted values with formatting; then display them normalised. Validate on blur, not on every keystroke.
**Check:** inputs don't reject reasonable variations; errors say how to fix, not just that something failed.

### M19. Jakob's law
**Definition:** people expect your app to work like the apps they already use.
**Do:** use established patterns: logo links home, search top-right or top-centre, cart icon top-right, standard form controls, standard gestures and shortcuts (`Esc` closes, `⌘K` command palette).
**Check:** any deviation from convention is deliberate, justified, and taught in context.

### M20. Frequency of use
**Definition:** the more often something is used, the less it should animate.
**Do:** actions used dozens of times a day (keyboard shortcuts, list navigation, command palettes, tab switches) get no or near-instant animation. Rare, meaningful moments (onboarding, first success, milestones) can be expressive.
**Check:** high-frequency interactions are ≤ 150ms or instant; nothing a power user hits repeatedly makes them wait on motion.

---

## 2. Unslop AI designs

Twenty edits that remove the generic "AI-generated" look.

### U1. Delete filler badges
Remove "New", "✨ AI-powered", "Beta" pills, and decorative tags that carry no information. **Check:** every badge states something true and useful the user would miss without it.

### U2. Drop redundant labels
Cut labels that repeat what the content already shows ("Email:" above an obvious email, "Title" above a heading, section eyebrows that restate the heading). **Check:** every label adds information the content doesn't.

### U3. Unitalicize random words
No italics or gradient text on arbitrary words for "flair". Emphasis is for genuine stress, citations or titles. **Check:** each italicised or highlighted word has a semantic reason.

### U4. Mono only where needed
Monospace is for code, keyboard keys, IDs, hashes and data that needs column alignment — not for headings or labels to look "technical". **Check:** every mono run is code or data.

### U5. Use a design system
Spacing, type sizes, radii, colours, shadows and motion come from a token scale (e.g. 4/8px spacing grid, a modular type scale), not one-off values. **Check:** no magic numbers; components reuse the same tokens.

### U6. Show the actual product
Use real screenshots, real UI or real photography rather than abstract blobs, generic 3D illustrations or stock imagery. **Check:** a visitor can see what the product actually looks like above the fold.

### U7. Enlarge the main feature
The most important thing gets the most space; avoid grids of equally sized feature cards. **Check:** clear size hierarchy — one feature is visually dominant.

### U8. Group with spacing
Use proximity (Gestalt) before borders, boxes and dividers: tighter spacing inside a group, larger between groups. **Check:** groups are readable with all borders removed; inter-group space is clearly larger than intra-group space (roughly ≥ 2×).

### U9. Make headlines specific
"Ship invoices in 30 seconds" beats "Supercharge your workflow". Name the user, the outcome and a concrete detail. **Check:** the headline could not be pasted onto a competitor's site unchanged.

### U10. Emphasize the CTA
One primary action per view with the strongest visual weight (filled, accent colour); secondary actions are visibly quieter. **Check:** squint test — the primary CTA is the first thing found; there aren't two equal-weight primaries.

### U11. Add an accent colour
A mostly neutral palette plus one deliberate accent for interaction and emphasis, instead of all-grey or rainbow. Build it in OKLCH (`C3`). **Check:** accent use is consistent (actions/highlights) and sparing.

### U12. Keep icons consistent
One icon family, one style (outline *or* filled), one stroke width, one size scale, optically aligned with text (`C5`). **Check:** no mixed icon sets or mixed fills in the same context.

### U13. Remove embellishments
Delete decorative gradients, glows, glassmorphism, blobs, sparkles, drop shadows on everything and emoji in headings unless they carry meaning. **Check:** removing each ornament would make the design worse, not just plainer. Background textures are the exception only when they meet section 5 (`T1`–`T8`).

### U14. Inner radius = outer − padding
Nested rounded corners stay concentric (`C1`). **Check:** inner radius equals outer radius minus the gap.

### U15. Improve text contrast
Body text meets ≥ 4.5:1, large text ≥ 3:1 (`A1`). Avoid low-contrast grey-on-grey "aesthetic" text. **Check:** measured with a contrast tool, not by eye.

### U16. Wrap headlines properly
Use `text-wrap: balance` for headlines and `text-wrap: pretty` for body to avoid orphans; constrain measure (headlines ~20ch, body 45–75ch); control breaks with `&nbsp;` where needed. **Check:** no single-word last lines on headlines; no ragged widows in short paragraphs.

### U17. Align by eye, not pixels
Optical alignment: nudge play icons right, align text to cap height not the box, hang punctuation, adjust icon placement so it *looks* centred. **Check:** visually centred items look centred, even where the math says otherwise.

### U18. Use tabular nums
`font-variant-numeric: tabular-nums` for prices, tables, timers, counters and anything that updates or aligns in columns. **Check:** numbers don't jitter when changing; columns of figures align.

### U19. Add loading states
Every async action has loading, empty, error and success states; prefer skeletons that match the final layout over spinners; disable double submission. **Check:** each state is designed, not just the happy path.

### U20. Animate useful feedback
Motion should communicate (state changed, item added, error here), not decorate. **Check:** every animation answers "what did this tell the user?"

---

## 3. CSS craft rules

Concrete implementation rules to follow in every UI built or reviewed.

### C1. Nested rounded corners
Inner radius = outer radius − padding.
```css
.card  { --r: 20px; --p: 8px; border-radius: var(--r); padding: var(--p); }
.card > .inner { border-radius: calc(var(--r) - var(--p)); } /* 12px */
```
If the result is ≤ 0, use a square inner corner or a small minimum (2–4px). **Check:** nested corners are concentric.

### C2. Alpha borders
Use translucent borders, not solid hex, so they blend into any background.
```css
border: 1px solid rgb(0 0 0 / 0.12);        /* light */
border: 1px solid rgb(255 255 255 / 0.10);  /* dark  */
```
**Check:** no hard-coded opaque grey borders that look wrong on tinted or image backgrounds.

### C3. Palettes in OKLCH
Build colour scales in `oklch()` instead of HSL so equal lightness actually looks equal across hues.
```css
--accent-500: oklch(0.62 0.19 255);
--accent-600: oklch(0.55 0.19 255);
```
Keep L steps even across the scale; adjust chroma to stay in gamut. **Check:** colours sharing a lightness token look equally light side by side; contrast still verified (`A1`).

### C4. Gradients in OKLCH
Interpolate in OKLCH so gradients don't go grey or muddy in the middle.
```css
background: linear-gradient(90deg in oklch, blue, orange);
```
**Check:** no dead grey band in the middle of a multi-hue gradient.

### C5. Icon stroke matches text weight
Match the icon's stroke width to the weight of adjacent text (e.g. ~1.5px stroke at 16px for regular 400 text, ~2px for 500–600). Size icons to the text's cap height or line height. **Check:** icons don't look heavier or lighter than their label.

### C6. Carousel edge masks
Fade the edges of horizontal scrollers so content suggests continuation.
```css
mask-image: linear-gradient(90deg, transparent, black 15% 85%, transparent);
```
Consider removing the mask on the side that's at its scroll start/end. **Check:** scroll regions show affordance; no hard cut-off.

### C7. Icon-swap blur
When one icon replaces another (copy → check, play → pause), blur the outgoing icon as it fades.
```css
.icon-out { opacity: 0; filter: blur(4px); scale: 0.8; transition: 150ms ease-in; }
```
**Check:** icon swaps crossfade with blur and scale, not a hard swap.

### C8. Dark-mode surfaces
Show depth with lighter surfaces, because shadows barely show on dark backgrounds. Each elevation level is a step lighter (e.g. `oklch(0.18 …)` → `0.22` → `0.26`). Avoid pure `#000` backgrounds and pure `#fff` text; reduce saturation of accents slightly. **Check:** elevated surfaces in dark mode are lighter than the base, not just shadowed.

### C9. 100dvh for full-height sections
Use `100dvh` (or `svh`/`lvh` deliberately) instead of `100vh`, which breaks on mobile Safari when the toolbar shows or hides.
```css
.hero { min-height: 100vh; min-height: 100dvh; } /* fallback first */
```
**Check:** no `100vh` on full-height layouts.

### C10. Product shots on white: multiply
Product photos shot on a white background use `mix-blend-mode: multiply` so the white disappears into a tinted or light surface.
```css
.product-shot { mix-blend-mode: multiply; }
```
Only works on light backgrounds; in dark mode use a transparent PNG/WebP or a light container instead. **Check:** no visible white boxes around product shots.

---

## 4. Accessibility baseline (WCAG 2.2 AA)

Not in the original source lists; added as the non-negotiable floor. Any `A` failure is a blocker.

| ID | Rule | How to check |
|---|---|---|
| A1 | **Text contrast** ≥ 4.5:1; large text (≥ 24px, or ≥ 18.66px bold) ≥ 3:1 (1.4.3) | Contrast checker on every text/background pair, including placeholders, disabled-but-meaningful text, text on images |
| A2 | **Non-text contrast** ≥ 3:1 for UI component boundaries, focus indicators, icons that convey meaning, chart lines (1.4.11) | Check input borders, toggle tracks, icon-only buttons |
| A3 | **Visible focus** on every interactive element; never `outline: none` without a replacement; focus not obscured by sticky headers (2.4.7, 2.4.11) | Tab through the whole UI |
| A4 | **Keyboard operable**: everything works with keyboard, logical tab order, no traps, `Esc` closes overlays, focus returns to trigger (2.1.1, 2.1.2, 2.4.3) | Unplug the mouse |
| A5 | **Target size** ≥ 24×24 CSS px or sufficient spacing (2.5.8); aim for 44×44 on touch | Measure hit areas |
| A6 | **Don't rely on colour alone**: errors, status, links in body text and chart series also use text, icons or patterns (1.4.1) | View in greyscale |
| A7 | **Text alternatives**: meaningful images have `alt`; decorative ones `alt=""`; icon-only buttons have accessible names (1.1.1, 4.1.2) | Screen reader / accessibility tree |
| A8 | **Forms**: every input has a visible, programmatic label (placeholder is not a label); errors are identified in text, linked to the field, and suggest a fix (1.3.1, 3.3.1, 3.3.3) | Submit an empty and an invalid form |
| A9 | **Reduced motion**: respect `prefers-reduced-motion`; no flashing > 3 times/second; auto-playing motion > 5s can be paused (2.3.1, 2.2.2, 2.3.3) | Toggle OS setting |
| A10 | **Reflow & zoom**: usable at 320px width and at 200% text zoom without horizontal scroll or clipped text; respect text-spacing overrides (1.4.4, 1.4.10, 1.4.12) | Resize and zoom |
| A11 | **Semantics**: real `<button>`, `<a>`, headings in order, landmarks, `lang` set; ARIA only when native HTML can't (1.3.1, 4.1.2) | Inspect HTML / a11y tree |
| A12 | **Status messages** announced without moving focus (`role="status"` / `aria-live`) for toasts, async results and validation (4.1.3) | Screen reader |

---

## 5. Background treatments

Twenty-two background treatments that stop a design looking flat (B1–B22), plus eight guardrails (T1–T8) that every one of them must pass. A background is the stage, not the actor: it adds mood, material or brand, and the content on top must still win (`M3`).

**How this fits with `U13` (remove embellishments):** a treatment from this section is allowed when it has a reason (brand, mood, material, wayfinding, or fixing gradient banding) and passes T1–T8. Picking one because the page "felt empty" is still slop.

### Guardrails (apply to every treatment)

| ID | Rule | How to check |
|---|---|---|
| T1 | **One treatment, one reason.** At most one textured background per view; never stack two (grain + halftone + grid). Name the reason it's there. | Remove it: if nothing is lost but noise, cut it (`U13`). |
| T2 | **Text contrast over texture.** Measure `A1` against the worst point of the texture under the text (lightest speck on dark, darkest on light), not the average. If it fails, put the text on a solid surface or a scrim, e.g. `linear-gradient(oklch(0.15 0 0 / 0.6), oklch(0.15 0 0 / 0.6))`. Busy, high-frequency texture never sits directly behind body text, even if it passes. | Contrast checker at several sampled points; any fail is a Blocker. |
| T3 | **Stage the focus.** Keep the texture's own contrast low behind content (about ΔL ≤ 0.06 in OKLCH, or grain opacity 3–8%). Only hero art may be loud, and only where no text sits. | Squint test: the background is never the first thing you see, unless it is the hero image. |
| T4 | **Static by default.** Any movement is slow, uses `transform`/`opacity` only, stops under `prefers-reduced-motion: reduce`, and can be paused if it runs > 5s (`A9`). Never animate `feTurbulence`, `feDisplacementMap`, `filter: blur()` or `backdrop-filter` values per frame. | Toggle reduced motion; check DevTools Performance for repaints. |
| T5 | **Light and fast.** Prefer CSS gradients and inline SVG (< ~10 KB). Bitmaps in AVIF/WebP: tiles < 20 KB, hero images < ~150 KB at 1280px, with `srcset`. Render SVG filters on one fixed-size layer; don't put them, `backdrop-filter`, or `background-attachment: fixed` on large scrolling areas. The background must not delay the page's main content (LCP). | Network panel sizes; Lighthouse LCP; scroll at 60fps on a mid-range phone. |
| T6 | **Built on tokens, with a fallback.** Colours in OKLCH (`C3`, `C4`); a solid `background-color` declared first so text is readable if the image or filter fails; decorative layers on `::before`/`::after` with `pointer-events: none` (or an `<img alt="" aria-hidden="true">`); full-height sections use `100dvh` (`C9`). Non-Baseline features (`backdrop-filter` prefix, `@property`) sit behind `@supports` or degrade to static. | Read the CSS; block images and reload. |
| T7 | **Every scheme.** Each treatment has a dark-mode variant (not just inverted: lower grain opacity, no pure `#000`, `C8`). Under `prefers-contrast: more` and `forced-colors: active`, drop the texture to a solid colour. | Toggle light/dark, increased contrast and forced colours. |
| T8 | **Scales cleanly.** Tiles repeat without visible seams; pattern sizes are whole pixels and align to the spacing grid (`U5`) to avoid moiré; photos use `cover` with a chosen focal point (`background-position` / `object-position`). | View at 320, 768 and 1280px+ and at 200% zoom. |

### Shared building blocks

Most texture treatments are one of three primitives, tuned:

```css
/* 1. Noise layer (grain, paper, felt, graphite, clouds). Greyscale via feColorMatrix. */
.texture { position: relative; isolation: isolate; background-color: var(--surface); }
.texture::before {
  content: ""; position: absolute; inset: 0; z-index: -1; pointer-events: none;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='240' height='240'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='.8' numOctaves='3' stitchTiles='stitch'/%3E%3CfeColorMatrix type='saturate' values='0'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
  opacity: var(--texture-opacity, 0.06);
  mix-blend-mode: multiply;            /* use soft-light or screen on dark surfaces */
}
@media (prefers-contrast: more), (forced-colors: active) { .texture::before { display: none; } }

/* 2. Repeating gradient (grids, dots, halftone, flutes): pure CSS, zero requests. */
/* 3. Pre-rendered image (photos, paintings, dithering, mosaics, contours): AVIF/WebP, alt="". */
```

`baseFrequency` sets the grain size (higher = finer); two values (`'0.8 0.03'`) stretch it into fibres or blades; `numOctaves` adds detail at a cost (keep ≤ 4).

### The 22 treatments

#### B1. Felt texture
**What:** a soft, fibrous, matte surface like wool felt.
**Use for:** warm, tactile, crafted brands; kids, games, hospitality, cosy empty states.
**How:** noise layer with `baseFrequency='.6'`, `numOctaves='4'`, tinted by a mid-chroma OKLCH base (e.g. `oklch(0.55 0.09 30)`), opacity 0.10–0.15, `mix-blend-mode: soft-light`. A second, larger noise at 0.04 opacity breaks up repetition.
**Watch:** felt is mid-dark and fuzzy, so text goes on a solid card (`T2`).

#### B2. Fluted glass
**What:** ribbed, reeded glass that slices and smears whatever sits behind it.
**Use for:** premium hero panels, product launches, a frosted header over imagery.
**How:** over an image or colour field, a ribbed overlay plus blur:
```css
.fluted {
  background-image: repeating-linear-gradient(90deg,
    oklch(1 0 0 / 0.16) 0 1px, oklch(1 0 0 / 0) 1px 10px, oklch(0 0 0 / 0.10) 10px 12px);
  -webkit-backdrop-filter: blur(8px); backdrop-filter: blur(8px);
}
@supports not (backdrop-filter: blur(1px)) { .fluted { background-color: oklch(0.95 0 0 / 0.85); } }
```
For a true refracted look, pre-render the photo with a displacement effect rather than using SVG `feDisplacementMap` on HTML (inconsistent in Safari).
**Watch:** a cousin of glassmorphism (`U13`): use once, on a hero or one surface, never behind body text; `backdrop-filter` is costly on large or scrolling areas (`T5`).

#### B3. Thermal gradients
**What:** heat-map colour fields (deep blue → magenta → orange → yellow) that look like a thermal camera.
**Use for:** energy, health and biometrics, AI and data products, anything about heat or intensity.
**How:** 2–4 overlapping `radial-gradient(in oklch, …)` blobs on a dark base, then grain (`B9`) to stop banding. Keep hues on the thermal path; let OKLCH interpolation (`C4`) avoid the muddy middle.
**Watch:** very high chroma: text on top needs a scrim or solid surface (`T2`); purely decorative, so never imply real data with it (`A6`).

#### B4. Water ripples
**What:** concentric rings or caustic light patterns like the surface of water.
**Use for:** wellness, calm, travel, hydration or "drop"-based metaphors.
**How:** rings via `repeating-radial-gradient(circle at 70% 40%, oklch(… / 0.10) 0 2px, transparent 2px 28px)`; caustics as a pre-rendered tile or an SVG with `feTurbulence` + `feDisplacementMap` rendered once.
**Watch:** if it moves, animate a pre-rendered layer's `transform` slowly; static under reduced motion (`T4`). Strong rings can cause discomfort, so keep contrast low (`T3`).

#### B5. Speckled graphite
**What:** dark charcoal with fine light and dark specks, like terrazzo or graphite stone.
**Use for:** dark-mode editorial, developer tools, hardware and premium tech.
**How:** base `oklch(0.22 0.005 260)` (not black, `C8`), noise layer at a high `baseFrequency` (≈ 1.2) pushed into specks with `<feComponentTransfer><feFuncA type="discrete" tableValues="0 0 0 1"/></feComponentTransfer>`; specks at 0.15–0.25 opacity.
**Watch:** a light speck behind light text kills contrast, so measure at the worst point (`T2`).

#### B6. Single-hue landscapes
**What:** layered hill or mountain silhouettes in one hue, each layer a step lighter toward the horizon.
**Use for:** onboarding, empty states, footers, outdoor, travel and climate brands.
**How:** 3–5 SVG `<path>` layers using one hue with even OKLCH lightness steps (e.g. L 0.35 / 0.45 / 0.55 / 0.65, same C and H, `C3`). Inline SVG, `preserveAspectRatio="xMidYMax slice"`, `aria-hidden="true"`.
**Watch:** put text in the open "sky" area, never across the ridgelines; check the layer steps still read in dark mode (`T7`).

#### B7. Blueprint grids
**What:** a blue engineering sheet with fine and major grid lines.
**Use for:** developer tools, engineering, docs, "how it works" and architecture sections.
**How:**
```css
.blueprint {
  --minor: oklch(0.9 0.05 250 / 0.14); --major: oklch(0.9 0.05 250 / 0.30);
  background-color: oklch(0.40 0.12 255);
  background-image:
    linear-gradient(var(--major) 1px, transparent 1px), linear-gradient(90deg, var(--major) 1px, transparent 1px),
    linear-gradient(var(--minor) 1px, transparent 1px), linear-gradient(90deg, var(--minor) 1px, transparent 1px);
  background-size: 80px 80px, 80px 80px, 16px 16px, 16px 16px;
}
```
**Watch:** grid size matches the spacing scale and aligns with the layout so content doesn't look misaligned (`T8`, `U17`).

#### B8. Halftone
**What:** a print-style dot pattern, often fading in size across the area.
**Use for:** editorial, retro, pop art, comic, music and poster aesthetics.
**How:** `radial-gradient(circle, var(--ink) 0 30%, transparent 31%) 0 0 / 8px 8px` over the base; fade with `mask-image: linear-gradient(to bottom, black, transparent)`. For photo halftones, pre-render.
**Watch:** moiré at non-integer zoom or with other patterns; keep cell size whole pixels (`T8`); not behind small text.

#### B9. Grainy gradients
**What:** a smooth OKLCH gradient with a fine film grain on top.
**Use for:** the safest default when a flat colour feels dead: heroes, cards, auth screens. The grain also hides gradient banding.
**How:** `background: linear-gradient(135deg in oklch, var(--a), var(--b))` plus the noise layer at 0.04–0.08 opacity, `mix-blend-mode: overlay` (or `soft-light`).
**Watch:** the most overused treatment of the set; keep the gradient to 2–3 close hues and the grain subtle (`T3`).

#### B10. Grass texture
**What:** dense, short vertical blades, like a lawn or turf from above or at ground level.
**Use for:** sport, outdoors, gardening, sustainability, games.
**How:** a pre-rendered photo or illustrated tile (AVIF, < 20 KB tile), or a stretched noise layer `baseFrequency='0.9 0.04'` tinted green in OKLCH. Best as a band (footer, section edge) rather than a full page.
**Watch:** very busy: never behind text (`T2`); keep to one band (`T1`).

#### B11. Dithered images
**What:** a photo reduced to a few colours with ordered (Bayer) or error-diffusion dithering, for a retro digital look.
**Use for:** tech, retro and lo-fi brands, editorial heroes, low-bandwidth aesthetics.
**How:** dither at build time (image tool or script) into a 2–8 colour PNG/WebP from the palette tokens; display with `image-rendering: pixelated` if upscaled. Don't dither at runtime with JS.
**Watch:** decorative images get `alt=""` (`A7`); dithered noise is high contrast, so no text on top without a solid surface (`T2`).

#### B12. Paper texture
**What:** warm off-white with faint fibres and blotches, like good paper stock.
**Use for:** editorial, writing and reading apps, docs, invitations, anything meant to feel calm and printed.
**How:** base `oklch(0.97 0.012 85)`; two noise layers: fine fibres (`baseFrequency='0.9'`, 0.05 opacity) and large blotches (`baseFrequency='0.02'`, 0.03 opacity).
**Watch:** the safest texture behind long-form text, but still measure contrast (`T2`); dark mode becomes "dark paper" (`oklch(0.2 0.01 85)`), not inverted noise (`T7`).

#### B13. Contour lines
**What:** topographic isolines, like an elevation map.
**Use for:** outdoor, maps and travel, data and research, adventure brands, section dividers.
**How:** a pre-generated SVG (export from a contour generator, simplify paths, aim < 15 KB) with `stroke: oklch(… / 0.15)`, `fill: none`, `vector-effect: non-scaling-stroke`. Tile or use one large `cover` image.
**Watch:** lines must stay low contrast so they don't read as content or charts (`T3`); keep stroke widths consistent at every size.

#### B14. Cloud sky
**What:** soft clouds on a sky gradient.
**Use for:** aspirational, calm, weather, travel, "lightness" brands. Cloud-computing visuals are a cliché, so justify it (`U9`, `U13`).
**How:** `linear-gradient(in oklch, oklch(0.75 0.1 240), oklch(0.92 0.04 230))` plus a low-frequency noise (`baseFrequency='0.008'`, `numOctaves='5'`) mapped to white alpha with `feColorMatrix`; or a pre-rendered photo.
**Watch:** any drift is very slow `transform` only, off under reduced motion (`T4`); white clouds behind white text fail contrast (`T2`).

#### B15. Pixel mosaics
**What:** a field of large square tiles in related colours, like a low-res image blown up.
**Use for:** games, creative tools, generative brand art, playful empty states.
**How:** downscale an image or generate a tiny one (e.g. 16×9 px PNG, under 1 KB) and display it at full size with `image-rendering: pixelated; object-fit: cover`. Or an SVG of `<rect>`s from palette tokens.
**Watch:** tile colours stay within a narrow lightness range so text on top can pass contrast (`T2`, `T3`).

#### B16. Lens distortion
**What:** a fisheye, barrel or chromatic-aberration warp of an image or grid.
**Use for:** experimental heroes, music, fashion and art-led brands. Rarely in product UI.
**How:** pre-render the distortion into the image. Live alternatives (SVG `feDisplacementMap` with a radial map, WebGL) are costly and inconsistent across browsers.
**Watch:** never under text; animated warps can trigger vestibular discomfort, so static only unless reduced motion is honoured (`T4`, `A9`).

#### B17. Stippled gradients
**What:** a gradient built from dots whose density changes, like pen stippling, instead of a smooth blend.
**Use for:** editorial illustration, science and research, handmade or print-inspired brands.
**How:** pre-render with a stippling tool to SVG/PNG; or approximate with a fine dot grid (`B18`) masked by a gradient: `mask-image: linear-gradient(to top, black, transparent)`, layered over a noise threshold for irregularity.
**Watch:** dense stipple areas behave like halftone: watch moiré and keep text off the dense end (`T2`, `T8`).

#### B18. Square-dot grids
**What:** a regular grid of tiny square (or round) dots.
**Use for:** canvas and whiteboard tools, developer and design tools, dashboards, empty states.
**How:**
```css
.dot-grid {   /* 2px square dot every 16px, colours from tokens */
  background:
    linear-gradient(90deg, var(--surface) 14px, transparent 0) 0 0 / 16px 16px,
    linear-gradient(var(--surface) 14px, var(--dot) 0) 0 0 / 16px 16px;
}
/* round dots: radial-gradient(circle, var(--dot) 1px, transparent 1.5px) 0 0 / 16px 16px */
```
**Watch:** dot pitch equals the spacing unit so placed items snap to it (`T8`); dots at ≤ ~0.15 contrast so they don't read as content.

#### B19. Blurred photos
**What:** a photo blurred into a soft colour field, often from the content itself (album art, a profile photo).
**Use for:** media players, profile and detail headers, ambient backgrounds that carry the content's colours.
**How:** serve a tiny version (≈ 32–64 px wide) and scale it up with `filter: blur(24px); transform: scale(1.1)` (the scale hides blurred edges), or pre-blur at build time. Add a scrim for text.
**Watch:** `filter: blur()` on a full-viewport element is expensive; pre-blur where you can (`T5`); contrast varies with each photo, so the scrim must guarantee `A1` for any image (`T2`).

#### B20. Iridescent shapes
**What:** holographic, hue-shifting shapes like oil on water or foil.
**Use for:** beauty, fashion, music, launch moments. Highest slop risk in the set (`U13`).
**How:**
```css
.iridescent {
  background: conic-gradient(from var(--angle, 0deg) in oklch longer hue,
    oklch(0.85 0.12 0), oklch(0.85 0.12 0));
  filter: blur(40px);
}
@property --angle { syntax: "<angle>"; inherits: false; initial-value: 0deg; }
@media (prefers-reduced-motion: no-preference) {
  .iridescent { animation: spin-hue 20s linear infinite; }
}
@keyframes spin-hue { to { --angle: 360deg; } }
```
Without `@property` support the shape stays static, which is fine. The blur bleeds past the box, so place it in a parent with `overflow: clip`.
**Watch:** one shape, behind no text, with a reason; continuous hue cycling must be pausable if it runs > 5s (`A9`, `T4`).

#### B21. Impressionist paintings
**What:** a crop of a painterly, brush-stroke artwork as a backdrop.
**Use for:** editorial, culture, luxury, wellness and calm brands.
**How:** use public-domain works (check the licence and credit) or commissioned art; crop to a quiet area; AVIF/WebP with `srcset`; `object-position` on the focal point.
**Watch:** AI-generated "in the style of" imagery can read as slop and has licence risk, so prefer real works (`U6`); place text on a calm region or a scrim (`T2`).

#### B22. ASCII and pixels
**What:** a field of ASCII characters or pixel art forming an image or pattern.
**Use for:** developer tools, terminal and CLI products, retro and hacker aesthetics.
**How:** pre-render to an image or `<canvas>` for large areas; if it must be live text, use a `<pre aria-hidden="true">` with `user-select: none`, sized in `ch` units, capped in size to keep the DOM small. Monospace is correct here because the characters are the art (`U4`).
**Watch:** screen readers must not read it (`aria-hidden`, `A7`); animated "matrix rain" stops under reduced motion and must be pausable (`T4`, `A9`).

---

## 6. Credits

The source lists were shared by **charles.unemployed** and draw on:

- **Raphael Salaja** — [userinterface.wiki](https://userinterface.wiki)
- **Frank Thomas & Ollie Johnston (Disney)** — *The Illusion of Life: Disney Animation* (the 12 principles, M1–M12)
- **Jon Yablonski** — [Laws of UX](https://lawsofux.com) (M13, M15–M19)
- **Emil Kowalski** — frequency of use (M20); see [emilkowal.ski](https://emilkowal.ski) and [animations.dev](https://animations.dev)
- **W3C** — [WCAG 2.2](https://www.w3.org/TR/WCAG22/) (section 4, added here)
- **Background treatments (section 5)** — the list of 22 comes from a "22 Backgrounds to Make Designs Less Flat" post Baruch shared (creator not named in the screenshot). Descriptions, implementations and guardrails T1–T8 were written by this agent.

Expansions, thresholds and code examples beyond the source definitions were added by this agent and should be revisited during the [skills-upkeep review](./skills-upkeep.md).
