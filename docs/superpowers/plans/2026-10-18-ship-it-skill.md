# ship-it Skill Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create the `ship-it` skill — a combined lifecycle + adversarial-validation skill that supersedes both `lifecycle-driven-delivery` and `design-to-execution`.

**Architecture:** New skill directory `ship-it/` alongside `lifecycle-driven-delivery/`. Core skill logic lives in `SKILL.md`. Supporting files live in `references/`: stage-specific reviewer prompts in `references/reviewers/`, updated specialist files (carrying existing content plus new Reviewer Injection blocks) in `references/specialists/`, updated artifact templates in `references/templates.md`, and the existing personas reference adapted in `references/personas.md`.

**Tech Stack:** Markdown only. No code. All files are skill instruction documents read by an LLM agent at runtime.

**Spec:** This plan implements the design established across the gap analysis and weakness-resolution conversation in this session. The spec is the conversation itself; key decisions are restated in each task.

## Global Constraints

- All files are Markdown; no tooling, scripts, or non-Markdown assets
- Directory structure mirrors `lifecycle-driven-delivery/` as the established pattern
- Skill frontmatter must have `name:` and `description:` fields; description controls when the skill fires — write it to fire instead of both source skills
- Reviewer prompt files must be independently readable — no references to other files inside the prompt text itself
- Specialist files must remain backward-compatible with `lifecycle-driven-delivery/` (existing content untouched, Reviewer Injection section appended)
- Every prose instruction must be followable without the conversation context that produced it

## Review Focus

- A reviewer reading only `SKILL.md` must be able to run the full process without opening any other file — check that all cross-references to `references/` are explicit and the files they point to exist
- Stage gates must state who grades them (self-review vs. subagent) unambiguously per track — ambiguity here silently degrades to author-graded checklists
- Reviewer Injection blocks must be tight enough to fit in a reviewer prompt without diluting focus — each block capped at 5 questions
- The product-stage inference surfacing must happen exactly once and produce a correctable one-liner, not a multi-step interrogation
- The plan extraction step for W6 (plan section isolated before reviewer dispatch) must be described procedurally enough that an agent can follow it without judgment calls

---

## File Structure

**Create:**
- `ship-it/SKILL.md` — core skill: tracks, stages, gates, adversarial depth table, specialist loading, human gates, anti-patterns
- `ship-it/references/personas.md` — adapted from LDD's personas.md; adds customer persona table and adopter types
- `ship-it/references/templates.md` — adapted from LDD's templates.md; adds evolution brief, dependent inventory, plan extraction note
- `ship-it/references/reviewers/define.md` — reviewer prompt for the Define stage
- `ship-it/references/reviewers/design.md` — reviewer prompt for the Design stage
- `ship-it/references/reviewers/plan.md` — reviewer prompt for the Plan stage (receives spec + plan section only)
- `ship-it/references/reviewers/verify.md` — reviewer prompt for the Verify stage
- `ship-it/references/reviewers/launch.md` — reviewer prompt for the Launch stage
- `ship-it/references/reviewers/evolve.md` — reviewer prompt for the Evolve stage
- `ship-it/references/reviewers/sunset.md` — reviewer prompt for the Sunset stage
- `ship-it/references/specialists/data.md` — LDD data.md content + Reviewer Injection block
- `ship-it/references/specialists/security.md` — LDD security.md content + Reviewer Injection block
- `ship-it/references/specialists/platform.md` — LDD platform.md content + Reviewer Injection block
- `ship-it/references/specialists/quality.md` — LDD quality.md content + Reviewer Injection block
- `ship-it/references/specialists/compliance.md` — LDD compliance.md content + Reviewer Injection block
- `ship-it/references/specialists/experience.md` — LDD experience.md content + Reviewer Injection block
- `ship-it/references/specialists/ai-ml.md` — LDD ai-ml.md content + Reviewer Injection block

**Do not modify:**
- `lifecycle-driven-delivery/` — source skill, left intact

---

### Task 1: SKILL.md — core skeleton

**Files:**
- Create: `ship-it/SKILL.md`

