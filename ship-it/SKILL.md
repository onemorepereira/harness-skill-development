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

Skip on Patch. From repo signals, infer Introduction, Growth, Maturity, or Decline; **read `references/product-stages.md`** for what each optimizes for. State it in one line: *"Reading this as a [stage]-stage product ([key signals]). Correct me if that's wrong — it changes what I optimize for."* Confirm with the user if the track is Full or the stage is Decline.

## Step 1: Pick a track

| Track | Use when | Stages |
|---|---|---|
| **Patch** | One-liner fix, no design needed | None — state the change and do it |
| **Quick** | Small, well-understood change in a clear area | Define-lite → Build → Verify |
| **Standard** | A new feature or meaningful change in an existing product | Discover → Define → Design → Build → Verify → Launch |
| **Full** | New product or service; cross-cutting change; anything a code revert can't undo: data migration or deletion, removing a schema element, endpoint, or field clients may rely on, auth model, billing | All that apply, with human sign-off after Define and Design |

**The revert test:** if reverting the commits fully restores the previous behavior and data, it is not Full — even when it changes behavior callers see (status codes, error bodies, validation). New tables, nullable columns, and endpoints are Standard. Full is for what a revert can't undo.

Evolve is for a shipped feature whose recorded success signal or metric falls short; it writes an evolution brief, then re-enters at Define. A request to improve existing behavior with no such signal ("make error handling better") is not Evolve: pick the track by the revert test and start at that track's first stage. Sunset (removing something) runs alongside Define: write the dependent inventory during Define and gate both together; the removal steps then go through Build, Verify, and Launch. State the track and why in one line. When unsure, take the bigger track for the *thinking* and the smaller one for the *paperwork*.

## Step 2: Load the specialists this change needs

Scan the request, the code you'll touch, and the spec (once written). Load only what matches.

| If the change touches… | Load | Specialists inside |
|---|---|---|
| Schemas, migrations, data stores, pipelines, backfills, analytics events, metrics | `references/specialists/data.md` | Data Engineer/DBA, Analytics Engineer |
| Auth, permissions, user input, secrets, uploads, new dependencies, personal data; attack-surface changes (new endpoints, public exposure, tenant boundaries) | `references/specialists/security.md` | Application Security Engineer, Privacy Engineer, Offensive Security Engineer |
| Infrastructure, deploy pipelines, CI/CD, config, scaling, reliability, cloud cost | `references/specialists/platform.md` | Infrastructure Engineer, DevOps/Platform Engineer, SRE, FinOps |
| Any Standard or Full track; performance budgets; onboarding or developer tools | `references/specialists/quality.md` | QA Automation Engineer, Exploratory Tester, Performance Engineer, Beta Tester |
| Regulated data or frameworks (SOC 2, HIPAA, PCI, GDPR…), audit logs, retention | `references/specialists/compliance.md` | Compliance Officer |
| UI, mobile, public APIs/SDKs/CLIs, docs, user-facing copy, multiple locales | `references/specialists/experience.md` | Product Designer, Frontend, Mobile, Accessibility, API Designer/DX, Technical Writer, Localization |
| Model calls, prompts, agents, retrieval, embeddings, ML features | `references/specialists/ai-ml.md` | ML/AI Engineer, AI Safety Reviewer |

State which specialists you loaded in one line so the human can add or remove one. Each specialist's `Reviewer Injection` blocks are appended to gate reviews (see `references/reviewers/README.md`).

---

## Step 3: Move through the stages

Open each file at the moment you need it, never up front. The rows below are summaries, not the instructions.

| Track | Read |
|---|---|
| Patch | Nothing beyond this file |
| Quick | The stage file on entering each stage; that stage's reviewer prompt at its self-review gate; `references/templates.md` when writing `spec.md`. Not `delegates/` or `reviewers/README.md` — everything Quick needs from them is in this file |
| Standard, Full | The stage file on entering each stage; `references/reviewers/README.md` before the first subagent gate; a delegate prompt only when about to dispatch it; templates and personas when writing the artifact that uses them |

