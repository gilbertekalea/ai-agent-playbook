---
name: spec-first-builder
description: Ship production web apps with one AI agent using the spec-first loop: brief, plan, one small pass, evidence, human review, deploy on go-ahead.
---

# Spec-First Builder

## What this skill does

This skill turns one AI agent into a steady building partner for a
solo builder shipping a real web app. It stops long code,
no written plan, no repeatable test, and an unchecked deploy.

The loop is short and always in the same order:
1. Debrief first. No code before the debrief.
2. Write the brief. One job, one acceptance list, clear non-goals.
3. Plan in phases. Each phase has exit criteria.
4. Build one small pass. One feature, or one small slice.
5. Show evidence. Tests, a live preview, and what was checked.
6. Human reviews. The human decides go or no-go.
7. Deploy only on a clear go-ahead. Then verify on the live site.
8. Update the docs. Docs are part of done, not an afterthought.

## Who it is for

- A solo builder shipping a web app with one AI agent.
- A non-technical founder who wants a working product, not a demo.
- A developer tired of rewrites caused by vague instructions.
It works in Claude Code, Cursor, ChatGPT, Codex, and any chat agent.

## When to use it

- Starting a new web app from an idea.
- Adding a feature to an app that already exists.
- Fixing a feature that keeps breaking in review.
- Taking over a half-built app and making it shippable.
- Any time the agent has built the wrong thing before.

## When NOT to use it

- A one-off question with no build attached. Just ask the question.
- A throwaway experiment you will delete today. A short note is enough.
- A task where the human already wrote a full brief. Use that brief,
  and use the companion skill `feature-brief-writer` next time.
- An emergency fix to a live outage. Fix, verify, then write the
  brief and the decision down afterwards.

## The two roles

**The human decides:** direction, taste, go or no-go, priorities,
and exceptions. Nothing goes to the main branch or to production
without the human saying so in plain words. Silence is not approval.
**The agent delivers depth:** build in small passes, verify against
the code and the live site, bring ranked options with trade-offs
and risks and a clear recommendation, then let the human pick.
Own mistakes in plain language, and fix them in the files and
the docs, not only in chat. Hold the bar: no demo logins in
client code, no untested money math, no fake data shown as real,
no docs left stale. The agent does not decide scope, merge,
deploy, or change an account setting on its own. It asks first
when an action is hard to undo, affects other people, or
changes money or data.

## Step 1 — Debrief first

Debrief before acting. Small request: 3 sentences. Large: write it out.
The debrief has 4 parts:
1. **Where we are:** project, phase, what is already shipped,
   what is in progress.
2. **What the request maps to:** which feature or phase this
   belongs to.
3. **Deviation check:** does it jump phases, skip exit criteria,
   add scope, or clash with a locked decision? Say so plainly.
4. **What happens next:** the smallest next step, and what
   decision the human must make before we proceed.
Example: "We are in Phase 1, Feature 1 (affordability check).
This maps to Feature 1. It does not jump phases. Next step:
confirm the acceptance criteria in writing, then build pass 1.
Decision needed: include utilities in version 1, or park it?"

## Step 2 — Write the brief

Write the idea in one sentence first:
> For [one audience], who [one painful job], [product] does
> [one core job] so they [one outcome].
If you cannot write that sentence, ask questions until you can.
Then write a one-page brief. Every field is required.
- **Goal:** the one job this feature does, in plain words.
- **Where:** where it fits in the project and the phased plan.
- **Context:** what already exists, what the user already sees.
- **Constraints:** stack, budget, time, rules, and limits.
- **Non-goals:** what this will NOT do. Be explicit. Non-goals
  stop scope creep.
- **Definition of done:** the acceptance list. See Step 3.
- **Verify by:** how a human checks it on a live preview,
  step by step.
- **Assumptions:** what you are guessing. Name each guess.
- **Stop rule:** what makes you stop and ask instead of guessing.
- **Do not:** things the agent must not touch, change, or rebuild.
For the full template, use the companion skill `feature-brief-writer`.

## Step 3 — Acceptance criteria before code

Write the acceptance list before any code. Every item must be
testable by a human on a live preview, in plain Given / When / Then.
Format: Given [starting state], When [user does X],
Then [exact result the user sees].
Rules: every criterion names what the user sees, not what the
code does. Include at least one error case; the happy path alone
is not enough. Include the exact wording for key messages when
wording matters. Never write "works correctly". Write what the
user sees. If a criterion cannot be tested by a human, rewrite it.

## Step 4 — Plan in phases, with exit criteria

