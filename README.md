# Best method for a solo builder working with one AI coding agent to ship world-class production web apps

**Audience:** Solo builder who is a systems analyst + business analyst (not a full-time professional developer), uses one AI agent as the main engineering partner, has already shipped production apps (e.g. a Montreal transit app with a real routing engine, live data feeds, payments, auth).
**Date researched:** 2026-10-16. Primary guidance verified live from Anthropic and GitHub; practitioner claims marked as index/secondary where they rest on summaries rather than a directly verified primary page.
**Bottom line:** Do not run the project as one long chat. Run it as a **spec → plan → one small pass → evidence → human review → deploy-on-go-ahead** loop, with all durable state in files and git, never only in the conversation. The builder's job shifts from writing code to writing precise briefs, judging evidence, and guarding the gates. That is the consensus across Anthropic's own guidance, GitHub Spec Kit, and the strongest practitioner workflows.

---

## Summary — the recommended workflow in one paragraph

One project, one agent, one task at a time. For each feature: (1) brief in writing with goal, scope, non-goals, constraints and a checkable definition of done; (2) agent explores and drafts a plan — human approves the plan, not just the code; (3) agent implements in one small, reviewable pass on its own branch, with tests and a verification step it can run; (4) a fresh-context review (agent reviewer + human diff review) checks the work against the plan; (5) verify on a preview deployment, then on the live site after deploy — never accept a status message as proof; (6) lock any decision in a decision record the same session, update docs as part of done, and only deploy on the human's explicit go-ahead. Repeat. Parallel agents and autonomous loops are optional accelerators for narrow, well-bounded work — not the default.

---

## 1. What the strongest practitioners do

### 1.1 Spec-driven / plan-first is the default for production work

The strongest pattern is: **written spec first, plan second, code third, verification against the spec last.**

- GitHub Spec Kit formalises this as: constitution once per project, then per feature `specify → plan → tasks → implement → converge`, reviewing the result of each step before continuing. It treats specifications as the source of truth and code as their expression. Its bug-fixing track keeps diagnosis, repair and verification separate, and states that missing verification is not a successful fix. Source: GitHub Spec Kit, verified live, https://github.com/github/spec-kit
- Anthropic's Claude Code guidance says the same in tool terms: use plan mode to separate exploration from execution, have the agent interview the human and write a spec before implementing, then start a fresh session to execute that spec. The most useful specs are self-contained: they name files and interfaces, state what is out of scope, and end with an end-to-end verification step that proves the feature works. Time spent making the spec precise pays off more than time spent watching implementation. Source: Anthropic, verified live, https://www.anthropic.com/engineering/claude-code-best-practices
- The spec-driven vs vibe-coding contrast in practitioner material is consistent: in spec-driven work, scope is defined upfront (in and out), tests map to acceptance criteria so pass/fail is objective, and every feature traces back to a requirement; in unstructured work the AI invents requirements and architecture, and problems are found in QA or production. Source: index, https://github.com/orwanec/sdd-meetup-demo

**Practical rule for this builder:** vibe-code only for throwaway exploration. Anything that will ship goes through a short written spec + plan. For a solo builder, "short" means one page, not a corporate PRD — see §7 for the exact template.

### 1.2 Small reviewable passes beat full builds

Anthropic's long-running-agent research found two characteristic failures when an agent is asked to build a whole app at once: it tries to do too much at once, runs out of context mid-implementation, and leaves the next session a half-implemented, undocumented feature to guess at; and later, a fresh agent sees progress and declares the job done prematurely. The fix was to work on only one feature at a time and leave the environment in a clean, mergeable state after every session. Source: Anthropic, verified live, https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents

The same idea appears in the Ralph Wiggum loop (Geoffrey Huntley): every iteration is a fresh session that does **one** task, proves it with tests, commits, and exits. Memory lives in files and git, not in the model. Source: index, https://github.com/chrisbrooksbank/cb-ralph-wiggum/blob/HEAD/README.md — and its do-not-use list is important: do not use that autonomous-loop style for exploratory work without clear acceptance criteria, major refactors without explicit success conditions, security-critical code requiring human review, or anything needing immediate human judgment. Source: index, https://github.com/tangledgroup/tangled-skills/blob/HEAD/misc/agent-ralph-wiggum-snarktank/SKILL.md

**Practical rule:** one pass = one user-visible behaviour or one bug, one branch, one commit/PR-sized diff the human can actually read. If the human cannot describe the diff in a sentence, the pass was too big. Anthropic makes the same point about planning: if the diff could be described in one sentence, skip the plan; otherwise plan first. Source: Anthropic, verified live, https://www.anthropic.com/engineering/claude-code-best-practices

### 1.3 Acceptance criteria are the contract

Every pass needs acceptance criteria written **before** code, in observable language: what a user (or a test, or a script) can see, click, or measure. Anthropic's harness work used a feature list in which every feature was initially marked failing, with concrete steps, and agents could only flip a feature to passing after careful testing — with a strong instruction never to edit or remove tests to make them pass. Source: Anthropic, verified live, https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents

Test-driven development with an agent is the sharpest version: write failing tests first, commit them, then implement until they pass without modifying the tests. Source: index, summarising Anthropic practice, https://github.com/iam-veeramalla/claude-code-crash-course/blob/HEAD/14-workflows-and-best-practices.md

### 1.4 How the human stays in control without becoming a bottleneck

The human controls **four gates**, not every keystroke:

1. **Brief gate** — approve the brief/spec before code.
2. **Plan gate** — approve the plan before implementation. Correcting a plan costs one message; correcting bad code costs many. Source: index, https://github.com/iam-veeramalla/claude-code-crash-course/blob/HEAD/14-workflows-and-best-practices.md
3. **Review gate** — read the diff and the evidence (test output, screenshots, preview link), not the agent's summary.
4. **Deploy gate** — explicit go-ahead, every time (see §5).

Between gates, the agent works uninterrupted. The bottleneck is removed by making verification **machine-checkable**: give the agent a check it can run (tests, build, lint, screenshot comparison, script diffing output against a fixture). Anthropic calls this the highest-leverage practice: without a check the human *is* the verification loop and every mistake waits for the human to notice it. Source: Anthropic, verified live, https://www.anthropic.com/engineering/claude-code-best-practices

Course-correct early: stop the agent the moment it goes off track. Anthropic's rule of thumb: if you have corrected the agent twice on the same issue in one session, the context is polluted — clear it and restart with a better prompt that incorporates what you learned. A clean session with a better prompt almost always beats a long session full of corrections. Source: Anthropic, verified live, https://www.anthropic.com/engineering/claude-code-best-practices

**Deterministic guardrails beat advisory rules.** Anthropic notes that CLAUDE.md instructions are advisory, while hooks are deterministic and guarantee an action happens (e.g. run lint after every edit, block writes to a migrations folder). Use hooks/scripts for things that must always happen; use prose for judgment. Source: Anthropic, verified live, https://www.anthropic.com/engineering/claude-code-best-practices

---

## 2. How the human should brief the agent

A good brief is a **self-contained work order a fresh agent can execute without asking the human anything**, plus a clear stop rule. Anthropic's spec advice and briefing literature converge on the same fields.

### The brief template (use for every pass)

| Field | What to write | Why |
|---|---|---|
| **Goal** | One sentence: the user-visible outcome. | Prevents the agent optimising the wrong thing. |
| **Where** | Exact files, routes, components, or "find them in X and name them before coding". Point to an existing pattern to copy. | Anthropic: reference specific files and existing patterns; vague prompts cannot be executed reliably. |
| **Context** | Who the user is, what the feature is for, what already exists, links to docs/API references, real data sources. | The agent cannot infer product judgment. |
| **Constraints** | Stack, libraries already in use, no new dependencies unless approved, follow tokens/components, performance or accessibility limits. | Constraints make the agent more useful, not less. |
| **Non-goals / out of scope** | Explicit list: what must NOT change, what is deferred. | The single most effective anti-scope-drift device. |
| **Definition of done** | Observable checks: test cases with inputs → expected outputs, edge cases, what the user sees on the live/preview site. Include a number or a date where possible. | If done cannot be checked from outside, the agent cannot know it is done either. |
| **Verify by** | Exact commands / steps: tests to run, routes to hit on preview, screenshots to compare. | Gives the agent a check it can run; see §1.4. |
| **Assumptions** | Require the agent to list assumptions in plain language and mark anything unverified as unverified. | Surfaces invented facts before they become code. |
| **Stop / escalate** | "If uncertain whether something fits the boundaries, or if you hit X (money, auth, deleting data, a conflicting decision), stop and bring it to me. Do not guess." | Agents fail worst in the gaps between rules; give them an explicit place to stand. |
| **Do not** | Never invent data, libraries, APIs, prices, schedules, or citations. If a value is not in the codebase, a doc, or a live feed, say so and stop. Never edit/remove tests to make them pass. Never deploy without explicit go-ahead. | Directly targets hallucination and silent cheating. |

Sources for this structure: Anthropic on specific context and verification criteria, verified live, https://www.anthropic.com/engineering/claude-code-best-practices ; the "definition of done + boundaries + escalation rule" framing, index, https://dev.to/tm_gunderson_9cff63a7ba/objectives-not-instructions-writing-work-an-ai-agent-can-actually-own-337e ; the Goal → Where → Rules → Done-when briefing order, index, https://dev.to/fzsheik/voice-prompting-for-cursor-and-claude-code-how-to-talk-to-your-ai-coding-agent-and-why-typed-124 ; the specific-task + relevant-context + clear-done-criteria test, index, https://www.lowcode.agency/blog/claude-code-prompting-guide

### Briefing habits that matter most for this builder

- **Brief like onboarding a new engineer.** Anthropic's context-engineering guidance treats context, not phrasing, as the work: give the agent the requirements, business constraints, existing code and tests, schema/API specs, conventions, prior decisions and why, and examples of good output. Source: Anthropic, verified live, https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- **Separate drafting from approval.** First pass: "explore and propose a plan, do not write code." Second pass, after human approval: implement. This is the plan-mode workflow in §1.1.
- **Keep the standing instructions short and pruned.** Put project-wide rules in one short `AGENTS.md` / `CLAUDE.md` (commands the agent cannot guess, style rules that differ from defaults, test instructions, repo etiquette, architecture decisions, gotchas). For each line ask: would removing this cause the agent to make mistakes? If not, cut it. Bloated instruction files get ignored. Move sometimes-relevant knowledge into on-demand skills/docs instead. Source: Anthropic, verified live, https://www.anthropic.com/engineering/claude-code-best-practices
- **Plain language over jargon — in both directions.** Require the agent to explain decisions, risks and verification results in plain language, and to define any term it introduces. This is not cosmetic: as a systems/business analyst, the builder's edge is judging whether the behaviour is right for the user and the business. Jargon hides wrong behaviour. Plain-language explanations are also the review surface: if the agent cannot explain a change plainly, it is not ready to ship.
- **One task per brief.** Multi-task prompts produce multi-failure output. Split first, then brief each piece.

