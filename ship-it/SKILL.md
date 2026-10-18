---
name: ship-it
description: Use when the user asks to build, design, plan, ship, refine, improve, polish, or tweak anything non-trivial: a feature, fix, tool, service, app, migration, deprecation, UI, or "v2 of X". Also when they say "plan this out", "take this end to end", "write a spec / PRD / design doc / technical design", "how should I approach this", or ask for an implementation plan before coding. Skip for one-liners, pure questions, and explicit "just write the code" requests.
---

# Ship It

Most weak agent output comes from two separate failures: skipped stages (code written before the problem is understood) and unverified gates (the agent grades its own work). This skill fixes both.

**Stages** give the order of work and the expert lens for each phase. A different kind of thinking is needed at Discover than at Launch — different questions, different failure modes, different people who'd catch the problems. Moving through stages in order is not ceremony; it's how errors are caught when they're cheapest to fix.

**Adversarial gates** replace self-assessed checklists. At each gate, the artifact produced is handed to a reviewer who has not seen the conversation — only the document. If the artifact needs explanation to make sense, it isn't done. This is the minimal-context test: every artifact must stand alone.

**Scale** matters. A three-page spec for a typo fix is its own failure. The track system sizes the process to the work: Patch gets no process; Full gets all of it.

---

## Step 0: Infer the product stage

Before selecting a track, read the repo for signals: age and activity, versioning, test coverage, CHANGELOG, deprecation markers, README tone, issue volume. Infer one of:

| Product stage | Optimize for | Watch out for |
|---|---|---|
| **Introduction** (new, pre-PMF) | Learning speed, simplest working thing, instrumentation to learn from users | Premature abstraction, platform-building, gold-plating |
| **Growth** (adoption rising) | Scalability, reliability, onboarding, extensibility, performance budgets | Debt that blocks scaling; one-off hacks that will multiply |
| **Maturity** (stable, many users) | Backward compatibility, consistency with conventions, small reversible changes, zero-downtime migrations, cost | Breaking changes, novel patterns that fragment the codebase |
| **Decline / Sunset** | Minimal safe change, migration paths, deprecation notices, data export | Investing in features nobody will use; stranding users |

State the inference in one line: *"Reading this as a [stage]-stage product ([key signals]). Correct me if that's wrong — it changes what I optimize for."*

**Always confirm** if the track is Full or if the inference is Decline.

---

## Step 1: Pick a track

| Track | Use when | Stages | Adversarial depth |
|---|---|---|---|
| **Patch** | One-liner fix, no design needed | None — state the change and do it | None |
| **Quick** | Small, well-understood change in a clear area | Define-lite → Build → Verify | Self-review only |
| **Standard** | A new feature or meaningful change in an existing product | Discover → Define → Design → Build → Verify → Launch | Self-review on Define, Plan, Launch; subagent on Design, Verify |
| **Full** | New product or service; cross-cutting change; anything hard to reverse (schema, public API, auth, billing, data deletion); migration | All stages, human checkpoints at Define and Design | Subagent on all artifact-producing stages |

State which track you chose and why in one line. When unsure, take the bigger track for the *thinking* and the smaller one for the *paperwork*.

---

## Step 2: Load the specialists this change needs

Scan the request, the code you'll touch, and the spec (once written) for these signals. Load only what matches.

| If the change touches… | Load | Specialists inside |
|---|---|---|
| Schemas, migrations, data stores, pipelines, backfills, analytics events, metrics | `references/specialists/data.md` | Data Engineer/DBA, Analytics Engineer |
| Auth, permissions, user input, secrets, uploads, new dependencies, personal data | `references/specialists/security.md` | Application Security Engineer, Privacy Engineer |
| Infrastructure, deploy pipelines, CI/CD, config, scaling, reliability, cloud cost | `references/specialists/platform.md` | Infrastructure Engineer, DevOps/Platform Engineer, SRE, FinOps |
| Any Standard or Full track; performance budgets; onboarding or developer tools | `references/specialists/quality.md` | QA Automation Engineer, Exploratory Tester, Performance Engineer, Beta Tester |
| Regulated data or frameworks (SOC 2, HIPAA, PCI, GDPR…), audit logs, retention | `references/specialists/compliance.md` | Compliance Officer |
| UI, mobile, public APIs/SDKs/CLIs, docs, user-facing copy, multiple locales | `references/specialists/experience.md` | Frontend, Mobile, Accessibility, API Designer/DX, Technical Writer, Localization |
| Model calls, prompts, agents, retrieval, embeddings, ML features | `references/specialists/ai-ml.md` | ML/AI Engineer, AI Safety Reviewer |

