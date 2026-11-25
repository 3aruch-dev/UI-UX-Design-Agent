# Keeping Skills Current

How this agent stays an up-to-date UI/UX expert. This is a **proposal**: no automation has been set up yet. Baruch decides whether to turn on the routine in section 3.

## 1. Sources to follow

**Foundational (re-read yearly, rarely change)**
- Disney — *The Illusion of Life* (12 principles of animation)
- Jon Yablonski — [Laws of UX](https://lawsofux.com)
- W3C — [WCAG 2.2](https://www.w3.org/TR/WCAG22/) and [WCAG 3 drafts](https://www.w3.org/TR/wcag-3.0/)
- Nielsen Norman Group — [nngroup.com/articles](https://www.nngroup.com/articles/)

**Craft & motion (check monthly)**
- Raphael Salaja — [userinterface.wiki](https://userinterface.wiki)
- Emil Kowalski — [emilkowal.ski](https://emilkowal.ski), [animations.dev](https://animations.dev)
- charles.unemployed (original source of these lists)
- Rauno Freiberg — [rauno.me](https://rauno.me) / [Web Interface Guidelines](https://interfaces.rauno.me)
- Josh W. Comeau — [joshwcomeau.com](https://www.joshwcomeau.com)

**Platforms & the web (check monthly)**
- Apple [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/) (and WWDC design sessions)
- Google [Material Design 3](https://m3.material.io)
- [web.dev](https://web.dev) / Chrome for Developers blog, [WebKit blog](https://webkit.org/blog/), [MDN](https://developer.mozilla.org) release notes
- [Baseline](https://web.dev/baseline) / [caniuse.com](https://caniuse.com) — what CSS (e.g. `text-wrap`, `@starting-style`, view transitions, anchor positioning) is safe to recommend

**Accessibility (check monthly)**
- [WebAIM](https://webaim.org) (including the yearly WebAIM Million report), [Deque blog](https://www.deque.com/blog/), [A11y Project](https://www.a11yproject.com)

## 2. What counts as an update

A change is worth adopting when it:
- changes a standard (new WCAG version or success criterion, a platform HIG change), or
- makes a new CSS/JS capability broadly available (Baseline "widely available"), or
- is a well-evidenced practice from at least one foundational or craft source that contradicts or sharpens an existing rule.

Trend-driven aesthetics (a new "look") are noted but not turned into rules unless they improve usability.

## 3. Proposed review routine

**Monthly (≈ 30 min, e.g. first Monday)**
1. Skim the monthly sources above for new articles, releases and spec changes since the last review.
2. For each candidate change, write a one-line proposal: rule ID affected, what changes, source link.
3. Open one PR titled `Skills update — <YYYY-MM>` that edits `principles.md` / `qc-checklist.md` and appends to the changelog below. Baruch approves before merge.

**Quarterly**
- Re-check every threshold and code example in `principles.md` against current browser support and guidance.
- Review the last quarter's QC reports: which rules fired most, which caused disagreement, and whether any rule is missing or too strict.

**Yearly**
- Re-read the foundational sources; refresh the sources list (drop dead links, add new voices).

**Possible automation (not created):** a scheduled Claude Code task on the first Monday of each month that runs steps 1–3 and opens the PR for review. Turning this on needs Baruch's go-ahead.

## 4. Changelog

| Date | Change | Source |
|---|---|---|
| 2026-11-25 | Initial principles: 20 UX & animation principles, 20 unslop rules, 10 CSS craft rules, WCAG 2.2 AA baseline | charles.unemployed; see [principles credits](./principles.md#5-credits) |
| 2026-11-25 | Added section 5, background treatments B1–B22 with guardrails T1–T8; QC group D2; `U13` now points to section 5 | "22 Backgrounds to Make Designs Less Flat" list shared by Baruch; implementations by this agent |