---

## 3. Review and quality gates that matter most

Ordered by leverage — highest first. A gate the agent can run itself is worth more than a gate that depends on the human remembering to check.

### Gate 1 — The agent verifies against the brief (machine-checkable)
- Tests, typecheck, lint, build all green on the pass's branch.
- The verification step named in the brief actually run, with output shown as evidence (test output, command + result, screenshot), not an assertion of success. Anthropic explicitly advises: have the agent show evidence rather than asserting success. Source: Anthropic, verified live, https://www.anthropic.com/engineering/claude-code-best-practices
- New behaviour has a regression test that fails before the fix/feature and passes after. Do not let the agent edit tests to make them pass.

### Gate 2 — Fresh-context review (agent reviewer, not the author)
- A reviewer in a fresh context sees only the diff and the criteria, not the reasoning that produced the change, so it evaluates the result on its own terms. Anthropic recommends a Writer/Reviewer pattern and an adversarial review step, including a dedicated code-review skill that reviews the diff in a fresh subagent. Source: Anthropic, verified live, https://www.anthropic.com/engineering/claude-code-best-practices
- Review prompt should name: the work to check, the plan/spec to check it against, and what counts as a finding. The implementing session then fixes findings and re-reviews without the human copying findings between windows.
- **Limitation:** an AI reviewer is not a substitute for human review on high-stakes code (see Gate 4). Use it to catch the obvious and the mechanical so human review time goes to judgment.

### Gate 3 — Human diff review (the builder)
- Read the diff, not the summary. Check: does it do only what the brief asked? Does it touch files outside scope? Does it change behaviour the brief did not mention? Are there invented values, hardcoded secrets, new dependencies, or changes to auth/payment/data code?
- Read the plain-language explanation: assumptions, what was verified, what was *not* verified, remaining risks.
- Keep passes small enough that this review takes minutes, not an hour. If review takes an hour, the pass was too big.

### Gate 4 — Security review (human, mandatory for sensitive paths)
Treat AI-generated code as untrusted by default and give the agent least privilege. Practitioner checklists converge on:
- Automated scanning as **required** CI checks, not advisory: SAST (e.g. Semgrep/CodeQL), dependency/SCA scanning, secret scanning on commits and history. Source: index, https://github.com/adnankaya/software-developer-project-checklist
- **Mandatory human review** for anything touching authentication, authorisation, payments/billing, cryptography, infrastructure/deployment config, API access control, or PII/sensitive data — regardless of how confident the agent sounds. Mark those paths in the agent instructions so the agent flags them. Source: index, https://github.com/devllmops/devllmops/blob/HEAD/docs/security.md ; also https://www.growexx.com/blog/ai-generated-code-owasp-top-10/
- Verify every AI-suggested dependency against the official registry before installing (watch for hallucinated / lookalike packages), pin versions, keep an SBOM. Source: index, https://github.com/adnankaya/software-developer-project-checklist
- Never trust the client: re-enforce every check (auth, authorisation, validation, price, role) on the server. Secrets never reach the browser or git. Source: index, https://github.com/lxveace/vibe-coding-website-security
- Human approval required for any agent action with irreversible consequences (deploys, deletions, money, config changes). This matches OWASP's human-in-the-loop guidance for agentic tools, as summarised in the sources above.

### Gate 5 — Verify against the live deployed app, not status messages
This is the builder's proven habit and it is the correct one. Anthropic's harness research found the agent's characteristic failure was marking a feature complete after unit tests or `curl` against a dev server, without recognising it did not work end-to-end — and that explicitly prompting browser-automation testing "as a human user would" dramatically improved performance, catching bugs invisible from code alone. Source: Anthropic, verified live, https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents

Concretely:
- **Preview first:** every change gets a preview deployment; prove the preview (hit real routes, check logs, click through the real user flow) before it goes near production. Promote the *same* deployment to production rather than rebuilding, so the released bytes equal the tested bytes. Source: index, https://github.com/skillmedev/skills/blob/HEAD/skills/vercel-deploy-pipeline/SKILL.md
- **Post-deploy:** open the production URL, sign in, run the core flow with real data, check error logs/monitoring. A deploy is not done until health checks are green and the new version is confirmed serving real traffic.
- **Never accept** "done", "deployed", "tests pass" as proof. Require: URL + what was clicked/checked + observed result + log/test output. If the agent cannot produce that evidence, the pass is not done.
- Add uptime monitoring with alerts and error tracking (e.g. Sentry-style) — not just "is it up" but "why did it break and for whom". Source: index, https://github.com/adnankaya/software-developer-project-checklist

### Gate 6 — Docs kept current as part of done
A pass is not done until: the spec/plan status is updated, the decision record (if any) is written, user-facing docs / README / changelog are updated, and the progress log is updated. See §5. If docs are a separate later task, they will never happen.

### Suggested CI minimum (runs on every PR)
Typecheck + lint + unit/integration tests + build + SAST + dependency scan + secret scan. Preview deployment per PR. Production deploy only on manual approval. Rollback path tested, not theoretical; database migrations reversible/backward-compatible; backups with a tested restore. Source: index, https://github.com/adnankaya/software-developer-project-checklist ; deploy pipeline pattern, index, https://github.com/sivarman/archmind-ai/blob/HEAD/docs/Phase-02/09_INFRASTRUCTURE_ARCHITECTURE.md

---

## 4. Common failure modes and how to prevent them

