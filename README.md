# Spec-First Builder — ship production web apps with one AI agent

**Stop vibe-coding. Start shipping.** A research-backed method for a solo builder working with one AI agent to ship world-class production web apps: spec first, small reviewable passes, evidence-based gates.

Built from real shipped products — Predock (live at predock.vercel.app), CampusNest, and LoyerCheck — not from theory alone.

- **Free here:** the core skill + this guide. Install it in 2 minutes and use it today.
- **Premium pack ($29 on Agensi):** all 6 skills, with templates, checklists, and rescue prompts. Singles $12 each. You keep the GitHub version free forever; the pack is the full working kit.

> Agensi: https://www.agensi.io/sell — list, search, and buy skills there. GitHub stays the free home of the core method.

## The loop, in one paragraph

One project, one agent, one task at a time. For each feature: (1) brief in writing — goal, scope, non-goals, constraints, and a checkable definition of done; (2) agent explores and drafts a plan — you approve the plan, not just the code; (3) agent builds in one small, reviewable pass on its own branch, with tests and a check it can run; (4) a fresh-context review checks the work against the plan; (5) verify on a preview, then on the live site — never accept a status message as proof; (6) lock any decision in writing the same session, update docs as part of done, and deploy only on your explicit go-ahead. Repeat.

Your job shifts from writing code to writing precise briefs, judging evidence, and guarding the gates.

## The 6 skills

| # | Skill | Price | What it does |
|---|-------|-------|--------------|
| 1 | `spec-first-builder` | **Free** | The whole loop in one page: debrief, brief, plan, one pass, 4 gates, live verify, docs as part of done. Start here. |
| 2 | `feature-brief-writer` | $12 | Vague idea → one-page, checkable spec. 5-question interview, Given/When/Then criteria with an error case, sizing, scoring. |
| 3 | `small-pass-builder` | $12 | One pass = one behaviour, one branch, one-sentence diff. Tests first where testable, no UI churn, clean close-out. |
| 4 | `ship-gates` | $12 | Six evidence gates: spec check, fresh-context review, human diff review, security, preview, live-site verification. Plus Production Definition of Done. |
| 5 | `fix-it-when-wrong` | $12 | Rescue when the agent drifts, repeats a mistake, claims done without evidence, or ships the wrong thing. Includes true revert. |
| 6 | `docs-decisions-keeper` | $12 | Decisions, docs, and memory stay current. Decision log + ADR-lite, lock it in writing, weekly health pass. |

**Bundle: all 6 for $29 on Agensi** — $72 bought singly, $29 as a pack. You keep 70% as the creator; Agensi covers Stripe fees and handles taxes.

## Install — 2 minutes

Each skill is a folder with a `SKILL.md` + `reference/` templates. No scripts, no installs, no keys. It works in Claude Code, Cursor, ChatGPT, Codex, Gemini CLI, and any agent that reads `SKILL.md`.

**Claude Code**
```bash
# from your project root
mkdir -p .claude/skills
cp -r skills/spec-first-builder .claude/skills/
# then in Claude Code: "Follow the spec-first-builder skill."
```

**Cursor**
```bash
mkdir -p .cursor/skills
cp -r skills/spec-first-builder .cursor/skills/
# then in Cursor chat: "Follow the spec-first-builder skill."
```

**Codex / ChatGPT / any chat agent**
Copy `skills/spec-first-builder/SKILL.md` into your project, or paste the 10-line Quick Start from that skill at the start of a project. The skill tells the agent what to do; you don't need to memorise it.

**Full pack (Agensi)**
Buy the pack on Agensi and your connected agent loads it on use — no files to copy, updates arrive automatically.

## Quick start — paste this into your agent

> 1. Follow the spec-first-builder skill.
> 2. Project: [name]. One-sentence job: [For whom, what job, what outcome].
> 3. Start with a debrief: where we are, what this maps to, any deviation, smallest next step.
> 4. Help me write a one-page brief: Goal, Where, Context, Constraints, Non-goals, Definition of done, Verify by, Assumptions, Stop rule, Do not.
> 5. Write acceptance criteria in Given/When/Then, with at least one error case. Stop before building.
> 6. I will review the brief first. No code until I approve it.
> 7. Then build one small pass, show a live preview, and summarise what changed, what was tested, and risks.
> 8. I test on phone and laptop. Nothing merges or deploys without my explicit go-ahead.
> 9. Verify on the live site, not from a status message.
> 10. Update the docs before calling it done.

## Proof — real projects

- **Predock** — live at predock.vercel.app. Montreal BIXI tool that predicts bikes and docks. Shipped in small passes in November 2026, including a type-split forecast, a same-station guard, and a one-tap ride.
- **CampusNest** — student housing for Quebec universities. Built feature by feature, with a phased plan and exit criteria.
- **LoyerCheck** — Quebec rent toolkit. Rent checks use the TAL new-regime reference (3.1% base) as a reference, not a cap, with the math shown.

These prove the loop works on live products. They are not a promise of the same timeline for your project.

## What changed in v2 (Nov 2026)

v1 (Oct 16, 2026) was a 317-line research README: strong sources, zero install, zero templates, zero skills. v2 turns it into something an agent can actually run:

- 6 installable `SKILL.md` skills with valid frontmatter, ready for Agensi, Claude Code, Cursor, and Codex
- Copy-paste briefs, checklists, decision log, retro, and rescue prompts — all as reference files, not prose you have to retype
- Install guides for every major agent
- A rescue skill for the moment every solo builder hits: the agent went wrong, and you need to stop, reset, and rebuild without losing the good work
- Free core stays free on GitHub; the full kit is the $29 Agensi pack

## Sources

Primary, verified live Oct 16, 2026: Anthropic — Claude Code best practices; Effective harnesses for long-running agents; Effective context engineering; Multi-agent research system. GitHub Spec Kit — https://github.com/github/spec-kit

Practitioner material is cited inside each skill where it is used, and marked as secondary where it rests on a summary rather than a directly verified primary page. Quantified claims from secondary summaries are not quoted as established facts.

## License

MIT. Use it, adapt it, ship with it. The premium pack on Agensi is the same method plus the full templates and checklists — buying it supports keeping the core free here.
