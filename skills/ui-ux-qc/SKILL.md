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
- **U13** Remove embellishments (glows, blobs, glassmorphism, sparkles, emoji headings) that carry no meaning.
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

### Code (PRs only)
- Tokens / CSS variables for colour, spacing, radius, type and motion.
- Every state: default, hover, focus, active, disabled, loading, empty, error, success.
- Responsive at 320 / 768 / 1280px+; respects `prefers-color-scheme`.
- No CSS without a fallback unless it is Baseline widely available.

## Severity

- **Blocker**: must fix before shipping. Any A failure; a critical flow with no loading or error state; primary action unreachable.
- **Major**: clearly hurts usability or trust. Competing primary CTAs, generic headline, transitions over 300ms, bare `100vh` hero.
- **Minor**: polish. Non-concentric radius, solid hex border, non-tabular numbers.

## Scoring

Score each category 0–5 (5 exemplary, 4 minor polish only, 3 some majors, 2 several majors, 1 fundamental issues, 0 not addressed): **Accessibility (×2)**, Hierarchy & layout (M3, M13–M16, U5, U7, U8, U10, U17), Content & copy (U1, U2, U6, U9, U16), Visual craft (U3, U4, U11–U14, U18, C1–C5, C8, C10, M11), Interaction & feedback (U19, M17–M19, C6), Motion (M1–M10, M20, U20, C7). Drop N/A categories. Overall = weighted average to one decimal.

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

Source lists shared by charles.unemployed, drawing on Raphael Salaja's userinterface.wiki, Disney's *The Illusion of Life* (Thomas & Johnston), Jon Yablonski's Laws of UX, and Emil Kowalski for frequency of use. Accessibility rules from W3C WCAG 2.2.