| Failure mode | How it shows up | Prevention (gate / habit) |
|---|---|---|
| **Scope drift / scope creep** | "Just one more thing", agent adds features, refactors unrelated files, expands the brief. | Non-goals in every brief; one task per pass; human diff review checks scope; plan gate before code. Spec-driven framing: scope defined upfront, in and out. |
| **UI churn / visual inconsistency** | Every screen gets a fresh look, new colours/spacing, rebuilt buttons; app looks "AI-made". | Design tokens + a small component kit as the single visual vocabulary; rule "tokens, never raw values"; briefs say "reuse existing components, do not restyle". Screenshot comparison in verification. See below. |
| **Invented / placeholder data** | Fake schedules, prices, names, lorem ipsum, hardcoded demo values, hallucinated library functions or packages. | "Never invent" rule in every brief + standing instructions; require real data source named per value; grep for TODO / lorem / hardcoded sample data before merge; verify unfamiliar packages in registry; ground values in live feeds/APIs and check them on the live site. |
| **Silent regressions** | Old behaviour breaks quietly; agent "fixes" by weakening a test. | Regression tests for core flows (routing, payments, auth, live feeds); CI green required; never edit/remove tests to pass; verify on preview + live; error tracking in production. |
| **Tech debt / mess accumulation** | Duplicated code, dead code, inconsistent patterns, quick hacks compound until the next feature is slow. | Small passes; "leave it clean/mergeable" as an explicit done criterion; periodic dedicated refactoring passes with their own acceptance criteria (no behaviour change + tests green); prune CLAUDE.md and docs as you go. |
| **Context loss across sessions** | New session re-litigates settled decisions, forgets constraints, re-does work, contradicts earlier choices. | All durable state in files + git (see §5); fresh session starts by reading spec, decisions, progress log, git log; never rely on conversation memory. |
| **Re-litigating settled decisions** | Agent (or human) reopens a decided question because the reasoning was never written down. | Decision record written the session the decision lands (see §5); new ADR supersedes old one, never edits it; briefs point at the decision record. |
| **Context poisoning** | One early error (wrong library version, wrong assumption about state) gets embedded in summaries/goals and compounds across turns. | Fresh sessions per pass; verify early; after 2 failed corrections, clear and restart with a better prompt; do not let a long session accumulate corrections. Anthropic on compaction: preserve decisions/bugs, discard redundant outputs; on context rot: performance degrades as context fills. |
| **Trust-then-verify gap** | Plausible-looking implementation that misses edge cases. | Anthropic's named failure pattern; fix is always provide verification (tests, scripts, screenshots) — "if you can't verify it, don't ship it." |
| **Kitchen-sink session** | One session drifts across unrelated tasks; context fills with irrelevant material. | One task per session; `/clear` / fresh session between tasks. |
| **Premature "done"** | Agent declares victory when progress exists, without end-to-end proof. | Feature list with explicit pass/fail; only mark passing after end-to-end verification; require evidence, not assertions. |
| **Agent grading its own work** | Author reviews own diff and finds nothing. | Fresh-context reviewer (Gate 2) + human review; reviewer checks against the plan, not the author's explanation. |

Sources: Anthropic failure-pattern lists, verified live, https://www.anthropic.com/engineering/claude-code-best-practices and https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents ; context poisoning, index, https://www.splunk.com/en_us/blog/artificial-intelligence/context-engineering-production-agents.html ; no-invention prompting, index, https://www.webpronews.com/the-prompt-engineering-fix-that-stops-ai-from-making-things-up/

### On UI churn specifically
The mechanism is a missing shared vocabulary: without one, the agent invents a fresh look per screen. The fix is a tokenised design system — every visual decision (colour, spacing, type) becomes a named token in one source of truth, components are built from tokens, and the agent assembles from the kit instead of designing each screen from scratch. Enforce "tokens, never raw values" with a check (lint/grep for hardcoded colours/spacing), and require screenshot comparison for UI passes. Sources: index, https://www.youtube.com/watch?v=a5UsOh8z7hM (practitioner explanation of the token mechanism); AI-ready design systems encode decisions as tokens/semantics/rules so AI generates consistent UI, index, https://markets.financialcontent.com/sandiego/article/abnewswire-2025-12-22-wavepace-introduces-ai-ready-design-systems-to-power-scalable-digital-products

### On invented data specifically
Treat "no invention" as a system rule, not a hope: (a) standing instruction + per-brief restatement; (b) every factual value in a pass must cite its source (file, doc, live API/feed, or "human to provide"); (c) a pre-merge grep for placeholder markers (`TODO`, `FIXME`, `lorem`, `sample`, `example.com`, hardcoded demo arrays) in production paths; (d) live-site verification with real data. The "no invention" prompt pattern — never invent URLs, citations, libraries or functions; if no source exists, say so — is reported to substantially reduce fabrication, though not to zero, which is why the grep + live check still matter. Source: index, https://www.webpronews.com/the-prompt-engineering-fix-that-stops-ai-from-making-things-up/

---

## 5. Keeping long-lived projects healthy

### 5.1 Decision logs (ADRs)
- **One decision per file**, in `docs/decisions/` (or `docs/adr/`), numbered sequentially: Context → Decision → Consequences + Alternatives Considered + **Why Not** (what was rejected and why). Source: index, https://github.com/mingblue/adr-log/blob/HEAD/SKILL.md
- Record only decisions that are hard to reverse or easy to break without realising it (framework/library choices, data model, auth/payments approach, routing engine approach, naming/config conventions). Source: index, https://github.com/erovelli/outdegree/blob/HEAD/docs/adr/0001-record-architecture-decisions.md
- **Immutable:** never edit an accepted ADR's decision. A reversal is a new ADR that supersedes the old one, with a link. This is what stops re-litigation: the next session reads the ADR and the rejected alternatives, instead of reopening the debate.
- **Write it the session the decision lands.** Retroactive ADRs lose context. Make "ADR written (if the pass decided anything)" part of done.

