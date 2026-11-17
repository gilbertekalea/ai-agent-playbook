# Quick Start — Spec-First Builder

Copy the prompt below into Claude Code, Cursor, ChatGPT, or Codex.
Fill in the brackets. Do not write code until the agent answers
with a debrief and a brief.

## Paste-in prompt (10 lines)

> 1. Follow the spec-first-builder skill.
> 2. Project: [name]. One-sentence job: [For whom, what job, what outcome].
> 3. Start with a debrief: where we are, what this maps to,
>    any deviation, smallest next step.
> 4. Help me write a one-page brief: Goal, Where, Context,
>    Constraints, Non-goals, Definition of done, Verify by,
>    Assumptions, Stop rule, Do not.
> 5. Write acceptance criteria in Given / When / Then, with
>    at least one error case. Stop before building.
> 6. I will review the brief first. No code until I approve it.
> 7. Then build one small pass, show a live preview, and
>    summarise what changed, what was tested, and risks.
> 8. I test on phone and laptop. Nothing merges or deploys
>    without my explicit go-ahead.
> 9. Verify on the live site, not from a status message.
> 10. Update the docs before calling it done.

## Project spine checklist — set this up on day one

Every project needs these files, even before the first feature.
Tick each one off, or write "not needed, because..." next to it.

- [ ] `AGENTS.md` — how this project works, in plain words:
  who decides, the spec-first loop, the 4 gates, the stop rule.
- [ ] `README.md` — what the app does, for whom, how to run it
  locally, where the live site is.
- [ ] `docs/specs/` — one file per feature, named after the job,
  e.g. `docs/specs/rent-increase-check.md`. Each holds the brief.
- [ ] `docs/decisions/` — one short note per locked decision:
  what was decided, when, why, and what it replaces.
- [ ] `PROGRESS.md` — where we are: phase, shipped features,
  in progress, parked items with revisit triggers.
- [ ] `FEATURES.md` — the feature list, one line per feature:
  job, size (S/M/L), priority score, state
  (Awaiting / Picked / Shipped / Dropped).
- [ ] Tests — at least one check for money or math logic with
  real numbers, and one check for each error case in the brief.
- [ ] Preview deploy — every pass gets a preview address the
  human can open on phone and laptop.
- [ ] Live site — the production address, written down in
  `README.md` and `PROGRESS.md`, checked after every deploy.
- [ ] Rollback plan — the steps to undo the riskiest change,
  written before that change ships.

## First-week order

1. Day 1: one-sentence job, brief, phased plan. No code yet.
2. Next: build Phase 1, Feature 1, in one small pass.
3. Review it on a live preview, phone and laptop.
4. Fix, re-test, update docs, then name the next feature.
