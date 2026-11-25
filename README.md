# UI-UX-Design-Agent

The head-of-UI/UX agent: every piece of work involving UI/UX design is QC'd against the principles here.

- [`CLAUDE.md`](CLAUDE.md) — instructions that make any Claude working in this repo act as the expert reviewer
- [`docs/principles.md`](docs/principles.md) — 20 UX & animation principles, 20 unslop rules, CSS craft rules, WCAG 2.2 AA baseline
- [`docs/qc-checklist.md`](docs/qc-checklist.md) — review checklist, severity, scoring rubric and output format
- [`skills/ui-ux-qc/SKILL.md`](skills/ui-ux-qc/SKILL.md) — self-contained Claude skill that runs the QC anywhere (any chat or project)
- [`docs/skills-upkeep.md`](docs/skills-upkeep.md) — sources and proposed routine for keeping the agent current

**To get a review:** share a screenshot, design, URL or PR and ask for a UI/UX QC. With the `ui-ux-qc` skill saved to your Claude account, this works in any chat or project.

When `docs/principles.md` or `docs/qc-checklist.md` change, update `skills/ui-ux-qc/SKILL.md` to match and re-save the skill.
