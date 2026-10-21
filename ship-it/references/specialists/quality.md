# Quality Specialists

Load for every Standard and Full track. The QA Engineer in `stages/verify.md` owns the Verify stage; these specialists sharpen it and pull quality work earlier.

---

## QA Automation Engineer

**Activate when:** always on Standard and Full tracks, and whenever acceptance criteria exist.

The key move is to start in Define, not Verify: an acceptance criterion you can't automate is usually one you haven't specified precisely enough.

| Stage | Lens |
|---|---|
| Define | Is each acceptance criterion automatable? If not, rewrite it until it is, or state how it will be checked manually. |
| Design | Choose the level for each test (unit, integration, end-to-end), favoring the fastest level that proves the behavior. Plan test data and fixtures. Plan seams for faking external services. |
| Build | Tests alongside code, matching the repo's test framework and conventions. Deterministic tests: no sleeps, no reliance on order or real clocks. |
| Verify | All acceptance criteria covered by tests that run in CI. Check for flakiness by running new tests several times. |
| Evolve | Add a regression test for every bug fixed. |

**Red flags:** tests that pass without the feature (they test nothing); sleeps instead of waiting on conditions; end-to-end tests for logic that a unit test could cover; tests coupled to implementation details.

**Gate evidence:** acceptance-criteria-to-test mapping; tests failing before the change and passing after (for bug fixes).

### Reviewer Injection — QA Automation Engineer

Append to reviewer prompts when this specialist is active.

- Does every acceptance criterion map to a test that runs in CI?
- Do any new tests use sleeps or rely on real clocks (they will be flaky)?
- Do tests fail before the change and pass after (for bug fixes)?

---

## Exploratory Tester

**Activate when:** user-facing changes; complex state; anything the automated tests can't easily reach.

| Stage | Lens |
|---|---|
| Verify | Use it like a curious, impatient user: double-submit, back button, refresh mid-flow, paste odd input, two tabs at once, slow connection. Note what surprised you. |

**Red flags:** only the happy path was ever exercised by hand.

**Gate evidence:** a short list of scenarios tried and their results.

### Reviewer Injection — Exploratory Tester

Append to reviewer prompts when this specialist is active.

- Has the happy path been exercised by a human or simulation that tried to break it (double-submit, back button, paste odd input, two tabs)?

---

## Performance Engineer

**Activate when:** the spec has a performance constraint; the change is on a hot path; data size or traffic will grow; or the change adds loops over collections, N+1 queries, or large payloads.

| Stage | Lens |
|---|---|
| Define | Performance budgets as acceptance criteria (for example, p95 latency, memory, bundle size). |
| Design | Complexity of the main operations; caching; pagination; what grows with data size. |
| Build | Avoid N+1 queries and unbounded loads. Paginate. |
| Verify | Measure against the budget with realistic data. Profile before optimizing. |

**Red flags:** "it's fast on my machine" with ten rows; optimizing without measuring.

**Gate evidence:** a benchmark or measurement against each budget.

### Reviewer Injection — Performance Engineer

Append to reviewer prompts when this specialist is active.

- Are performance budgets from the spec stated as acceptance criteria (p95, memory, bundle size)?
- Has performance been measured with realistic data volume, not ten rows?

---

## Beta Tester (simulated user)

**Activate when:** user-facing features, developer tools, APIs, CLIs, and docs. Most valuable for Introduction-stage products and for anything with onboarding.

An agent can't recruit real beta testers, but it can simulate one honestly: adopt the target user from the spec (see the customer personas in `../personas.md`), set aside everything learned while building, and try to accomplish the user's goal using only what a real user would have — the UI, the docs, the error messages.

| Stage | Lens |
|---|---|
| Discover | If real users or beta feedback exist (issues, tickets, feedback docs), read them first. |
| Verify | Do a "zero-context walkthrough": starting from the README or the entry screen, complete the main user story. Write down every point of confusion, missing step, or unclear message. |
| Launch | Use the walkthrough notes to fix docs and error messages before release. Recommend a real beta or staged rollout to early adopters when the product stage calls for it. |

**Red flags:** setup steps that exist only in the builder's head; error messages that require reading the code.

**Gate evidence:** walkthrough notes and what was fixed as a result.

### Reviewer Injection — Beta Tester

Append to reviewer prompts when this specialist is active.

- Starting only from the README or entry screen, can the main user story be completed without reading the code?
- Are setup steps that exist only in the builder's head documented?
