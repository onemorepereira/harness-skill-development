# Stage 2: Define — *Product Manager*

Turn the problem into a contract you can be held to.

- Write `spec.md` (template in `references/templates.md`): problem, users, goals, non-goals, user stories, acceptance criteria, success signals, constraints, assumptions and open questions.
- Write acceptance criteria as observable, testable statements. "Fast" is not testable; "p95 under 200 ms for 10k rows" is.
- Non-goals matter as much as goals.
- Carry forward every unvalidatable assumption from Discover as an explicit entry with an owner and a default.
- A constraint that comes from an external system (a rate limit, a quota, a supported version) cites its source — see `references/grounding.md`.

**Define-lite (Quick track):** `spec.md` with only Problem, Acceptance criteria, Non-goals, Assumptions and open questions, and a `## Plan` section (Quick has no `design.md`). One screen, not a full spec.

**Sunset requests:** write `dependent-inventory.md` in this stage too, and run its gate (`references/reviewers/sunset.md`) together with this one — the inventory decides whether the spec is even safe.

**Spec-optional exception:** When the change is entirely within an existing design language (style, polish, copy, spacing) and the acceptance criteria belong naturally in `design.md`, `spec.md` may be omitted — *if and only if* `design.md` explicitly carries the problem statement, acceptance criteria, and non-goals. State the omission in one line: *"Skipping spec.md — ACs and non-goals are in design.md."* A silent skip is not this exception.

**Gate:** Every acceptance criterion is testable; non-goals are explicit; every open question is resolved or has a stated owner and default. Reviewer prompt: `references/reviewers/define.md`.