State which specialists you loaded in one line so the human can add or remove one.

**How specialist files work with reviewer prompts:** Each specialist file contains a `## Reviewer Injection` section at the end of each sub-specialist block. When a specialist is active, append its Reviewer Injection blocks to the base reviewer prompt for the stages that specialist covers. The injection is additive — it never replaces the base reviewer questions. See `references/specialists/` for the full specialist lenses; the Reviewer Injection blocks are the compact form for augmenting reviewers.

---

## Step 3: Move through the stages

Detailed persona checklists are in `references/personas.md`. Artifact templates are in `references/templates.md`. Reviewer prompt files are in `references/reviewers/`.

### Adversarial gate mechanics

When a stage calls for a **subagent reviewer**:
1. Prepare the artifact(s) named in the stage
2. Open the reviewer prompt file for that stage
3. Append any active specialists' Reviewer Injection blocks to the prompt (for the stages they cover)
4. Dispatch the subagent with only the artifact(s) and the combined prompt — no conversation history, no background
5. Apply convergence rule: no blocking issues → advance; two passes with only minor/advisory findings → advance; minor findings never block; a recurring finding after a fix means the fix was wrong, not another pass

When a stage calls for **self-review**:
Apply the reviewer prompt's questions yourself, explicitly. State what you found. This is weaker than a subagent pass — it accepts a small fidelity loss in exchange for lower overhead on Standard track.

**Quick track**: self-review on all stages that have a gate.
**Standard track**: self-review on Define, Plan, Launch; subagent on Design, Verify.
**Full track**: subagent on all artifact-producing stages.

---

### 1. Discover — *Product Manager, UX Researcher*

Understand the problem before choosing a solution.

- Read the request, then the code, docs, issues, and conventions around it. Answer what you can from evidence before asking the user anything.
- Identify who has the problem and what kind of user they are (see customer personas in `references/personas.md`).
- Name the job the user is trying to get done, their current workaround, constraints, and what is explicitly out of scope.
- Produce an **evidence summary**: what you found (issues, tickets, usage patterns, feedback) and what you could not find. Turn every unvalidatable assumption into an open question for the Define stage.

**For vague or open-ended requests** ("refine X", "improve Y", "I want to tweak Z"), do the discovery conversation inline — do not hand off to a separate brainstorming skill. Ask at most three focused questions at a time, each with a proposed default so the user can answer fast. Stay in this stage until you can state the problem in one sentence and say who has it. Only then advance to Define.

- If you need the user, ask at most three questions at once, each with a proposed default.

**Gate:** You can state the problem in one sentence without mentioning a solution, and say who has it. No reviewer dispatch — this is a thinking pass, not an artifact.

*Adversarial depth: skip (no artifact to review).*

---

### 2. Define — *Product Manager*

Turn the problem into a contract you can be held to.

- Write `spec.md` (template in `references/templates.md`): problem, users, goals, non-goals, user stories, acceptance criteria, success signals, constraints, assumptions and open questions.
- Write acceptance criteria as observable, testable statements. "Fast" is not testable; "p95 under 200 ms for 10k rows" is.
- Non-goals matter as much as goals.
- Carry forward every unvalidatable assumption from Discover as an explicit entry with an owner and a default.

**Spec-optional exception:** When the change is entirely within an existing design language (style, polish, copy, spacing) and the acceptance criteria belong naturally in `design.md`, `spec.md` may be omitted — *if and only if* `design.md` explicitly carries the problem statement, acceptance criteria, and non-goals. State the omission in one line: *"Skipping spec.md — ACs and non-goals are in design.md."* A silent skip is not this exception.

