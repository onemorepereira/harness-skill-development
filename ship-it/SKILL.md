---
name: ship-it
description: Use when the user asks to build, design, plan, ship, refine, improve, polish, or tweak anything non-trivial: a feature, fix, tool, service, app, migration, deprecation, UI, or "v2 of X". Also when they say "plan this out", "take this end to end", "write a spec / PRD / design doc / technical design", "how should I approach this", or ask for an implementation plan before coding. Skip for one-liners, pure questions, and explicit "just write the code" requests.
---

# Ship It

Most weak agent output comes from two failures: skipped stages (code written before the problem is understood) and unverified gates (the agent grades its own work). **Stages** fix the first: each has its own order of work and expert lens, and errors are cheapest to catch in the stage that owns them. **Adversarial gates** fix the second: each artifact goes to a reviewer who has seen only the document. If it needs explanation, it isn't done. **Tracks** size the process to the work — a three-page spec for a typo fix is its own failure.

This file is the index. Detailed instructions live in `references/`; read each file at the point named below, not before.

---

## Ground everything

Training knowledge is a hypothesis, not evidence. Before a claim about a library, API, service, tool, vulnerability, or standard shapes a spec, design, fix, or diagnosis, verify it against an authoritative source for the version in use, and cite it. If no source is reachable, record the claim as an assumption and prove it empirically before relying on it. This applies on every track, including Patch. **Read `references/grounding.md`** the first time a claim needs a source.

---

## Step 0: Infer the product stage

From repo signals, infer Introduction, Growth, Maturity, or Decline; **read `references/product-stages.md`** for what each optimizes for. State it in one line: *"Reading this as a [stage]-stage product ([key signals]). Correct me if that's wrong — it changes what I optimize for."* Confirm with the user if the track is Full or the stage is Decline.

## Step 1: Pick a track

| Track | Use when | Stages |
|---|---|---|
| **Patch** | One-liner fix, no design needed | None — state the change and do it |
| **Quick** | Small, well-understood change in a clear area | Define-lite → Build → Verify |
| **Standard** | A new feature or meaningful change in an existing product | Discover → Define → Design → Build → Verify → Launch |
| **Full** | New product or service; cross-cutting change; anything hard to reverse (schema, public API, auth, billing, data deletion); migration | All that apply, with human sign-off after Define and Design |

Evolve (improving something shipped) and Sunset (removing something) run on whichever track needs them. State the track and why in one line. When unsure, take the bigger track for the *thinking* and the smaller one for the *paperwork*.

## Step 2: Load the specialists this change needs

Scan the request, the code you'll touch, and the spec (once written). Load only what matches.

| If the change touches… | Load | Specialists inside |
|---|---|---|
| Schemas, migrations, data stores, pipelines, backfills, analytics events, metrics | `references/specialists/data.md` | Data Engineer/DBA, Analytics Engineer |
| Auth, permissions, user input, secrets, uploads, new dependencies, personal data | `references/specialists/security.md` | Application Security Engineer, Privacy Engineer |
| Infrastructure, deploy pipelines, CI/CD, config, scaling, reliability, cloud cost | `references/specialists/platform.md` | Infrastructure Engineer, DevOps/Platform Engineer, SRE, FinOps |
| Any Standard or Full track; performance budgets; onboarding or developer tools | `references/specialists/quality.md` | QA Automation Engineer, Exploratory Tester, Performance Engineer, Beta Tester |
| Regulated data or frameworks (SOC 2, HIPAA, PCI, GDPR…), audit logs, retention | `references/specialists/compliance.md` | Compliance Officer |
| UI, mobile, public APIs/SDKs/CLIs, docs, user-facing copy, multiple locales | `references/specialists/experience.md` | Frontend, Mobile, Accessibility, API Designer/DX, Technical Writer, Localization |
| Model calls, prompts, agents, retrieval, embeddings, ML features | `references/specialists/ai-ml.md` | ML/AI Engineer, AI Safety Reviewer |