**Artifacts live in `docs/<feature-slug>/`** in the repo: `spec.md`, `design.md`, `launch.md`, `dependent-inventory.md`, `handoff.md` — whichever the track produces.

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

Discover has no gate review; Patch has none at all. **Self** review: answer the stage's reviewer prompt questions yourself, explicitly, and state what you found. **Subagent** review: a fresh reviewer gets only the artifact and the prompt — the mechanics are in `references/reviewers/README.md`; read it before the first subagent gate.

**Convergence:** advance when a pass returns no blocking issues; minor findings never block. A finding that recurs after your fix means the fix was wrong — diagnose it before looping again. **At most 3 subagent passes per gate**; a dispatch that failed or lacked the artifact doesn't count. If the third still has blockers, stop and bring them to the human with the reviewer's findings and your proposed resolution for each.

### Delegate heavy work

On Standard and Full, hand these to a subagent using the prompt file, and keep only what it returns; with no subagent tool, follow the prompt file yourself. On Patch and Quick, do the work inline without opening the prompt files: check the versions you rely on, run the available scanners, run the full suite, and run the change once the way it will actually run.

| Work | When | Prompt | Tier |
|---|---|---|---|
| Evidence sweep | Discover | `references/delegates/evidence-sweep.md` | lighter |
| Source lookup | Claims need grounding — batch them per stage | `references/delegates/source-lookup.md` | lighter |
| Security scan | Security specialist active; Build and Verify | `references/delegates/security-scan.md` | same |
| Pentest probe | Offensive Security Engineer active; Verify | `references/delegates/pentest-probe.md` | same |
| Verify run | Verify | `references/delegates/verify-run.md` | lighter |

**Tier:** where the harness lets you set model or effort per dispatch, send *lighter* delegates one step down (lower effort, or a smaller model); they gather and report against a fixed return shape, so judgment isn't what they're paying for. *Same* runs at your own level — the scan triages false positives and the probe attacks, and a wrong call there costs more than the tokens. With no such control, ignore the column. Gate reviewer tiers are in `references/reviewers/README.md`.

Budget: one evidence sweep per request; one source-lookup batch per stage (check later stragglers inline); one verify run, one security scan, and one pentest probe per Verify pass. Delegates gather and run; they don't decide. Gate verdicts and claims of done stay with you, based on the evidence they return.

---

## Human checkpoints

Stop and ask the human at these points only:

| When | What to ask |
|---|---|
| Track or product stage ambiguous | State inference; ask to confirm or correct |
| After Define gate (Full track) | Questions only they can resolve: scope, trade-offs, open questions from Discover |
| After Design gate (Full track) | Same; plus any irreversible architectural decision |
| A gate still has blockers after 3 subagent passes | The blockers, the reviewer's reasoning, and your proposed resolution for each |
| Product Designer runs a design review (UI work, any track but Patch), or a gate finding would change a design the user approved, supplied, or steered | Directions to choose from, then the refined design to approve; for a conflicting finding, the finding and your proposed resolution |
| Before probing a target ship-it didn't start itself, or any active heavy-tier use (Offensive Security Engineer) | The one-line scope note — target, tests, destructive tests excluded — for the user's OK |
| Accepting a Critical or High security finding under extenuating circumstances | The finding, its evidence, the circumstances, and compensating controls; only the user can accept it |
| Before any irreversible action (any track) | Explicit confirmation regardless of track |
| After execution | Final summary: what shipped, rulings made, deferred minors |

Do not stop between stages for progress updates. Do not ask "should I continue?" — that is not a decision requiring human intent.

## Handoff

On Quick, Standard, and Full, finish by writing `docs/<feature-slug>/handoff.md` from the template in `references/templates.md` and summarizing it in chat. On Patch, a one- or two-line summary in chat is the handoff.

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
| Human as test harness | Tests fake the project's own wiring, so the human finds the bugs by trying the real thing, one round trip at a time. Run the real path yourself before handing anything over. |
| Building from memory | Recalled APIs, flags, defaults, and limits are often for a different version. Check the source and cite it. |
| Working from the index | Following a stage from its one-line summary here instead of reading its file. |