**Gate:** Every acceptance criterion is testable; non-goals are explicit; every open question is resolved or has a stated owner and default. On Full track, get the user's sign-off here.

*Adversarial depth: self-review on Quick/Standard; subagent (dispatch `references/reviewers/define.md`) on Full.*

---

### 3. Design — *UX Designer, Software Architect, Security & Privacy Reviewer*

Decide how, while changes are still cheap.

- **Experience:** Map all states: empty, loading, partial, error, success, no permission. Write real copy. For APIs, CLIs, libraries: naming, defaults, error messages, discoverability.
- **Architecture:** Start from existing patterns. For non-trivial choices, sketch two or three options and record the decision and reasoning (ADR format in the template). Cover data model, interfaces, failure modes, observability, migration and rollback, performance, security and privacy.
- Find the riskiest assumption. If uncertain, plan a spike before committing.
- Write `design.md` (template in `references/templates.md`).

**Gate:** Someone could implement from `design.md` without asking you; every acceptance criterion maps to a component; irreversible decisions are flagged. On Full track, get the user's sign-off here.

*Adversarial depth: self-review on Quick; subagent (dispatch `references/reviewers/design.md`) on Standard/Full.*

**project-lenses activation:** On Standard or Full track, for user-facing changes on Introduction or Growth stage products, invoke `superpowers:project-lenses` for a persona coverage pass after the design reviewer is clean.

---

### 4. Build — *Senior Engineer*

- Write the implementation plan as the `## Implementation plan` section of `design.md`. Order slices thin and vertical, riskiest first, each independently testable.
- Before dispatching the plan reviewer: **extract only the `## Implementation plan` section** from `design.md` and pass it alongside `spec.md` only. Do not pass the full `design.md`. This preserves reviewer independence.
- Read neighboring code before writing; match conventions.
- Write tests alongside code. For logic with clear criteria, write the test first.
- Keep diffs reviewable. Note useful refactors as follow-ups.
- When reality contradicts the design, update `design.md` rather than drifting silently.

**Plan gate (before starting execution):** Every spec requirement maps to a task; tasks are in buildable order; interfaces are consistent.

*Plan gate adversarial depth: self-review on Quick; self-review on Standard; subagent (dispatch `references/reviewers/plan.md` with spec + plan section only) on Full.*

**Per-slice gate:** It builds; new and existing tests pass; lint and type checks are clean; implementation matches the design. Any deviation from the design — even a small one (a value that differs, a component that was swapped) — gets a note in the relevant ADR or a new ADR entry before the slice is considered done. Silent drift at the slice level compounds into a design doc that no longer describes the system.

---

### 5. Verify — *QA Engineer, Independent Reviewer*

- Walk every acceptance criterion and prove each one with a test or a demonstration you actually ran.
- Probe edges: empty and huge inputs, concurrency, unicode, permissions, network failure, re-running (idempotency), upgrade from the previous version.
- Run the whole suite, not just the new tests.
- For UI: render and inspect every state.
- **Record the result.** Fill the Verification section of the handoff report (or add a checklist to `design.md` if spec was omitted) with each AC and how it was proven, committed alongside the code. An unrecorded Verify pass didn't happen.
- **Advance the doc status.** Update `spec.md` Status to `Built`, or `design.md` Status to `Current` if spec was omitted. Do this before closing the gate.

**Gate:** All criteria proven and recorded; no known failing tests; review findings fixed or explicitly deferred with a reason; doc status updated.

*Adversarial depth: self-review on Quick; subagent (dispatch `references/reviewers/verify.md`) on Standard/Full.*

---

### 6. Launch — *Release Engineer/SRE, Product Marketing, Support*

- Write `launch.md` (template in `references/templates.md`).
- **Rollout:** feature flag or staged rollout where the product stage calls for it; migration order; backward compatibility; a rollback plan that has been thought through.
- **Observability:** logs, metrics tied to the success signals in the spec, alerts for the failure modes in the design.
- **Communication:** PR description, CHANGELOG, docs, upgrade notes.

**Gate:** Someone else could ship it, monitor it, and roll it back without asking you.