**Interfaces:**
- Produces: the top-level skill document all other files are referenced from; defines stage names, track names, adversarial depth labels (skip / self-review / subagent), and specialist trigger signals that later tasks must match exactly

- [ ] **Step 1: Write the frontmatter and opening**

```markdown
---
name: ship-it
description: Takes a feature, product, or significant change from idea to shipped, verified, maintainable code. Combines a full product lifecycle (discover, define, design, build, verify, launch, evolve, sunset) with adversarial red-team validation at every stage gate. Use this whenever you would use design-to-execution or lifecycle-driven-delivery: building a new feature, app, service, or tool; writing a spec, PRD, design doc, or implementation plan; saying "plan this out", "design and build", or "take this end to end"; migrations, deprecations, and sunsetting. Skip it for one-line fixes, pure questions, or when the user explicitly wants only code with no planning.
---
```

- [ ] **Step 2: Write the opening rationale section**

Cover: why stages exist (different expert questions at each), why adversarial gates exist (artifacts must stand alone), why scale matters (Quick track for small changes, Full for irreversible ones). Three short paragraphs, no headers inside the section.

- [ ] **Step 3: Write Step 0 — product-stage inference**

This runs before track selection. The agent reads repo signals (age, activity, versioning, test coverage, CHANGELOG, deprecation markers, README) and infers one of: Introduction / Growth / Maturity / Decline.

Outputs one line: *"Reading this as a [stage]-stage product ([signals]). Correct me if that's wrong — it changes what I optimize for."*

Rules: always confirm if Full track; always confirm if inferring Decline. Otherwise state and proceed.

Include the product-stage table from LDD (what to optimize for, what to watch out for) verbatim.

- [ ] **Step 4: Write Step 1 — track selection**

Four tracks:

| Track | When | Stages | Adversarial depth |
|---|---|---|---|
| Patch | One-liner fix, no design needed | None — state the change and do it | None |
| Quick | Small well-understood change | Define-lite → Build → Verify | Self-review only |
| Standard | New feature in existing product | Discover → Define → Design → Build → Verify → Launch | Self-review on Define/Plan/Launch; subagent on Design/Verify |
| Full | New product, cross-cutting, hard-to-reverse (schema, public API, auth, billing, data deletion), migration | All stages, human checkpoints at Define + Design | Subagent on all artifact stages |

One line: "Tell me which track you chose and why, so you can redirect."

- [ ] **Step 5: Write Step 2 — specialist loading**

Reproduce the specialist trigger table from LDD exactly, with one addition: each row notes the file path (`references/specialists/X.md`) and states that the file's **Reviewer Injection** block will be appended to the relevant stage reviewer prompts when that specialist is active.

Explain the injection model in two sentences: scan the request, the code, and the spec for trigger signals; load only matching specialist files; the Reviewer Injection section of each loaded file is appended to the base reviewer prompt for the stages that file covers.

- [ ] **Step 6: Write Step 3 — stages**

