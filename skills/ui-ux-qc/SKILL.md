---
name: ui-ux-qc
description: Expert UI/UX quality check. Use whenever reviewing or building any design, mockup, screenshot, prototype, website, app screen, frontend PR, CSS or interface copy, or when asked for UI/UX feedback or a design QC.
---

# UI/UX QC

You are acting as a **head of UI/UX**. Review any design, screenshot, prototype, URL, frontend PR or interface copy against the rules below and give a verdict with concrete fixes. When building UI, follow the same rules and end with a short self-review in the same format.

The rules are self-contained here. The fuller source with longer explanations lives at github.com/3aruch-dev/UI-UX-Design-Agent (`docs/principles.md`, `docs/qc-checklist.md`); if this skill and that repo disagree, the repo is newer.

## Workflow

1. **Identify the input**: static (screenshot, mockup, Figma frame) or live (URL, running app, PR with code).
2. **Identify the context**: product, audience, platform, light/dark mode, existing design system. If unknown, state what you assumed. Never invent a brand or design system.
3. **Run every check** in the rule groups below. Mark each one Pass, Fix, or N/A with a reason. Static inputs can't be checked for motion, keyboard or code rules: mark them `N/A (static)`, never Pass.
4. **Measure, don't eyeball** where possible: compute contrast ratios, read CSS values, run or screenshot the page at 320 / 768 / 1280px+, light and dark, and tab through it.
5. **Score, give a verdict, and write the report** in the output format at the end.

## Rules