State which specialists you loaded in one line so the human can add or remove one. Each specialist's `Reviewer Injection` blocks are appended to gate reviews (see `references/reviewers/README.md`).

---

## Step 3: Move through the stages

**Read the stage's file before starting the stage.** The row below is a summary, not the instructions. Persona checklists: `references/personas.md`. Artifact templates: `references/templates.md`.

| Stage | Lens | Produces | Gate in one line | Read |
|---|---|---|---|---|
| 1. Discover | PM, UX Researcher | Evidence summary | Problem in one sentence, no solution, and who has it | `references/stages/discover.md` |
| 2. Define | PM | `spec.md` | ACs testable; non-goals explicit; open questions owned | `references/stages/define.md` |
| 3. Design | UX, Architect, Security | `design.md` | Implementable without asking; ACs mapped; one-way doors flagged | `references/stages/design.md` |
| 4. Build | Senior Engineer | Plan + code + tests | Plan covers spec; each slice green and matches design | `references/stages/build.md` |
| 5. Verify | QA, Independent Reviewer | Recorded proof per AC | Every AC proven by something run, and recorded | `references/stages/verify.md` |
| 6. Launch | Release/SRE, Marketing, Support | `launch.md` | Someone else could ship, monitor, and roll back | `references/stages/launch.md` |
| 7. Evolve | Growth PM, Platform, SRE | Evolution brief | Constraint proven by evidence; change targets it | `references/stages/evolve.md` |
| 8. Sunset | Portfolio PM, Support, Compliance | Dependent inventory | Nothing still calls it; migrations exist; retention stated | `references/stages/sunset.md` |

### Gate depth by track

| Gate | Quick | Standard | Full |
|---|---|---|---|
| Define | self | self | subagent + human sign-off |
| Design | — | subagent | subagent + human sign-off |
| Plan (start of Build) | self | self | subagent |
| Verify | self | subagent | subagent |
| Launch | — | self | subagent |
| Evolve, Sunset | subagent | subagent | subagent |

Discover has no gate review; Patch has none at all. **Subagent** and **self** review are defined in `references/reviewers/README.md` — read it before the first gate.

**Convergence:** advance when no blocking issues remain. Minor findings never block; two consecutive passes with only minor or advisory findings is convergence. A finding that recurs after your fix means the fix was wrong — diagnose it before looping again.

### Delegate heavy work

On Standard and Full, hand these to a subagent using the prompt file, and keep only what it returns. On Patch and Quick — or when the harness has no subagent tool — do the same work inline, following the same prompt.

| Work | When | Prompt |
|---|---|---|
| Evidence sweep | Discover | `references/delegates/evidence-sweep.md` |
| Source lookup | Claims need grounding — batch them per stage | `references/delegates/source-lookup.md` |
| Security scan | Security specialist active; Build and Verify | `references/delegates/security-scan.md` |
| Verify run | Verify | `references/delegates/verify-run.md` |

Delegates gather and run; they don't decide. Gate verdicts and claims of done stay with you, based on the evidence they return.

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

## Handoff

Finish with the handoff report in `references/templates.md`.

---

## Anti-patterns

| Pattern | Why it fails |
|---|---|
| Persona theater | Narrating "As a Product Manager, I think…". Apply the lens silently; show its results. |
| Designing in a vacuum | A clean architecture that ignores how the existing system works. |
| Gold-plating | Building beyond the acceptance criteria. Note ideas as follow-ups. |
| Silent drift | The implementation diverges from the spec and nobody is told. |
| Spec that restates the design | The spec must be independently buildable. If a developer can't build from it without the design, it isn't done. |
| Skipping or self-substituting the red-team | Same author, same blind spots. Where the table says subagent, another read of your own work does not replace it. |
| Building from memory | Recalled APIs, flags, defaults, and limits are often for a different version. Check the source and cite it. |
| Working from the index | Following a stage from its one-line summary here instead of reading its file. |
