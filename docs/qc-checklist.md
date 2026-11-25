# QC Review Checklist & Rubric

Apply this to every design, mockup, screenshot, prototype or UI pull request. Rule IDs point to [principles.md](./principles.md).

## 1. Before reviewing

1. **Identify the input type.** Static (screenshot, mockup, Figma frame) or live (URL, running app, PR with code).
2. **Identify context.** Product, audience, platform (web / iOS / Android / desktop), light and/or dark mode, and any existing design system. If unknown, say what you assumed.
3. **Scope the checks.** Static inputs can't be checked for motion, keyboard or code rules — mark those `N/A (static)` rather than passing them. For PRs, read the diff and run or screenshot the UI where possible.

## 2. Checklist

Work through each group. Mark every item **Pass**, **Fix**, or **N/A** (with reason).

### A. Accessibility (blocking)
- [ ] A1 Text contrast ≥ 4.5:1 (large ≥ 3:1) — measured
- [ ] A2 Non-text contrast ≥ 3:1 for controls, focus rings, meaningful icons
- [ ] A3 Visible focus on all interactive elements, not obscured
- [ ] A4 Full keyboard operation, logical order, `Esc` closes, focus returns
- [ ] A5 Targets ≥ 24px (aim 44px on touch)
- [ ] A6 Meaning not conveyed by colour alone
- [ ] A7 Alt text and accessible names
- [ ] A8 Labelled inputs; helpful, linked error messages
- [ ] A9 `prefers-reduced-motion` respected; no flashing
- [ ] A10 Works at 320px and 200% zoom
- [ ] A11 Semantic HTML / correct roles
- [ ] A12 Status messages announced

### B. Hierarchy & layout
- [ ] M3 One focal point per view (squint test)
- [ ] U7 Main feature visibly dominant
- [ ] U10 One primary CTA, clearly strongest
- [ ] U8 Grouping by spacing; inter-group gap clearly > intra-group
- [ ] U5 Spacing, type, radius, colour from tokens — no magic numbers
- [ ] U17 Optically aligned
- [ ] M15 / M16 Choices limited and chunked; recommended option highlighted

### C. Content & copy
- [ ] U9 Headlines specific to this product
- [ ] U2 No redundant labels
- [ ] U1 No filler badges
- [ ] U16 Headlines balanced, measure constrained, no orphans
- [ ] U6 Real product shown

### D. Visual craft
- [ ] U11 / C3 Neutral palette + one accent, defined in OKLCH
- [ ] C4 Gradients interpolate `in oklch`
- [ ] U13 No decorative embellishments without purpose
- [ ] U3 No random italics / gradient words
- [ ] U4 Monospace only for code/data
- [ ] U18 Tabular numerals on prices, tables, counters
- [ ] U12 / C5 One icon family; stroke matches text weight
- [ ] U14 / C1 Nested radii concentric
- [ ] C2 Borders use alpha colours
- [ ] C8 Dark mode: elevation via lighter surfaces; no pure black/white
- [ ] C10 Product shots on white use `multiply` (light backgrounds only)
- [ ] M11 Consistent light source and elevation scale

### D2. Backgrounds & texture (when a background treatment B1–B22 is used)
- [ ] T1 One treatment per view, with a stated reason (passes `U13`)
- [ ] T2 Text over texture meets A1 at the worst sampled point; body text never directly on busy texture
- [ ] T3 Texture contrast low behind content (≈ ΔL ≤ 0.06, grain 3–8%); squint test still finds the content first
- [ ] T4 Static by default; any motion is slow `transform`/`opacity`, off under reduced motion, pausable > 5s
- [ ] T5 CSS/SVG preferred; tiles < 20 KB, hero < ~150 KB AVIF/WebP; no large `backdrop-filter`/SVG filters on scrolling areas; LCP not delayed
- [ ] T6 OKLCH tokens, solid `background-color` fallback, decorative layer `pointer-events: none` / `aria-hidden`, `@supports` for non-Baseline features
- [ ] T7 Dark-mode variant designed; texture removed under `prefers-contrast: more` and `forced-colors`
- [ ] T8 No seams or moiré; pattern size on the spacing grid; checked at 320 / 768 / 1280px+ and 200% zoom

