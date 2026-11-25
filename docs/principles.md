# Design Principles

The foundation every review in this repo is measured against. Each principle has a plain definition, what to do, and how to check it. IDs (e.g. `M6`, `U14`, `C3`, `A2`) are referenced by the [QC checklist](./qc-checklist.md).

Sections:

1. [UX & animation principles (M1–M20)](#1-ux--animation-principles)
2. [Unslop AI designs (U1–U20)](#2-unslop-ai-designs)
3. [CSS craft rules (C1–C10)](#3-css-craft-rules)
4. [Accessibility baseline, WCAG 2.2 AA (A1–A12)](#4-accessibility-baseline-wcag-22-aa)
5. [Credits](#5-credits)

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
Delete decorative gradients, glows, glassmorphism, blobs, sparkles, drop shadows on everything and emoji in headings unless they carry meaning. **Check:** removing each ornament would make the design worse, not just plainer.

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

## 5. Credits

The source lists were shared by **charles.unemployed** and draw on:

- **Raphael Salaja** — [userinterface.wiki](https://userinterface.wiki)
- **Frank Thomas & Ollie Johnston (Disney)** — *The Illusion of Life: Disney Animation* (the 12 principles, M1–M12)
- **Jon Yablonski** — [Laws of UX](https://lawsofux.com) (M13, M15–M19)
- **Emil Kowalski** — frequency of use (M20); see [emilkowal.ski](https://emilkowal.ski) and [animations.dev](https://animations.dev)
- **W3C** — [WCAG 2.2](https://www.w3.org/TR/WCAG22/) (section 4, added here)

Expansions, thresholds and code examples beyond the source definitions were added by this agent and should be revisited during the [skills-upkeep review](./skills-upkeep.md).
