---
name: lifecycle-driven-delivery
description: Takes a feature, product, or significant change from idea to shipped, verified, maintainable code by moving through product-lifecycle stages (discover, define, design, build, verify, launch, evolve, sunset), applying the right persona's lens at each stage and passing a quality gate before moving on. Use this whenever the user asks to build a new feature, app, service, or tool; asks for a spec, PRD, design doc, technical design, or implementation plan; says things like "plan this out", "design and build", "take this end to end", or "v2 of X"; or when a request is vague enough that jumping straight to code risks building the wrong thing. Also use for deprecations, migrations, and sunsetting features. Skip it for one-line fixes, pure questions, or when the user explicitly wants only code with no planning.
---

# Lifecycle-Driven Delivery

Most weak agent output comes from skipped stages: code written before the problem is understood, a design that ignores the existing system, "done" declared without running anything. Products that succeed move through a lifecycle in order, and at each stage a different expert asks different questions. This skill borrows that structure:

- **Stages** give the order of work.
- **Personas** give the questions to ask at each stage. They are lenses you apply, not characters you narrate.
- **Gates** stop errors from compounding. A wrong requirement caught at Define costs a sentence; caught after Build it costs a rewrite.

The aim is better outcomes, not more paperwork. Scale everything below to the size of the work.

## Step 1: Pick a track

| Track | Use when | Stages | Written artifacts |
|---|---|---|---|
| **Quick** | Small, well-understood change in a clear area (bug fix, small enhancement) | Define-lite → Build → Verify | None. Restate the goal and 2–4 acceptance criteria in your reply. |
| **Standard** | A new feature or meaningful change in an existing product | Discover → Define → Design → Build → Verify → Launch | `spec.md`, `design.md` (both short) |
| **Full** | New product or service, cross-cutting change, anything hard to reverse (schema, public API, auth, billing, data deletion), or a migration | All stages, with human checkpoints at Define and Design | `spec.md`, `design.md`, `launch.md` |

When unsure, take the bigger track for the *thinking* and the smaller one for the *paperwork*. Tell the user which track you chose in one line so they can redirect.

## Step 2: Locate the product in its lifecycle

The stage of the *product* (not the feature) changes what "good" means. Infer it from signals: age and activity of the repo, versioning, users and integrations, test coverage, CHANGELOG, deprecation markers, the README. State your inference in one line; ask only if it's genuinely unclear and the answer would change the approach.

| Product stage | Optimize for | Watch out for |
|---|---|---|
| **Introduction** (new, pre-PMF) | Learning speed, simplest working thing, instrumentation to learn from users | Premature abstraction, platform-building, gold-plating |
| **Growth** (adoption rising) | Scalability, reliability, onboarding, extensibility, performance budgets | Debt that blocks scaling; one-off hacks that will multiply |
| **Maturity** (stable, many users) | Backward compatibility, consistency with conventions, small reversible changes, zero-downtime migrations, cost | Breaking changes, novel patterns that fragment the codebase |
| **Decline / Sunset** | Minimal safe change, migration paths, deprecation notices, data export | Investing in features nobody will use; stranding users |

## Step 3: Pull in the specialists the change needs

The core personas in Step 4 own each stage. Specialists are different: each one cares about a *domain* (data, security, infrastructure) and contributes across many stages. A data engineer shapes the spec (data contracts), the design (schema), the build (migrations), verification (data quality), and sunset (retention). So specialists are pulled in by what the change touches, not by which stage you're in.

Scan the request, the code you'll change, and the spec for these signals, then read only the matching files:

| If the change touches… | Load | Specialists inside |
|---|---|---|
| Schemas, migrations, data stores, pipelines, backfills, analytics events, metrics | `references/specialists/data.md` | Data Engineer/DBA, Analytics Engineer |
| Auth, permissions, user input, secrets, uploads, new dependencies, personal data | `references/specialists/security.md` | Application Security Engineer, Privacy Engineer |
| Infrastructure, deploy pipelines, CI/CD, config, scaling, reliability, cloud cost | `references/specialists/platform.md` | Infrastructure Engineer, DevOps/Platform Engineer, SRE, FinOps |
| Any Standard or Full track; performance budgets; onboarding or developer tools | `references/specialists/quality.md` | QA Automation Engineer, Exploratory Tester, Performance Engineer, Beta Tester (simulated) |
| Regulated data or frameworks (SOC 2, HIPAA, PCI, GDPR…), audit logs, retention, data residency | `references/specialists/compliance.md` | Compliance Officer |
| UI, mobile, public APIs/SDKs/CLIs, docs, user-facing copy, multiple locales | `references/specialists/experience.md` | Frontend, Mobile, Accessibility, API Designer/DX, Technical Writer, Localization |
| Model calls, prompts, agents, retrieval, embeddings, ML features | `references/specialists/ai-ml.md` | ML/AI Engineer, AI Safety Reviewer |

How to use them:

- **One owner per decision.** The stage's core persona makes the call. Specialists add requirements, raise risks, and add evidence to the gate. A specialist can block a gate only within its own domain (for example, security can block Launch over a missing authorization check).
- **Fold their output into the existing stages.** A specialist's Define lens adds constraints and acceptance criteria to `spec.md`; its Design lens adds sections or decisions to `design.md`; its gate evidence joins the stage's gate. Don't create separate per-specialist documents.
- **Scale with the track.** On Quick, skim the matching files' red flags only. On Standard, apply the lenses for the stages you run. On Full, apply all of them and include each specialist's gate evidence in the handoff.
- **Usually one to three.** If more than four match, the change may be too big for one pass; consider splitting it in Define.
- Say which specialists you pulled in, in one line, so the user can add or remove one.

## Step 4: Move through the stages

Each stage lists its core personas, what to do, what it produces, and the gate to pass. Detailed core persona checklists are in `references/personas.md`; artifact templates are in `references/templates.md`. Read them when you start a Standard or Full track. At every gate, also check the gate evidence of the specialists you pulled in.

### 1. Discover — *Product Manager, UX Researcher*

Understand the problem before choosing a solution.

- Read the request, then the code, docs, issues, and conventions around it. Answer what you can from the codebase before asking the user anything.
- Identify who has the problem and what kind of user they are. Early adopters tolerate rough edges for capability; mainstream users want it to just work; late adopters want it to be familiar and cheap. This shapes defaults, error handling, and polish.
- Name the job the user is trying to get done, their current workaround, constraints, and what is explicitly out of scope.
- If you need the user, ask at most three questions at once, each with a proposed default, so they can answer "yes" fast.

**Gate:** You can state the problem in one sentence without mentioning the solution, and say who has it.

### 2. Define — *Product Manager*

Turn the problem into a contract you can be held to.

- Write `spec.md`: problem, users, goals, non-goals, user stories, acceptance criteria, success signals, constraints, assumptions, open questions.
- Write acceptance criteria as observable, testable statements (Given/When/Then works well). "Fast" is not testable; "p95 under 200 ms for 10k rows" is.
- Non-goals matter as much as goals. They prevent scope creep and tell reviewers what not to expect.

**Gate:** Every acceptance criterion is testable; non-goals are explicit; every open question is resolved or has a stated assumption. On the Full track, get the user's sign-off here.

### 3. Design — *UX Designer, Software Architect, Security Reviewer* (plus the specialists from Step 3, who do most of their work here)

Decide how, while changes are still cheap.

- **Experience:** Map the flows and every state: empty, loading, partial, error, success, permission denied. Write real copy, not placeholders. Consider accessibility. For APIs, CLIs, and libraries, the developer experience *is* the UX: naming, defaults, error messages, discoverability.
- **Architecture:** Start from the existing patterns; a new pattern must earn its place. For non-trivial choices, sketch two or three options and record the choice and its reasoning (a short ADR). Cover data model, interfaces and contracts, failure modes, observability, migration and rollback, performance, security and privacy.
- Find the riskiest assumption. If it's uncertain, plan a small spike to test it before committing.