### E. Interaction & feedback
- [ ] U19 Loading, empty, error and success states designed
- [ ] M17 Feedback within 100ms; async > 400ms shows progress
- [ ] M13 / M14 Primary targets large and near; hit areas extended
- [ ] M18 Inputs forgiving, output normalised
- [ ] M19 Conventions followed (or deviations justified)
- [ ] C6 Scroll regions have edge masks / affordance

### F. Motion (live or prototype only)
- [ ] M9 UI transitions ≤ 300ms; exits faster than enters
- [ ] M6 Eased, never linear spatial movement
- [ ] M4 Animates `transform`/`opacity`; no layout thrash
- [ ] M20 High-frequency actions near-instant
- [ ] U20 / M10 / M8 Motion communicates; exaggeration reserved for key moments
- [ ] M1 / M2 / M5 / M7 Physicality (press scale, anticipation, overshoot, arcs) used subtly and where it fits
- [ ] C7 Icon swaps blur + fade

### G. Code (PRs only)
- [ ] C9 `100dvh` (with `100vh` fallback), never bare `100vh` on full-height sections
- [ ] Colours, spacing, radii via tokens / CSS variables
- [ ] No `outline: none` without replacement focus style
- [ ] Responsive at 320 / 768 / 1280px+

## 3. Severity

| Level | Meaning | Examples |
|---|---|---|
| **Blocker** | Must fix before shipping. Excludes users or breaks the task. | Any `A` failure (including text over a texture failing contrast, `T2`); no loading/error state on a critical flow; primary action unreachable |
| **Major** | Clearly hurts usability or trust; fix in this iteration. | Two competing primary CTAs; generic headline on a landing page; 600ms transitions; `100vh` hero; background competing with content or stacked textures (`T1`, `T3`); animated texture ignoring reduced motion (`T4`) |
| **Minor** | Polish. Fix when touching the area. | Non-concentric radius; solid hex border; non-tabular numbers in a small table; visible tile seam or moiré (`T8`); oversized texture file (`T5`) |

## 4. Scoring rubric

Score each category 0–5:

| Score | Meaning |
|---|---|
| 5 | Exemplary — nothing to fix |
| 4 | Solid — minor polish only |
| 3 | Acceptable — some majors |
| 2 | Weak — several majors |
| 1 | Poor — fundamental issues |
| 0 | Not addressed |

Categories (weight): **Accessibility (×2)**, Hierarchy & layout, Content & copy, Visual craft, Interaction & feedback, Motion. Background checks (D2) score under Visual craft, except `T2` (Accessibility) and `T4` (Motion). Categories marked N/A are dropped from the total. Overall = weighted average, rounded to one decimal.

**Verdict:**
- **PASS** — no blockers and overall ≥ 4.0
- **PASS WITH FIXES** — no blockers, overall ≥ 3.0; listed majors should be fixed before or soon after shipping
- **FIX REQUIRED** — any blocker, or overall < 3.0

## 5. Output format

Every review uses this shape. Lead with the verdict; keep fixes concrete enough to implement without further questions.

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
| 2 | Major | U10 | Pricing header | "Start trial" and "Book demo" are equal weight | Make "Start trial" filled accent; "Book demo" ghost |

### Scores
| Category | Score | Note |
|---|---|---|
| Accessibility (×2) | 2 | Contrast and focus issues |
| Hierarchy & layout | 4 | |
| Content & copy | 3 | Generic headline |
| Visual craft | 4 | |
| Interaction & feedback | 3 | No loading state on submit |
| Motion | N/A | Static |

### What's working
- <2–4 specific strengths worth keeping>
```

Rules for the output:
- Every Fix cites a rule ID, a location, and a concrete change (value, token, or code) — never just "improve contrast".
- Order fixes by severity, then by visual prominence.
- For PRs, give file and line where possible, and offer a patch for minor fixes.
- Don't pad: if it passes, say so and stop.