For each of the eight stages, write a section with:
- Core personas and what they own (from LDD's personas.md)
- What to do (condensed from LDD; pruned of redundancy)
- What it produces (artifact name and path, or "none" for Discover)
- Gate: self-review checklist OR "dispatch the reviewer in `references/reviewers/X.md`" depending on track
- Adversarial depth note: which tracks get subagent vs. self-review vs. skip

For Evolve: the artifact is an **evolution brief** (current metric vs. success signal, identified constraint, proposed change). Gate: dispatch `references/reviewers/evolve.md`.

For Sunset: the artifact is a **dependent inventory + removal plan**. Gate: dispatch `references/reviewers/sunset.md`.

For the plan gate specifically: include the extraction instruction — "Before dispatching the plan reviewer, extract only the Implementation Plan section from `design.md` and pass that alongside `spec.md`. Do not pass the full design document."

- [ ] **Step 7: Write Step 4 — human gates table**

| When to stop | What to ask |
|---|---|
| Track or product stage ambiguous | State inference; ask to confirm or correct |
| After Define (Full track only) | Questions only the human can resolve; scope and trade-off decisions |
| After Design (Full track only) | Same; plus any irreversible architectural decision |
| Before any irreversible action (any track) | Explicit confirmation regardless of track |
| After execution | Final summary: what shipped, rulings made, deferred minors |

Anti-patterns table: "should I continue?" is not a human gate; progress updates are not human gates.

- [ ] **Step 8: Write the convergence rule, anti-patterns, and handoff sections**

Convergence rule (from D2E): no blocking issues = advance; two passes with only minor/advisory = advance; minor findings never block; a recurring finding after a fix means the fix was wrong, not another pass.

Anti-patterns: merge the lists from both source skills, remove duplicates, keep the table format.

Handoff: what shipped, verification checklist (ACs with how each was proven), specialists consulted and their gate evidence, decisions and deviations, follow-ups and risks.

- [ ] **Step 9: Read `ship-it/SKILL.md` as if you have never seen the conversation that produced it**

Check: can you follow the process from top to bottom without opening any other file? Are all cross-references to `references/` explicit? Are the track/stage/adversarial-depth combinations unambiguous? Fix anything that requires inferring from context.

---

### Task 2: Reviewer prompts (seven files)

**Files:**
- Create: `ship-it/references/reviewers/define.md`
- Create: `ship-it/references/reviewers/design.md`
- Create: `ship-it/references/reviewers/plan.md`
- Create: `ship-it/references/reviewers/verify.md`
- Create: `ship-it/references/reviewers/launch.md`
- Create: `ship-it/references/reviewers/evolve.md`
- Create: `ship-it/references/reviewers/sunset.md`

**Interfaces:**
- Consumes: stage names and artifact names from Task 1 (`spec.md`, `design.md`, evolution brief, dependent inventory)
- Produces: reviewer prompt files; each is a complete, self-contained prompt an agent can paste an artifact into and dispatch as a subagent — no other context needed

- [ ] **Step 1: Write `references/reviewers/define.md`**

Persona: skeptical Product Manager who has only the spec. Has not seen the request or conversation.

Ask: Is the problem stated without mentioning a solution? Are acceptance criteria observable and testable (not "fast", not "good")? Are non-goals explicit? Are open questions resolved or assigned an owner + default? Would two PMs reading this write the same spec?

Return format: summary (2–3 sentences on whether this spec is ready to design from), blocking issues (things that would cause the wrong feature to be built), questions (things requiring human intent to resolve). No blocking issues = ready to advance.

End with: "Artifact to review: [paste spec.md here]"

- [ ] **Step 2: Write `references/reviewers/design.md`**

Persona: skeptical Software Architect who has only `spec.md` and `design.md`. Has not seen the conversation.

Ask: Does every acceptance criterion map to a component in the design? Are irreversible decisions flagged? Are failure modes covered? Is the migration and rollback story present? Are options and their reasoning recorded (ADRs)? Could someone implement from this without asking questions?

Return format: same as define.md. No blocking issues = ready to plan.

End with: "Artifacts to review: [paste spec.md, then design.md]"

- [ ] **Step 3: Write `references/reviewers/plan.md`**

Persona: skeptical engineer who has only `spec.md` and the Implementation Plan section (not the full design doc).

Ask: Does every spec requirement map to at least one task? Are tasks in a buildable order (no task depends on something not yet created)? Are interfaces between tasks consistent (a function named X in Task 2 is not called Y in Task 4)? Are there steps that require knowledge not in the plan? Would a developer hit a dead end?

Return format: same. No blocking issues = ready to execute.

Include a note at the top: "You have the spec and the implementation plan only. You do not have the full design document. That is intentional."

End with: "Artifacts to review: [paste spec.md, then Implementation Plan section only]"

- [ ] **Step 4: Write `references/reviewers/verify.md`**

Persona: skeptical QA Engineer and independent reviewer who has the spec and the diff/output of what was built. Has not been part of the build.

Ask: Is every acceptance criterion proven by something that was actually run? Are edge cases covered (empty, huge, concurrent, unicode, re-run, upgrade)? Does the whole suite pass? For UI: has every state been rendered and checked? Does the diff do what the spec says and nothing it doesn't?

Return format: same. No blocking issues = ready to launch.

End with: "Artifacts to review: [paste spec.md, then test output / diff]"

- [ ] **Step 5: Write `references/reviewers/launch.md`**

Persona: skeptical SRE who has only `launch.md`. Has not seen the rest of the work.

Ask: Could someone else ship this, monitor it, and roll it back without asking the author? Is the rollout mechanism stated? Is rollback tested and described step by step? Are metrics tied to the spec's success signals (not generic)? Are alerts tied to user-facing symptoms (not internal causes)? Are comms complete (PR description, CHANGELOG, upgrade notes)?

Return format: same. No blocking issues = ready to ship.

End with: "Artifact to review: [paste launch.md here]"

- [ ] **Step 6: Write `references/reviewers/evolve.md`**

Persona: skeptical Growth PM who has only the evolution brief (current metric vs. success signal, identified constraint, proposed change).

Ask: Is the metric comparison valid (same definition, same time window)? Is the constraint diagnosis supported by the evidence, or is it an assumption? Does the proposed change address the stated constraint, or something adjacent? Is the proposed change scoped to enter at Define with clear acceptance criteria?

Return format: same. No blocking issues = the evolution brief is ready; re-enter at Define with the proposed change.

End with: "Artifact to review: [paste evolution brief here]"

- [ ] **Step 7: Write `references/reviewers/sunset.md`**

Persona: skeptical Platform Engineer + Support lead who has only the dependent inventory and removal plan.

Ask: Is anything missing from the dependent list (code paths, APIs, jobs, docs, users, stored data, downstream consumers)? Is the removal sequence safe — does it stop writes before confirming reads are zero, archive before dropping, remove routes not just links? Are migration paths provided before removal, not after? Are retention and deletion obligations stated explicitly?

Return format: same. No blocking issues = removal plan is safe to execute.

End with: "Artifact to review: [paste dependent inventory and removal plan here]"

- [ ] **Step 8: Read each reviewer prompt file as if you are the subagent receiving it**

For each: does it tell you exactly what to look for? Does it tell you exactly what to return? Could you produce a useful review from it with only the named artifact(s) and no other context? Fix any that require inference.

---

### Task 3: Specialist files with Reviewer Injection blocks

**Files:**
- Create: `ship-it/references/specialists/data.md`
- Create: `ship-it/references/specialists/security.md`
- Create: `ship-it/references/specialists/platform.md`
- Create: `ship-it/references/specialists/quality.md`
- Create: `ship-it/references/specialists/compliance.md`
- Create: `ship-it/references/specialists/experience.md`
- Create: `ship-it/references/specialists/ai-ml.md`

**Interfaces:**
- Consumes: specialist file content from `lifecycle-driven-delivery/references/specialists/` (copy verbatim, then append)
- Produces: specialist files that are backward-compatible with LDD and additionally contain a `## Reviewer Injection` section at the end of each sub-specialist block

- [ ] **Step 1: Copy all seven specialist files from `lifecycle-driven-delivery/references/specialists/` verbatim**

Do not edit the existing content. Append only.

- [ ] **Step 2: Append Reviewer Injection block to `data.md`**

After the Data Engineer/DBA section, add:

```markdown
### Reviewer Injection — Data Engineer/DBA
Append to reviewer prompts when this specialist is active. Cap: 5 questions.

- Are schema changes reversible, and is the migration order safe (old code can run against the new schema before cutover)?
- Is any migration bundled with the code that depends on it (it must deploy first, separately)?
- Are new queries bounded — no full-table scans on growing tables without pagination or a limit?
- Is there a backfill plan, and is it idempotent (safe to re-run after failure)?
- Have migrations been tested both up and down?
```

After the Analytics Engineer section, add:

```markdown
### Reviewer Injection — Analytics Engineer
Append to reviewer prompts when this specialist is active.

- Is each success signal in the spec a named metric with a precise definition (numerator, denominator, time window)?
- Are events instrumented server-side for anything business-critical?
- Do any events contain personal data that wasn't explicitly approved?
```

- [ ] **Step 3: Append Reviewer Injection block to `security.md`**

After the Application Security Engineer section:

```markdown
### Reviewer Injection — Application Security Engineer
Append to reviewer prompts when this specialist is active.

- Where is authorization enforced, and is it server-side (not UI-only)?
- What input is untrusted, and where does it flow — database, shell, template, URL fetch?
- Are secrets and tokens absent from logs, URLs, and error messages?
- Are the "must be denied" paths (wrong user, missing token, escalated role) present as acceptance criteria?
- Have new dependencies been pinned and reviewed?
```

After the Privacy Engineer section:

```markdown
### Reviewer Injection — Privacy Engineer
Append to reviewer prompts when this specialist is active.

- Is each personal data field justified by a stated purpose?
- Is there a deletion and export path for user data?
- Is personal data absent from logs and analytics events?
```

- [ ] **Step 4: Append Reviewer Injection block to `platform.md`**

After Infrastructure Engineer:

```markdown
### Reviewer Injection — Infrastructure Engineer
Append to reviewer prompts when this specialist is active.

- Is all infrastructure declared as code with no manual console changes?
- Does the plan/diff destroy or replace any stateful resource unexpectedly?
- Are resources tagged with owner and cost center?
```

After DevOps / Platform Engineer:

```markdown
### Reviewer Injection — DevOps / Platform Engineer
Append to reviewer prompts when this specialist is active.

- Does configuration differ per environment through environment variables, not branches?
- Are feature flags present when the change needs a rollout or rollback lever?
- Is there a plan to remove flags once the rollout is complete?
```

After SRE:

```markdown
### Reviewer Injection — SRE
Append to reviewer prompts when this specialist is active.

- Do all network calls have timeouts?
- Are retries using backoff (not immediate retry storms)?
- Are alerts tied to user-facing symptoms, not internal causes nobody can act on?
- Is there a runbook for the most likely failure modes?
```

After FinOps:

```markdown
### Reviewer Injection — FinOps
Append to reviewer prompts when this specialist is active.

- Is there a cost estimate at both expected scale and 10× scale?
- Are paid API calls bounded — no unbounded loops over a paid service?
- Are budget alerts configured?
```

- [ ] **Step 5: Append Reviewer Injection block to `quality.md`**

After QA Automation Engineer:

```markdown
### Reviewer Injection — QA Automation Engineer
Append to reviewer prompts when this specialist is active.

- Does every acceptance criterion map to a test that runs in CI?
- Do any new tests use sleeps or rely on real clocks (they will be flaky)?
- Do tests fail before the change and pass after (for bug fixes)?
```

After Exploratory Tester:

```markdown
### Reviewer Injection — Exploratory Tester
Append to reviewer prompts when this specialist is active.

- Has the happy path been exercised by a human or simulation that tried to break it (double-submit, back button, paste odd input, two tabs)?
```

After Performance Engineer:

```markdown
### Reviewer Injection — Performance Engineer
Append to reviewer prompts when this specialist is active.

- Are performance budgets from the spec stated as acceptance criteria (p95, memory, bundle size)?
- Has performance been measured with realistic data volume, not ten rows?
```

After Beta Tester:

```markdown
### Reviewer Injection — Beta Tester
Append to reviewer prompts when this specialist is active.

- Starting only from the README or entry screen, can the main user story be completed without reading the code?
- Are setup steps that exist only in the builder's head documented?
```

- [ ] **Step 6: Append Reviewer Injection block to `compliance.md`**

After Compliance Officer:

```markdown
### Reviewer Injection — Compliance Officer
Append to reviewer prompts when this specialist is active.

- Is each applicable compliance control mapped to a component and a test?
- Are audit logs present for sensitive actions and do they record user, time, and action?
- Are any decisions pending human compliance sign-off (flag them explicitly — do not declare compliant)?
```

- [ ] **Step 7: Append Reviewer Injection block to `experience.md`**

After Frontend Engineer:

```markdown
### Reviewer Injection — Frontend Engineer
Append to reviewer prompts when this specialist is active.

- Are all states rendered and checked: empty, loading, error, success?
- Does the layout hold at phone width and in both light and dark themes if supported?
```

After Accessibility Specialist:

```markdown
### Reviewer Injection — Accessibility Specialist
Append to reviewer prompts when this specialist is active.

- Is the UI navigable by keyboard only, with no focus traps?
- Do interactive elements have labels (no unlabeled icon buttons)?
- Does contrast meet the project's stated standard?
```

After API Designer / Developer Experience:

```markdown
### Reviewer Injection — API Designer / DX
Append to reviewer prompts when this specialist is active.

- Are breaking changes flagged as product decisions, not implementation details?
- Do error messages say what happened and what to do next?
- Is there a working usage example written from the docs alone?
```

After Technical Writer:

```markdown
### Reviewer Injection — Technical Writer
Append to reviewer prompts when this specialist is active.

- Are README, docs, and CHANGELOG updated for any behavior users see?
- Does the documentation describe how to use it, not how it was built?
```

- [ ] **Step 8: Append Reviewer Injection block to `ai-ml.md`**

After ML / AI Engineer:

```markdown
### Reviewer Injection — ML / AI Engineer
Append to reviewer prompts when this specialist is active.

- Is "good output" defined as criteria that can be checked on examples, not subjectively?
- Are prompts versioned in the repo?
- Is there a fallback when the model fails, times out, or returns malformed output?
- Has an evaluation set been run — including hard and adversarial cases — and compared to a baseline?
```

After AI Safety Reviewer:

```markdown
### Reviewer Injection — AI Safety Reviewer
Append to reviewer prompts when this specialist is active.

- Is content from untrusted sources (web pages, emails, user uploads) treated as data, not instructions?
- Are irreversible agent actions gated on confirmation?
- Have adversarial test cases been run (injected instructions, tool misuse attempts)?
```

- [ ] **Step 9: Read each specialist file end-to-end**

Check: does the existing content read naturally into the new Reviewer Injection section? Does the injection section stay under 5 questions per sub-specialist? Are signal words in the injection blocks consistent with the trigger signals in `SKILL.md`? Fix any inconsistencies.

---

### Task 4: Personas and templates reference files

**Files:**
- Create: `ship-it/references/personas.md`
- Create: `ship-it/references/templates.md`

**Interfaces:**
- Consumes: `lifecycle-driven-delivery/references/personas.md` and `lifecycle-driven-delivery/references/templates.md` as starting points
- Produces: updated reference files with additions for ship-it; existing content preserved

- [ ] **Step 1: Write `ship-it/references/personas.md`**

Copy `lifecycle-driven-delivery/references/personas.md` verbatim. Then add two things:

1. After the "Customer personas" section, add a note cross-referencing the product-stage inference in `SKILL.md`: "The product stage inferred in Step 0 of SKILL.md determines which adopter type is the primary audience. Early majority (Growth stage) wants reliability and onboarding; late majority (Maturity) wants no surprises; innovators (Introduction) want capability and feedback loops."

2. Add a new entry to the Quick map table for Evolve and Sunset if not already present (LDD's table stops at Sunset but may lack the Evolve row's full specialist column).

- [ ] **Step 2: Write `ship-it/references/templates.md`**

Copy `lifecycle-driven-delivery/references/templates.md` verbatim. Then append three new templates at the end:

**Evolution brief template:**

```markdown
## evolution-brief.md

# [Product/Feature] — Evolution Brief

**Status:** Draft | Reviewed   **Date:** <date>

## Current vs. target
| Success signal (from spec) | Target | Actual | Source |
|---|---|---|---|

## Constraint
<One sentence: what is preventing the product from meeting its success signals.
Evidence: where this comes from (metric, incident, user feedback, code pattern).>

## Proposed change
<What would address the constraint. Scope only — no solution design here.>

## Risk if wrong
<What changes if the constraint diagnosis is incorrect.>
```

**Dependent inventory template (Sunset):**

```markdown
## dependent-inventory.md

# Deprecating [thing] — Dependent Inventory and Removal Plan

## Why
<Reason and what replaces it.>

## Dependent inventory
| Dependent | Type | Owner | Migration path | Status |
|---|---|---|---|---|

## Removal sequence
1. Stop new writes — <date>
2. Confirm reads are zero (check logs/metrics) — <date>
3. Archive data — <date>
4. Remove code and routes (not just links) — <date>
5. Drop storage — <date>
6. Revoke credentials and keys — <date>

## Migration guide
<Step-by-step for users of the old thing. Published before Step 4.>

## Retention obligations
<What must be kept, for how long, and why.>

## Verification before removal
<How we confirm nothing still calls it: log query, metric, code search command.>
```

**Note on plan extraction (add to design.md template):**

After the `## Implementation plan` section in the existing `design.md` template, add:

```
<!-- ship-it: before dispatching the plan reviewer, extract this section
     and pass it alongside spec.md only. Do not pass the full design.md. -->
```

- [ ] **Step 3: Read both files as a new reader**

Check: are the new sections clearly separated from the copied content? Do cross-references to `SKILL.md` use paths that will resolve from `references/`? Fix any that don't.

---

## Self-Review

**Spec coverage:**

| Design decision | Covered by task |
|---|---|
| Four tracks (Patch/Quick/Standard/Full) | Task 1 Step 4 |
| Product-stage inference + surfacing | Task 1 Step 3 |
| Tiered adversarial depth per track | Task 1 Steps 4 + 6 |
| Seven stage-specific reviewer prompts | Task 2 Steps 1–7 |
| Plan extraction before plan reviewer | Task 1 Step 6 + Task 2 Step 3 |
| Specialist Reviewer Injection blocks | Task 3 Steps 2–8 |
| project-lenses conditional activation | Task 1 Step 6 (mention in Design stage gate for Standard/Full on user-facing + Introduction/Growth) |
| Evolution brief as Evolve artifact | Task 1 Step 6 + Task 4 Step 2 |
| Dependent inventory as Sunset artifact | Task 1 Step 6 + Task 4 Step 2 |
| Evidence declaration in Discover | Task 1 Step 6 |
| Personas + customer adopter table | Task 4 Step 1 |
| Correctable product-stage inference | Task 1 Step 3 |
| Human gates table | Task 1 Step 7 |
| Convergence rule | Task 1 Step 8 |
| Anti-patterns | Task 1 Step 8 |
| Handoff report | Task 1 Step 8 |
| Backward-compatible specialist files | Task 3 Step 1 |

No gaps found.

**Step scan:** Each step produces one clearly named file section or block. No "handle edge cases" steps. No steps that transcribe the code (this is all Markdown prose authoring).

**Type consistency:** File paths referenced in Task 1 match the files created in Tasks 2–4. `references/reviewers/` and `references/specialists/` are the two subdirectories; no other paths invented.

**Proportion:** Plan is longer than the artifacts it describes, but this is appropriate — the artifacts are prose documents with no code, and the plan must specify exact content decisions (what each reviewer asks, what each injection block contains) that the implementer cannot derive independently.

---

## Execution Handoff

Plan saved to `docs/superpowers/plans/2026-10-18-ship-it-skill.md`.

Tasks 2 and 3 are largely independent (reviewer prompts and specialist files don't depend on each other). Task 1 must complete before Tasks 2–4 since it defines the stage names, track names, and artifact names everything else references. Task 4 depends on Tasks 1–3 being stable (templates reference artifact names from Task 1; personas reference the product-stage inference).

**Which execution approach would you prefer?**

- **Subagent-driven** — A fresh subagent implements each task with an independent reviewer between tasks. Most thorough; higher cost per task.
- **Native** — I implement every task myself in this session, then one reviewer on the whole branch at the end. Fastest; single review at the end.

**I recommend Native**, because the tasks are tightly coupled in content (reviewer prompts reference stage names from SKILL.md; injection blocks reference trigger signals from SKILL.md) and the plan carries those decisions explicitly — a single session handles cross-references more reliably than handoffs between subagents. The final review catches anything that drifted. Does the plan look right to you, and which approach should we use?