### 5.2 Memory / context across sessions
Anthropic's long-horizon techniques are: compaction, structured note-taking, and sub-agent architectures. For a solo builder, the practical implementation is files + git, not model memory:
- **Progress file** (`PROGRESS.md` or `claude-progress.txt`): what was done, what is in progress, what is blocked, updated at the end of every session. Anthropic's harness used exactly this plus git history so a fresh agent could get up to speed without guessing. Source: Anthropic, verified live, https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents
- **Feature list** with pass/fail status per feature, updated only by changing the status field — never by deleting or editing the requirement.
- **Session start ritual:** read progress file + feature list + recent git log + relevant ADRs + spec for the current feature, then run a basic end-to-end smoke check on the dev server before writing new code (catches undocumented breakage before it compounds).
- **Session end ritual:** commit with a descriptive message, update progress file, update docs, write ADR if needed, state what is verified and what is not. Never end a session with a half-implemented feature and no note — that is the failure mode Anthropic documents.
- **Compaction is lossy.** Treat anything that only exists in the conversation as already lost. If it matters, it is in a file before the session ends. Anthropic notes compaction preserves decisions and bugs while discarding redundant outputs, but also that compaction does not always pass perfectly clear instructions to the next agent — hence files + git as the real memory. Sources: Anthropic, verified live, https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents and https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents

### 5.3 Refactoring discipline
- Separate refactoring passes from feature passes. A refactoring pass has one acceptance criterion: **no behaviour change** (same tests, same user-visible behaviour, plus cleaner structure). Never mix a refactor into a feature pass — it makes the diff unreviewable and hides regressions.
- Small, continuous refactoring beats big rewrites. When a pass reveals duplication or a wrong abstraction, log it as a debt item (ranked, like an audit finding) and schedule it as its own small pass — do not let the agent "tidy up" unrelated code inside a feature pass.
- Keep the standing instruction file pruned. If the agent already does something correctly without a rule, delete the rule.

### 5.4 Keeping docs in sync
- Docs are part of done (Gate 6, §3), not a separate phase.
- One canonical place per fact: spec = what/why, ADRs = decisions, README/runbook = how to run/deploy, code comments = why this line is surprising. If the same fact lives in three places, it will rot in two of them. Link instead of duplicating.
- When a spec changes, update the spec first, then the plan, then the code. Never retrofit a spec to match shipped code — that defeats the purpose of the spec. Source: index, https://github.com/glowelephant/context-forge/blob/HEAD/catalog/spec-driven/github-spec-kit.md (Spec Kit notes)

### 5.5 Deployment discipline with explicit human go-ahead
- **No deploy without the human saying "deploy" for that specific change.** The agent may prepare everything (branch, CI green, preview verified, release notes, rollback plan) but must stop at the gate.
- Pipeline: merge to main → staging/preview auto-deploy → smoke test on staging → production only on manual approval. Rollback = promote/redeploy the previous known-good deployment; migrations must be backward-compatible. Source: index, https://github.com/sivarman/archmind-ai/blob/HEAD/docs/Phase-02/09_INFRASTRUCTURE_ARCHITECTURE.md
- Every deploy gets: what changed (one plain-language paragraph), what was verified on preview, what to check on live after deploy, and how to roll back. The human checks live after deploy and confirms, or rolls back.
- Never deploy with failing CI, with a known failing test, with secrets in the diff, or with a migration that has not been applied/rehearsed on staging.

---

## 6. When parallel work / multiple agents help vs hurt

### The default for this builder: one agent
One agent, one task at a time is the default — it matches the builder's setup (one main engineering partner), keeps review load manageable, and avoids coordination failures. Anthropic's own caution: most coding tasks involve fewer truly parallelizable tasks than research, and agents are not yet great at coordinating and delegating in real time. Source: Anthropic, verified live, https://www.anthropic.com/engineering/multi-agent-research-system

### When parallel / multi-agent helps
- **Breadth-first, decomposable work:** independent research (e.g. compare three transit APIs, survey three payment providers), independent audits, or exploring several approaches in parallel. Anthropic's research system is the exemplar: a lead decomposes, subagents explore in parallel in isolated contexts and return condensed findings, the lead synthesises — 90.2% better than a single agent on their internal research eval for breadth-first queries. Source: Anthropic, verified live, https://www.anthropic.com/engineering/multi-agent-research-system
- **Writer + reviewer:** one agent implements, a fresh-context agent reviews. This is the smallest useful multi-agent pattern and is recommended by Anthropic. Source: Anthropic, verified live, https://www.anthropic.com/engineering/claude-code-best-practices
- **Independent, non-overlapping code tasks** on separate branches/worktrees, where each task has its own spec, acceptance criteria, and verification — and the human can absorb the review load.
- **Specialisation / safety separation:** a read-only reviewer or security reviewer with narrower tools, so the reviewer cannot accidentally modify code.