Break the work into phases, each with 2 to 5 exit criteria.
You cannot leave a phase until they pass with evidence.
Example shape: Phase 1, Core checks — exit: the main job passes
user testing, docs are updated, no personal data is stored by
mistake. Phase 2, Second job — exit: it passes user testing, its
delete rule is verified, its paywall works if it has one.
Phase 3, Polish and launch — exit: copy is finished in every
language you ship, the privacy notice is current, the launch
checklist passes. A new feature jumping phases is a deviation.
A skipped exit criterion is a deviation. Out-of-order work is a
deviation. Flag it in plain words. Do not quietly skip it.

## Step 5 — Build in one small pass

One pass is one feature, or one small slice of a feature. A pass
ends with a live preview, a short summary, and human review.
The summary always states: what changed, with file names; what
was tested, and the result; what the risks are, and what is
still unknown; what the human should check on the preview.
Build rules: smallest useful slice first — ship the slice that
helps a user, even if it is simple. Data and math before polish:
if the calculation is wrong, polish does not matter. Error
states are handled: no blank screens, no silent failures.
A human tests on phone and laptop, not only on a large screen.
When a request is not understood, stop and confirm first.
Guessing and building the wrong thing costs more than asking.

## The 4 gates

Nothing moves past a gate without the human.
**Gate 1 — Brief gate:** the human reviews the brief.
Acceptance criteria are agreed. Non-goals are locked.
No code until this gate passes.
**Gate 2 — Plan gate:** the human approves the phased plan
and the order of work. The next feature to build is named.
**Gate 3 — Review gate:** the human tests the pass on a live
preview, using the acceptance list, and tests at least one
failure path: bad input, bad upload, or a payment failure.
Bugs are recorded with the exact steps that show the bug, the
page, and the wording seen. The agent fixes in a fix pass.
The human re-tests.
**Gate 4 — Deploy gate:** the agent summarises what changed,
what was tested, risks, and what is still unknown. The human
reviews on a preview or staging link and gives an explicit
go or no-go. Only then does the agent push or deploy to
production. Production stays unchanged while staging is tested.

## Step 6 — Live verify, not status messages

A deploy status or green check is not proof. Open the live
site, confirm the new behaviour is serving, and state the
address and what you saw. Watch 24 hours for errors and
payments. Know the rollback plan — how to undo the change —
before shipping the riskiest change.

## Step 7 — Lock decisions the same session

When the human says "that is exactly what is in my head",
write it down that same session. A "no" is final: do not
re-ask it later. A parked decision stays parked, with a
revisit trigger (date, event, or condition). When the human
overrules a rule, record the exception. When the human says
"stop", halt at once and state what stopped.

## Step 8 — Docs are part of done

A feature is not done until the docs reflect it: user guide,
developer notes, FAQ, project plan, decision log. Then run a
5-minute retro: what shipped? What worked? What rule to lock?
What to stop? What was parked, and what brings it back?

## Failure modes — and the fix

- **Built the wrong thing.** Cause: no brief, or a vague
  brief. Fix: stop, write the brief, get it reviewed.
- **Pass too big to review.** Cause: two jobs in one pass.
  Fix: split it. "And also" in a brief is a split signal.
- **"Done" with no evidence.** Cause: a status message was
  treated as proof. Fix: live verify, and show the file,
  the test, and the live check.
- **Scope keeps growing.** Cause: no non-goals. Fix: write
  non-goals, lock them, park extras with a trigger.
- **Same mistake twice.** Cause: the lesson was never written
  down. Fix: add one sentence to the docs, same session.
- **Blaming the cache or the build.** Cause: the code was not
  checked. Fix: verify in the code first, then check the build.

## Proof — real projects, real method

This method was used to ship real apps, not demos:
- **Predock** — live at predock.vercel.app. A Montreal BIXI
  tool that predicts bikes and docks. Shipped in small passes
  in November 2026, including a type-split forecast, a
  same-station guard, and a one-tap ride.
- **CampusNest** — student housing for Quebec universities.
  Built feature by feature, with a phased plan and exit criteria.
- **LoyerCheck** — a Quebec rent toolkit. Rent checks use the
  TAL new-regime reference (3.1% base) as a reference, not a cap,
  with the math shown.
They prove the loop works on live products, not a promise
of the same timeline for your project.

## Quick Start — paste this into your agent

Copy the 10 lines below into your agent at the start of a
project or feature. Fill in the brackets. No code until the
agent answers with a debrief and a brief.
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
The same prompt, plus the project spine checklist, lives
in `reference/quickstart.md`.

## Reference files

- `reference/quickstart.md` — the paste-in prompt and the
  project spine checklist: the files every project needs
  from day one.