**Gate:** Someone could implement from `design.md` without asking you; every acceptance criterion maps to a component; irreversible decisions are flagged. On the Full track, get the user's sign-off here.

### 4. Build — *Senior Engineer*

- Plan thin vertical slices, each one testable on its own. Do the riskiest slice first so surprises surface early.
- Read neighboring code before writing, and match its conventions over personal preference.
- Write tests alongside the code. For logic with clear criteria, write the test first.
- Keep diffs reviewable. Note useful refactors as follow-ups rather than slipping them in.
- When reality contradicts the design, stop and update `design.md` (and the spec, if scope changed) instead of drifting silently. The docs stay true to what was built.

**Gate (per slice):** It builds; new and existing tests pass; lint and type checks are clean.

### 5. Verify — *QA Engineer, then an independent Reviewer*

- Walk every acceptance criterion and prove each one with a test or a demonstration you actually ran.
- Probe edges: empty and huge inputs, concurrency, unicode, permissions, network or dependency failure, re-running (idempotency), upgrade from the previous version.
- Run the whole suite, not just the new tests.
- For UI, render it and look at it in each state.
- Review the diff as a skeptic would. If the harness supports subagents, give a fresh reviewer the spec and the diff (not your reasoning) so the work isn't grading itself.

**Gate:** All criteria proven; no known failing tests; review findings fixed or explicitly deferred with a reason.

### 6. Launch — *Release Engineer/SRE, Product Marketing, Support*

- **Rollout:** feature flag or staged rollout where the product stage calls for it, migration order, backward compatibility, a rollback plan that has been thought through.
- **Observability:** logs, metrics tied to the success signals in the spec, alerts for the failure modes in the design.
- **Communication:** a PR description written for the reviewer, CHANGELOG and docs, upgrade notes, known limitations.

**Gate:** Someone else could ship it, monitor it, and roll it back without asking you.

### 7. Evolve — *Growth PM, Platform Engineer*

Use this stage when the work is improving something already shipped. Compare actual behavior against the original success signals, find the constraint (performance, reliability, usability, debt), and run the change through Define → Verify like any other. Look for repeated workarounds in the code; they usually point at the real need.

### 8. Sunset — *Portfolio PM, Support, Compliance*

For deprecating or removing something:

- Inventory every dependent: code paths, APIs, jobs, docs, users, data.
- Provide the replacement and a migration guide before removal.
- Deprecate in steps: warn, then disable behind a flag, then remove. Each step is reversible until the last.
- Handle data deliberately: export, retention, deletion obligations.
- Verify nothing still calls it (logs, search, metrics) before final removal.

See the deprecation template in `references/templates.md`.

## Human checkpoints

By default, move through gates on your own for Quick and Standard tracks, and pause for the user at Define and Design on the Full track. Always pause before an irreversible action (destructive migration, public API break, data deletion) regardless of track. If the user has said how they want to work ("just build it", "check with me at each step"), follow that instead.

## Where artifacts live

Follow the repository's existing docs convention if there is one. Otherwise use `docs/specs/<YYYY-MM-DD>-<short-slug>/` with `spec.md`, `design.md`, and `launch.md`. Keep them short and treat them as living documents. If there's no file system, put the artifacts in your reply.

## Handoff

Finish with a brief report:

- **What shipped:** one or two sentences.
- **Verification:** the acceptance criteria as a checklist, each with how it was proven.
- **Specialists consulted:** which ones, and their gate evidence.
- **Decisions and deviations:** what changed from the design, and why.
- **Follow-ups and risks:** what's deferred, what to watch after launch.

## Anti-patterns

- **Ceremony over substance:** a three-page spec for a typo fix. Size the track.
- **Persona theater:** "As a Product Manager, I think…". Apply the lens silently; show its results.
- **Asking what you could have read:** check the code and docs before asking the user.
- **Designing in a vacuum:** a clean architecture that ignores how the existing system works.
- **Gold-plating:** building beyond the acceptance criteria. Note ideas as follow-ups.
- **Unverified done:** claiming success without running the code or the tests.
- **Silent drift:** the implementation diverges from the spec and nobody is told.