### When it hurts
- **Sequential, dependency-heavy work:** tasks where B depends on A's design decisions. A controlled-study summary reports centralised multi-agent coordination improving parallelisable analysis by ~80.9% but **degrading sequential planning/reasoning by 39–70%**, with independent (uncoordinated) agents amplifying errors far more than centralised ones. Source: index (secondary summary of Google Research study), https://github.com/zscole/ai-poc-daily/blob/HEAD/2026-02-02/README.md
- **Tightly coupled code changes:** two agents editing the same files, shared components, data model, or UI kit will conflict, duplicate, or silently diverge. "If subtasks are tightly coupled, splitting them usually makes things worse." Source: index, https://github.com/tanker327/research-assistant/blob/HEAD/agent-code-review/original/04-ai-coding-agents-practical-guide.md
- **When review is the bottleneck:** parallel agents produce parallel diffs. If the human cannot review them, work piles up unverified — worse than sequential, because unverified parallel work can conflict at merge.
- **Cost:** multi-agent systems use roughly 15× the tokens of a chat interaction (vs ~4× for a single agent), per Anthropic. Only parallelise when the value justifies it. Source: Anthropic, verified live, https://www.anthropic.com/engineering/multi-agent-research-system
- **General rule:** multi-agent is an architecture trade-off, not an automatic upgrade. Start with one agent; add more only when a specific bottleneck appears. Source: index, https://github.com/bhaskarjha-com/genai-notes/blob/HEAD/agents/multi-agent-architectures.md ; and the orchestration review: outside heavily parallelisable, high-value work, multi-agent often adds cost and coordination failures without beating a well-run single agent. Source: index, https://github.com/overbrilliant/ob-1/blob/HEAD/docs/research/multi-agent-orchestration.md

### How to coordinate parallel work when you do use it
1. **One orchestrator (the human).** The human decomposes, assigns, and merges. Agents do not coordinate with each other directly.
2. **Isolation:** one git worktree + one branch per agent/task, so edits never collide on disk; conflicts surface only at merge, where git handles them. Each agent gets its own worktree, branch, and (for dev servers) its own port/test data. Sources: index, https://github.com/laurence-nz/superset-pwsh/blob/HEAD/apps/marketing/content/category/parallel-coding-agents.mdx ; https://github.com/namlee1998/claudekit/blob/HEAD/skills/using-git-worktrees/SKILL.md ; two agents in one checkout will corrupt shared state — give each its own working tree. Source: index, https://sharkly.ai/blog/parallel-agents-isolated-worktrees/
3. **Written contracts per task:** each agent gets the same template as §2 (goal, scope, non-goals, done criteria, verify-by, stop rule) plus its branch/worktree and the files it may touch. Without detailed task descriptions, agents duplicate work, leave gaps, or fail. Source: Anthropic, verified live, https://www.anthropic.com/engineering/multi-agent-research-system
4. **No shared files:** do not assign two agents the same file, the design-token file, the data model, or the migration in the same round. Shared files go through one agent, sequentially.
5. **Merge one at a time, then re-verify the combination:** review and test the combined branch after integration, even when git merges it without a conflict — a clean merge does not mean the combined behaviour is correct. Source: index, https://github.com/laurence-nz/superset-pwsh/blob/HEAD/apps/marketing/content/category/parallel-coding-agents.mdx
6. **Cap concurrency at what you can review:** for this builder, a practical cap is **1 builder + 1 reviewer** as the standing pattern, and at most 2–3 parallel builders for a short, well-decomposed burst — then stop and review before starting more. Scale effort to complexity; do not fan out a fixed number of agents on every task.
7. **Clean up:** remove worktrees and delete merged branches after each round, so stale work does not get re-read as current state.

---

## 7. The recommended workflow — step by step

This is the **one** workflow to follow on the next project. It folds the builder's proven habits (audit-first with ranked findings, small reviewable passes, explicit go-ahead before any deploy, locking decisions in writing the session they land, verifying against the live site rather than status replies, documentation as part of done, plain language over jargon) into the spec-driven loop the sources support. Each step names its source so the reasoning is checkable.

### Project setup (once, before any feature)

**Step 0 — Set up the project spine (one session).**
Create the repo with: `AGENTS.md` (short standing instructions), `docs/specs/`, `docs/decisions/`, `docs/runbook.md`, `PROGRESS.md`, `FEATURES.md` (feature list, all initially failing), a CI pipeline (typecheck, lint, tests, build, SAST, dependency scan, secret scan), a preview deployment per PR, and a design-token file + component kit if there is any UI. Write a one-page project constitution: what the app is for, who it serves, stack, non-goals, and the standing rules (never invent data, never deploy without go-ahead, docs as part of done). *Reasoning: Spec Kit's "constitution once per project" + Anthropic's initializer-agent pattern (environment, progress file, feature list, init script) + the solo security checklist's CI/preview/rollback minimum.*

**Step 0b — Brief template + gates in `AGENTS.md`.** Paste the §2 brief template and the §3 gate list into `AGENTS.md` so every session inherits them. Keep `AGENTS.md` short; prune anything the agent already does without being told. *Reasoning: Anthropic CLAUDE.md hygiene — per-line "would removing this cause mistakes?" test.*

### Per feature (repeat)

**Step 1 — Audit first (when the feature touches existing code).**
Before writing the spec, run a short audit pass: "Read the relevant code and the live behaviour. Report ranked findings: what is broken, risky, or inconsistent, with evidence (file + line or live URL + observed behaviour), severity, and suggested fix. Do not change code yet." Rank by user/business impact, not by ease. *Reasoning: the builder's proven audit-first habit; Anthropic's explore-before-plan phase; audit findings become the backlog for the feature and for later refactoring passes.*