*Adversarial depth: self-review on Quick/Standard; subagent (dispatch `references/reviewers/launch.md`) on Full.*

---

### 7. Evolve — *Growth PM, Platform Engineer, SRE*

Use this stage when improving something already shipped.

- Write an **evolution brief** (template in `references/templates.md`): current metric vs. original success signal, identified constraint and its evidence, proposed change.
- The proposed change re-enters the pipeline at Define. Treat it as a new Standard or Full track request depending on scope.
- When the new Define cycle produces a `spec.md`, mark the previous `spec.md` as `Status: Superseded` and add a `Superseded by:` link to the new one.
- Update the **living sections** of `design.md` (Overview, Architecture, Data and contracts, Failure modes, etc.) to reflect the current system. Do not edit past ADR entries — append new ones instead.

**Gate:** The constraint diagnosis is supported by evidence (not assumption); the proposed change addresses the constraint, not something adjacent.

*Adversarial depth: subagent (dispatch `references/reviewers/evolve.md`) on all tracks that reach this stage.*

---

### 8. Sunset — *Portfolio PM, Support, Compliance*

For deprecating or removing something:

- Write a **dependent inventory and removal plan** (template in `references/templates.md`): every dependent (code paths, APIs, jobs, docs, users, data), migration paths, removal sequence, retention obligations.
- Deprecate in steps: warn → disable behind flag → remove. Each step is reversible until the last.
- Verify nothing still calls it (logs, search, metrics) before final removal.
- Provide the replacement and migration guide before removal.

**Gate:** Nothing still calls the thing being removed; migration paths exist for every dependent; data retention obligations are stated.

*Adversarial depth: subagent (dispatch `references/reviewers/sunset.md`) on all tracks that reach this stage.*

---

## Human checkpoints

Stop and ask the human at these points only:

| When | What to ask |
|---|---|
| Track or product stage ambiguous | State inference; ask to confirm or correct |
| After Define gate (Full track) | Questions only they can resolve: scope, trade-offs, open questions from Discover |
| After Design gate (Full track) | Same; plus any irreversible architectural decision |
| Before any irreversible action (any track) | Explicit confirmation regardless of track |
| After execution | Final summary: what shipped, rulings made, deferred minors |

Do not stop between stages for progress updates. Do not ask "should I continue?" — that is not a decision requiring human intent.

---

## Convergence rule

A stage's adversarial loop converges when there are no blocking issues. Do not raise the loop count to chase advisory or stylistic feedback. Two consecutive passes with only minor or advisory findings is operational convergence — advance. A finding that recurs after your fix means the fix was wrong, not that you need another pass. Diagnose the fix before looping again.

---

## Handoff

Finish with a brief report:

- **What shipped:** one or two sentences
- **Verification:** the acceptance criteria as a checklist, each with how it was proven
- **Specialists consulted:** which ones, and their gate evidence
- **Decisions and deviations:** what changed from the design, and why
- **Follow-ups and risks:** what's deferred, what to watch after launch

---

## Anti-patterns

| Pattern | Why it fails |
|---|---|
| Ceremony over substance | A three-page spec for a typo fix. Size the track. |
| Persona theater | Narrating "As a Product Manager, I think…". Apply the lens silently; show its results. |
| Asking what you could have read | Check the code and docs before asking the user. |
| Designing in a vacuum | A clean architecture that ignores how the existing system works. |
| Gold-plating | Building beyond the acceptance criteria. Note ideas as follow-ups. |
| Unverified done | Claiming success without running the code or the tests. |
| Silent drift | The implementation diverges from the spec and nobody is told. |
| Skipping the red-team | The red-team finds what you cannot see. It costs one subagent call. |
| Spec that restates the design | The spec must be independently buildable. If a developer can't build from it without the design, it isn't done. |
| Chasing minor findings | Minor findings don't block. Advance and note them. |
| Accumulating human gates | The human resolves open questions at each gate. Accumulating them means executing on wrong assumptions. |
| Self-review as the final reviewer | Same author, same blind spots. An independent subagent is the only fresh context the stage buys — don't replace it with another read of your own diff. |