### A. Accessibility, WCAG 2.2 AA (every failure is a Blocker)
- **A1** Text contrast ≥ 4.5:1; large text (≥ 24px, or ≥ 18.66px bold) ≥ 3:1. Includes placeholders and text on images.
- **A2** Non-text contrast ≥ 3:1 for control boundaries, focus rings, meaningful icons, chart lines.
- **A3** Visible focus on every interactive element; no `outline: none` without a replacement; focus not hidden under sticky headers.
- **A4** Fully keyboard operable, logical tab order, no traps, `Esc` closes overlays, focus returns to the trigger.
- **A5** Targets ≥ 24×24px (aim for 44×44 on touch).
- **A6** Meaning never by colour alone (errors, status, links in body text, chart series).
- **A7** Meaningful images have alt text, decorative ones `alt=""`; icon-only buttons have accessible names.
- **A8** Every input has a visible label (placeholder isn't a label); errors are in text, linked to the field, and say how to fix.
- **A9** Respect `prefers-reduced-motion`; nothing flashes > 3×/s; auto-moving content > 5s can be paused.
- **A10** Usable at 320px wide and 200% zoom without horizontal scroll or clipping.
- **A11** Semantic HTML (real buttons, links, ordered headings, landmarks, `lang`); ARIA only when native HTML can't.
- **A12** Toasts, async results and validation announced via `role="status"` / `aria-live`.

### M. UX & animation principles
Disney's 12 animation principles adapted to UI (M1–M12) plus UX laws (M13–M20).
- **M1 Squash & stretch**: things squish on impact and stretch in motion, giving weight. Press scale ~0.97; deformation under ~5%.
- **M2 Anticipation wind-up**: a small pull back before the big move. Only on large deliberate moves, never delaying input response or on frequent actions.
- **M3 Staging the focus**: direct the eye to one thing at a time. One focal point per view (squint test); only one thing animates for attention at once.
- **M4 Pose to pose**: set key states and let the browser interpolate. Animate `transform` and `opacity`, not `width/height/top/left`.
- **M5 Follow through**: parts keep moving after the body stops, then settle. Small overshoot (≤ ~4%), settles fast, no wobble.
- **M6 Slow in & slow out**: never constant speed. `ease-out` to enter, `ease-in` to exit, `ease-in-out` to move; `linear` only for spinners and progress.
- **M7 Moving in arcs**: long diagonal travel follows a curve (split x/y easings or `offset-path`).
- **M8 Secondary action**: a small flourish backs up the main action (checkmark after save, badge bump) and never competes with it.
- **M9 Snappy timing**: UI animations under 300ms. Micro 100–200ms, enters 150–250ms, exits 20–30% faster than enters.
- **M10 Exaggerated feedback**: push past real so key moments land (error shake, success pop). Reserved for meaningful moments.
- **M11 Solid drawing in 3D**: give flat things depth. One light source, a defined elevation scale, layered shadows.
- **M12 Overall appeal**: the sum of every detail, done with care.
- **M13 Fitts's law**: bigger, closer targets are faster to hit. Primary actions large and near; destructive actions away from frequent ones.
- **M14 Bigger hit areas**: the clickable area can be bigger than it looks (padding or `::before { inset: -8px }`), without overlapping neighbours.
- **M15 Hick's law**: more choices mean slower decisions. Fewer visible options, a recommended default, progressive disclosure.
- **M16 Miller's law**: about 7 things in mind at once, so chunk into groups of ~5–9; format long numbers.
- **M17 Doherty threshold**: respond within 400ms or it feels slow. Acknowledge input within ~100ms; optimistic UI, skeletons or progress beyond 400ms.
- **M18 Postel's law**: accept messy input (spaces, dashes, date formats, pasted text), return clean normalised output; validate on blur.
- **M19 Jakob's law**: people expect your app to work like apps they already use. Follow conventions; justify and teach any deviation.
- **M20 Frequency of use**: the more often something is used, the less it should animate. Frequent actions instant or ≤ 150ms; rare moments can be expressive.

### U. Unslop AI designs
- **U1** Delete filler badges ("New", "✨ AI-powered") that carry no information.
- **U2** Drop redundant labels that repeat what content already shows.
- **U3** Unitalicize random words; no gradient or italic words for "flair".
- **U4** Mono only where needed: code, keys, IDs, aligned data.
- **U5** Use a design system: spacing, type, radius, colour, shadow and motion from tokens, no magic numbers.
- **U6** Show the actual product, not abstract blobs or stock 3D.
- **U7** Enlarge the main feature; no grid of equal-weight cards.
- **U8** Group with spacing before borders; gaps between groups ≥ ~2× gaps within.
- **U9** Make headlines specific: user, outcome, concrete detail. Fails if it could sit on a competitor's site unchanged.
- **U10** Emphasize the CTA: one filled-accent primary per view; secondaries visibly quieter.
- **U11** Add an accent colour: mostly neutral palette plus one deliberate accent, used consistently and sparingly.
- **U12** Keep icons consistent: one family, one style, one stroke, one size scale.
- **U13** Remove embellishments (glows, blobs, glassmorphism, sparkles, emoji headings) that carry no meaning. Background textures pass only under section B (T1–T8).
- **U14** Inner radius = outer radius − padding.
- **U15** Improve text contrast (see A1).
- **U16** Wrap headlines properly: `text-wrap: balance` on headings, `pretty` on body; measure ~20ch headlines, 45–75ch body; no orphans.
- **U17** Align by eye, not pixels: optical centring of play icons, cap-height alignment, hanging punctuation.
- **U18** Use tabular nums (`font-variant-numeric: tabular-nums`) for prices, tables, timers, counters.
- **U19** Add loading states: design loading, empty, error and success; skeletons over spinners; prevent double submit.
- **U20** Animate useful feedback: every animation tells the user something.

### C. CSS craft rules
- **C1 Nested radius**: inner = outer − padding, e.g. `border-radius: calc(var(--r) - var(--p))`. If ≤ 0, use square or 2–4px.
- **C2 Alpha borders**: `rgb(0 0 0 / 0.12)` (light), `rgb(255 255 255 / 0.10)` (dark), not solid hex, so they blend into any background.
- **C3 OKLCH palettes**: build scales in `oklch()` not HSL so equal lightness looks equal; even L steps.
- **C4 OKLCH gradients**: `linear-gradient(90deg in oklch, blue, orange)` so the middle doesn't go grey.
- **C5 Icon stroke**: match icon stroke to adjacent text weight (~1.5px at 16px regular, ~2px at 500–600).
- **C6 Carousel edge masks**: `mask-image: linear-gradient(90deg, transparent, black 15% 85%, transparent)`.
- **C7 Icon-swap blur**: blur the outgoing icon as it fades: `filter: blur(4px); opacity: 0; scale: 0.8`, ~150ms.
- **C8 Dark mode depth**: show elevation with lighter surfaces, since shadows barely show; no pure `#000` background or pure `#fff` text.
- **C9 Full height**: `min-height: 100vh; min-height: 100dvh;` — never bare `100vh`, which breaks in mobile Safari.
- **C10 Product shots on white**: `mix-blend-mode: multiply` so the white disappears; light backgrounds only (use transparent images in dark mode).

### B. Background treatments (B1–B22) and guardrails (T1–T8)
Allowed only with a reason (brand, mood, material, hiding gradient banding) and when every T rule passes; otherwise it's a `U13` embellishment.

**Guardrails**
- **T1** One treatment per view, never stacked; name why it's there.
- **T2** Text over texture meets A1 at the worst sampled point, not the average; otherwise a solid surface or scrim. No body text directly on busy texture. A fail is a Blocker.
- **T3** Stage the focus (M3): texture contrast low behind content (≈ ΔL ≤ 0.06 OKLCH, grain 3–8% opacity); only hero art may be loud, and not where text sits.
- **T4** Static by default; motion is slow `transform`/`opacity`, off under `prefers-reduced-motion`, pausable if > 5s. Never animate `feTurbulence`, displacement, `blur()` or `backdrop-filter` per frame.
- **T5** CSS gradients / inline SVG (< ~10 KB) first; AVIF/WebP tiles < 20 KB, heroes < ~150 KB with `srcset`; no large `backdrop-filter`, SVG filters or `background-attachment: fixed` on scrolling areas; don't delay LCP.
- **T6** OKLCH tokens; solid `background-color` declared first as fallback; decorative layer on `::before` with `pointer-events: none` (or `<img alt="" aria-hidden="true">`); `100dvh` for full height; `@supports` for non-Baseline features.
- **T7** Designed dark variant (lower grain, no pure black); drop texture under `prefers-contrast: more` and `forced-colors: active`.
- **T8** No seams or moiré; whole-pixel pattern sizes on the spacing grid; photos `cover` with a set focal point; check 320 / 768 / 1280px+ and 200% zoom.

**Noise primitive** (grain, paper, felt, graphite, clouds): a `::before` layer with an SVG data-URI `<feTurbulence type="fractalNoise" baseFrequency=".8" numOctaves="3" stitchTiles="stitch">` + `<feColorMatrix type="saturate" values="0">`, `opacity` 0.04–0.15, `mix-blend-mode: multiply` (light) / `soft-light` (dark). Higher `baseFrequency` = finer; two values (`"0.9 0.04"`) stretch into fibres or blades.

**Treatments** (what · use for · how)
- **B1 Felt**: soft fibrous matte · warm, crafted, kids, games · noise `.6`/4 octaves, 0.10–0.15, soft-light on mid-chroma base; text on cards.
- **B2 Fluted glass**: ribbed glass smearing what's behind · premium hero panel · `repeating-linear-gradient(90deg, …)` ribs + `backdrop-filter: blur(8px)` with `-webkit-` prefix and `@supports` fallback; pre-render true refraction. Glassmorphism risk: once, never behind body text.
- **B3 Thermal gradients**: heat-map blue→magenta→orange→yellow · energy, health, AI/data · overlapping `radial-gradient(in oklch …)` on dark + grain. Text needs a scrim; never imply real data.
- **B4 Water ripples**: rings or caustics · wellness, calm, travel · `repeating-radial-gradient(circle at …)` or a pre-rendered caustic tile; any motion via `transform` only.
- **B5 Speckled graphite**: charcoal with specks · dark editorial, dev tools, hardware · base ≈ `oklch(0.22 0.005 260)`, high-frequency noise thresholded with `feFuncA type="discrete"`; measure contrast at a light speck.
- **B6 Single-hue landscapes**: layered hills in one hue · onboarding, empty states, footers, outdoor · 3–5 inline SVG paths, same C and H, even L steps; text in the sky, not on ridgelines.
- **B7 Blueprint grids**: blue sheet with minor/major lines · dev tools, engineering, docs · four `linear-gradient` 1px lines at 16px and 80px on `oklch(0.40 0.12 255)`; grid = spacing scale.
- **B8 Halftone**: print dots · editorial, retro, pop · `radial-gradient(circle, var(--ink) 0 30%, transparent 31%) 0 0 / 8px 8px`, faded with `mask-image`; whole-pixel cells to avoid moiré.
- **B9 Grainy gradients**: OKLCH gradient + film grain · safest default for heroes, cards, auth; hides banding · `linear-gradient(135deg in oklch, …)` + noise 0.04–0.08 overlay. Most overused: 2–3 close hues.
- **B10 Grass**: dense vertical blades · sport, outdoors, sustainability · AVIF tile or stretched noise `"0.9 0.04"` tinted green; a band, not a page; never behind text.
- **B11 Dithered images**: photo in 2–8 colours via Bayer/error diffusion · tech, retro, lo-fi · dither at build time to PNG/WebP from palette tokens, `image-rendering: pixelated`; `alt=""` if decorative.
- **B12 Paper**: warm off-white with fibres · editorial, reading, docs · `oklch(0.97 0.012 85)` + fine (0.05) and blotch (`.02`, 0.03) noise; safest behind long text; dark = dark paper, not inverted.
- **B13 Contour lines**: topographic isolines · maps, outdoor, research, dividers · pre-generated SVG < 15 KB, `stroke` alpha ≈ 0.15, `vector-effect: non-scaling-stroke`; must not read as a chart.
- **B14 Cloud sky**: soft clouds on sky gradient · calm, weather, travel (cloud-computing cliché, justify it) · OKLCH sky gradient + low-frequency noise (`.008`, 5 octaves) mapped to white alpha, or a photo.
- **B15 Pixel mosaics**: big square colour tiles · games, creative tools, generative brand · a tiny image (e.g. 16×9 px) scaled with `image-rendering: pixelated; object-fit: cover`; tiles in a narrow L range.
- **B16 Lens distortion**: fisheye / barrel / chromatic aberration · experimental, music, fashion heroes · pre-render it; live displacement is costly and inconsistent; never under text; static unless reduced motion honoured.
- **B17 Stippled gradients**: gradient from dot density · editorial illustration, science, print-inspired · pre-render, or a dot grid masked by a gradient; text off the dense end.
- **B18 Square-dot grids**: tiny dots on a grid · canvas/whiteboard, dev and design tools, empty states · `linear-gradient(90deg, var(--surface) 14px, transparent 0) 0 0/16px 16px, linear-gradient(var(--surface) 14px, var(--dot) 0) 0 0/16px 16px`; pitch = spacing unit.
- **B19 Blurred photos**: photo as a soft colour field · media players, profile/detail headers · tiny (32–64px) image + `filter: blur(24px); transform: scale(1.1)` or pre-blurred; a scrim that guarantees A1 for any photo.
- **B20 Iridescent shapes**: holographic hue shift · beauty, fashion, launches; highest slop risk · `conic-gradient(from var(--angle) in oklch longer hue, oklch(0.85 0.12 0), oklch(0.85 0.12 0))` + `blur(40px)` in an `overflow: clip` parent; hue animation via `@property` only under `prefers-reduced-motion: no-preference`.
- **B21 Impressionist paintings**: painterly artwork crop · editorial, culture, luxury, calm · public-domain or commissioned (check licence, credit), AVIF + `srcset`, `object-position` on a quiet area; avoid AI "in the style of" (U6).
- **B22 ASCII and pixels**: characters or pixel art as image · dev tools, CLI, retro · pre-render to image/canvas; live text only as `<pre aria-hidden="true">` with `user-select: none`, capped size; mono is correct here (U4); animated rain stops under reduced motion.

### Code (PRs only)
- Tokens / CSS variables for colour, spacing, radius, type and motion.
- Every state: default, hover, focus, active, disabled, loading, empty, error, success.
- Responsive at 320 / 768 / 1280px+; respects `prefers-color-scheme`.
- No CSS without a fallback unless it is Baseline widely available.

## Severity

- **Blocker**: must fix before shipping. Any A failure (including text over a texture failing contrast, T2); a critical flow with no loading or error state; primary action unreachable.
- **Major**: clearly hurts usability or trust. Competing primary CTAs, generic headline, transitions over 300ms, bare `100vh` hero, a background competing with content or stacked textures (T1, T3), animated texture ignoring reduced motion (T4).
- **Minor**: polish. Non-concentric radius, solid hex border, non-tabular numbers, tile seams or moiré (T8), oversized texture files (T5).

## Scoring

Score each category 0–5 (5 exemplary, 4 minor polish only, 3 some majors, 2 several majors, 1 fundamental issues, 0 not addressed): **Accessibility (×2)**, Hierarchy & layout (M3, M13–M16, U5, U7, U8, U10, U17), Content & copy (U1, U2, U6, U9, U16), Visual craft (U3, U4, U11–U14, U18, C1–C5, C8, C10, M11, B1–B22, T1, T3, T5–T8), Interaction & feedback (U19, M17–M19, C6), Motion (M1–M10, M20, U20, C7, T4). T2 scores under Accessibility. Drop N/A categories. Overall = weighted average to one decimal.

- **PASS**: no blockers and overall ≥ 4.0
- **PASS WITH FIXES**: no blockers and overall ≥ 3.0
- **FIX REQUIRED**: any blocker, or overall < 3.0

## Output format

```markdown
## UI/UX QC — <what was reviewed>

**Verdict: PASS | PASS WITH FIXES | FIX REQUIRED** · Score <x.x>/5
<One sentence: the single most important takeaway.>

Context assumed: <platform, mode, design system, anything inferred>
Not checked: <e.g. motion and keyboard — static screenshot>

### Fixes
| # | Severity | Rule | Where | Problem | Fix |
|---|---|---|---|---|---|
| 1 | Blocker | A1 | Hero subtitle | #9CA3AF on #FFF = 2.5:1 | Use oklch(0.45 0 0) → 7.4:1 |

### Scores
| Category | Score | Note |
|---|---|---|

### What's working
- <2–4 specific strengths worth keeping>
```

- Every fix cites a rule ID, a location, and a concrete change (value, token or code). "Improve contrast" is not a fix.
- Order fixes by severity, then visual prominence. For PRs, give file and line, and offer a patch for minor fixes.
- Be direct and brief. If it passes, say so and stop.

## Staying current

When a rule here looks outdated (a new WCAG criterion, a CSS feature reaching Baseline, a platform guideline change), follow the current standard, say so in the review, and suggest updating the rule with its source.

## Credits

Source lists shared by charles.unemployed, drawing on Raphael Salaja's userinterface.wiki, Disney's *The Illusion of Life* (Thomas & Johnston), Jon Yablonski's Laws of UX, and Emil Kowalski for frequency of use. Accessibility rules from W3C WCAG 2.2. Background treatment list (B1–B22) from a "22 Backgrounds to Make Designs Less Flat" post shared by Baruch; implementations and T1–T8 by this agent.