**Step 2 — Brief + spec (human + agent, no code yet).**
Human writes a one-paragraph brief (goal, user, why now). Agent interviews the human on edge cases, constraints, and trade-offs, then drafts a one-page spec in `docs/specs/<feature>.md`: goal, user story, scope, non-goals, data sources (real ones, named), acceptance criteria (observable), verification step, and open questions. Human approves the spec, or sends it back. *Reasoning: Anthropic — have the agent interview you and write a spec; the most useful specs are self-contained and end with an end-to-end verification step; Spec Kit specify phase.*

**Step 3 — Plan (agent proposes, human approves).**
Agent drafts a plan: files to touch, data model/API changes, test plan, risks, and the order of small passes. Human reviews the plan critically and approves it. If the plan deviates from the spec, fix the spec first. *Reasoning: Anthropic plan mode — correcting a plan costs one message; Spec Kit plan phase; Spec Kit note that the plan stage is the critical correction point.*

**Step 4 — Break into small passes.**
Split the plan into passes, each one user-visible behaviour or one bug, each with its own acceptance criteria. Write them into `FEATURES.md` as failing. Order by dependency and risk (riskiest/unknown first, so surprises arrive early). *Reasoning: Anthropic one-feature-at-a-time; Ralph one-task-per-iteration; small passes keep human review feasible.*

**Step 5 — Implement one pass (fresh session, own branch).**
Start a fresh session. The session reads: `AGENTS.md`, the spec, the relevant ADRs, `PROGRESS.md`, and the pass's acceptance criteria. It implements **only** that pass on its own branch, writes/updates tests first where the behaviour is testable, runs typecheck/lint/tests/build, and verifies end-to-end (browser automation as a human user where the pass is user-visible, or real API calls against a dev/preview server for backend passes). It ends by committing with a descriptive message and updating `PROGRESS.md`. *Reasoning: Anthropic fresh-session-per-task + get-up-to-speed ritual (read progress, git log, run smoke test before new code) + browser-automation verification; fresh context prevents context poisoning and drift.*

**Step 6 — Fresh-context review.**
A reviewer in a fresh context (agent reviewer) checks the diff against the spec + plan + acceptance criteria and returns ranked findings. The builder reads the diff + evidence + reviewer findings, checks scope (nothing extra, nothing invented, no touched auth/payment files without flagging), and either approves or sends back specific fixes. Fixes are their own small pass, not a long correction thread — after two failed corrections on the same issue, clear context and restart with a better brief. *Reasoning: Anthropic Writer/Reviewer + adversarial review; after-two-corrections rule.*

**Step 7 — Security gate (when the pass touches sensitive paths).**
If the pass touches auth, payments, crypto, infra, access control, or PII: human security review is mandatory, plus CI scans green. Verify new dependencies in the registry. Re-check server-side enforcement for any client-side check. *Reasoning: §3 Gate 4 sources.*

**Step 8 — Preview verification (human + agent).**
Deploy the branch to preview. The agent verifies on preview (real routes, real data, logs clean). The human clicks through the real user flow on the preview URL and confirms the acceptance criteria in plain language. Only a preview that passes gets near production. *Reasoning: preview-first / promote-the-same-deployment pattern; Anthropic "show evidence, not assertions".*

**Step 9 — Deploy on explicit go-ahead only.**
Human says "deploy" for that specific change. Agent deploys (or the human triggers the deploy), then the human verifies on the **live site**: open the production URL, sign in, run the core flow, check logs/monitoring. The agent reports URL + what was checked + observed result. If anything is wrong, roll back to the previous deployment first, diagnose second. *Reasoning: the builder's proven explicit-go-ahead + live-verification habits; deploy pipeline with manual approval and tested rollback.*

**Step 10 — Lock decisions + close the loop (same session).**
If the pass decided anything (library, pattern, data rule, UX rule), write the ADR **now** — Context, Decision, Consequences, Alternatives + Why Not. Update the spec status, `FEATURES.md` (flip to passing only with evidence), `PROGRESS.md`, README/runbook/changelog, and the design-token/component docs if UI changed. The session ends with a short plain-language summary: what changed, what was verified (with evidence), what was not verified, what is next. *Reasoning: ADRs immutable + written at decision time; docs as part of done; Anthropic session-end ritual (commit + progress update + clean state).*

### Ongoing (weekly / between features)

**Step 11 — Weekly health pass (one short session).**
Run the audit habit on the whole app: ranked findings (bugs, regressions, debt, doc drift, UI inconsistencies, security scan results). Pick the top 1–3, turn them into small passes (Steps 2–10). Schedule one refactoring pass per cycle with a no-behaviour-change criterion. Prune `AGENTS.md` and delete stale docs. Check monitoring/alerts and backup restore still work. *Reasoning: solo-workflow principle that shipping is the unit of progress and that unfinished work moves forward rather than blocking; continuous small refactoring beats big rewrites; docs rot unless pruned.*

**Step 12 — Parallel work only when earned.**
Default stays one builder + one reviewer. Add a second parallel builder only when: (a) tasks are independent, non-overlapping, each with its own spec and worktree/branch; (b) the human has review capacity for both diffs; (c) the work is breadth-first or genuinely parallelisable. Merge one at a time and re-verify the combination. Never parallelise a sequential design decision, a shared data model, or the UI kit. *Reasoning: §6.*

### What to do when it goes wrong (quick reference)

- Agent drifting / repeating a mistake → stop it, `/clear`, restart with a better brief that states what was learned. Do not argue with a polluted context.
- Agent claims done but evidence is thin → send back with "show me: preview URL, what you clicked, what you saw, test output". Do not accept status messages.
- A pass grows too big → split it; land the first half, re-plan the rest.
- A decision is being re-argued → point at the ADR; if the ADR is wrong, write a superseding ADR with reasons, do not edit the old one.
- Production breaks → roll back first, then diagnose; write the incident + fix as a pass with a regression test.
- Docs and code disagree → docs are wrong or code is wrong; decide which, fix one, record it. Never leave them disagreeing.

---

## Could not verify / open questions

- **Quantified claims from secondary summaries** (e.g. the Google 180-configuration multi-agent study's +80.9% / −39–70% figures, and the 25–45% vulnerability rates sometimes cited for AI-generated code) were found only in secondary summaries in this research pass, not re-verified against the primary papers. They are directionally consistent with Anthropic's own primary cautions (multi-agent cost, coding-task fit) but should not be quoted as established facts without checking the primary source.
- **Tool-specific mechanics** (exact slash commands, hooks, `/goal`, worktree flags) change fast and are version-dependent; the Anthropic pages cited are dated 2025–2026 and were read 2026-10-16, but the builder should confirm the current syntax in the tool they actually use.
- **No primary source was found that prescribes this exact end-to-end workflow as a package.** The §7 workflow is a synthesis of the cited sources plus the builder's stated habits, not a single vendor's prescribed method. That is intentional — the sources agree on the components, not on one brand-name process.
- **The mission did not specify the builder's current tool** (Claude Code vs Copilot vs Cursor vs other), hosting, or stack, so tool-specific setup steps are deliberately left generic.

## Sources

Primary — verified live 2026-10-16:
- Anthropic, Claude Code best practices — https://www.anthropic.com/engineering/claude-code-best-practices
- Anthropic, How we built our multi-agent research system — https://www.anthropic.com/engineering/multi-agent-research-system
- Anthropic, Effective context engineering for AI agents — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- Anthropic, Effective harnesses for long-running agents — https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents
- GitHub, Spec Kit — https://github.com/github/spec-kit

Practitioner / secondary — via search index, 2026-10-16:
- Ralph Wiggum loop — https://github.com/chrisbrooksbank/cb-ralph-wiggum/blob/HEAD/README.md ; do-not-use list — https://github.com/tangledgroup/tangled-skills/blob/HEAD/misc/agent-ralph-wiggum-snarktank/SKILL.md
- Spec-driven vs vibe coding — https://github.com/orwanec/sdd-meetup-demo ; Spec Kit notes — https://github.com/glowelephant/context-forge/blob/HEAD/catalog/spec-driven/github-spec-kit.md
- Solo developer checklist (CI, security, deploy safety) — https://github.com/adnankaya/software-developer-project-checklist
- Solo developer workflow — https://github.com/sungurerdim/dev-skills/blob/HEAD/docs/methodology/solo-dev-workflow.md
- Explore-plan-code-verify + TDD with agents — https://github.com/iam-veeramalla/claude-code-crash-course/blob/HEAD/14-workflows-and-best-practices.md
- Briefing: definition of done + boundaries + escalation — https://dev.to/tm_gunderson_9cff63a7ba/objectives-not-instructions-writing-work-an-ai-agent-can-actually-own-337e ; Goal→Where→Rules→Done — https://dev.to/fzsheik/voice-prompting-for-cursor-and-claude-code-how-to-talk-to-your-ai-coding-agent-and-why-typed-124 ; task+context+done — https://www.lowcode.agency/blog/claude-code-prompting-guide
- Security review workflow — https://github.com/devllmops/devllmops/blob/HEAD/docs/security.md ; OWASP-aligned mitigation — https://www.growexx.com/blog/ai-generated-code-owasp-top-10/ ; vibe-coding website security principles — https://github.com/lxveace/vibe-coding-website-security
- ADRs — https://github.com/mingblue/adr-log/blob/HEAD/SKILL.md ; https://github.com/erovelli/outdegree/blob/HEAD/docs/adr/0001-record-architecture-decisions.md
- Multi-agent trade-offs — https://github.com/zscole/ai-poc-daily/blob/HEAD/2026-02-02/README.md ; https://github.com/bhaskarjha-com/genai-notes/blob/HEAD/agents/multi-agent-architectures.md ; https://github.com/overbrilliant/ob-1/blob/HEAD/docs/research/multi-agent-orchestration.md ; tightly-coupled splitting — https://github.com/tanker327/research-assistant/blob/HEAD/agent-code-review/original/04-ai-coding-agents-practical-guide.md
- Worktrees / parallel agents — https://github.com/laurence-nz/superset-pwsh/blob/HEAD/apps/marketing/content/category/parallel-coding-agents.mdx ; https://github.com/namlee1998/claudekit/blob/HEAD/skills/using-git-worktrees/SKILL.md ; https://sharkly.ai/blog/parallel-agents-isolated-worktrees/
- Preview → promote deploy pipeline — https://github.com/skillmedev/skills/blob/HEAD/skills/vercel-deploy-pipeline/SKILL.md ; staging → manual approval — https://github.com/sivarman/archmind-ai/blob/HEAD/docs/Phase-02/09_INFRASTRUCTURE_ARCHITECTURE.md
- Context poisoning — https://www.splunk.com/en_us/blog/artificial-intelligence/context-engineering-production-agents.html
- No-invention prompting — https://www.webpronews.com/the-prompt-engineering-fix-that-stops-ai-from-making-things-up/
- Design tokens / UI consistency — https://www.youtube.com/watch?v=a5UsOh8z7hM ; https://markets.financialcontent.com/sandiego/article/abnewswire-2025-12-22-wavepace-introduces-ai-ready-design-systems-to-power-scalable-digital-products

Working notes: notes/sources.md, notes/plan.md
