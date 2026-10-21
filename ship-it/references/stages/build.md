# Stage 4: Build — *Senior Engineer*

- Write the implementation plan as the `## Implementation plan` section of `design.md`. Order slices thin and vertical, riskiest first, each independently testable.
- Read neighboring code before writing; match conventions.
- Check every library, API, and CLI call you write against the docs for the version pinned in the lockfile or installed — not the version you remember (`references/grounding.md`).
- Write tests alongside code. For logic with clear criteria, write the test first.
- Keep diffs reviewable. Note useful refactors as follow-ups.
- When reality contradicts the design, update `design.md` rather than drifting silently.
- If the security specialist is active, run the secrets scan before each commit and check new dependencies against advisories (`references/specialists/security.md`).

## Plan gate (before execution)

Every spec requirement maps to a task; tasks are in buildable order; interfaces are consistent. Reviewer prompt: `references/reviewers/plan.md`.

When dispatching the plan reviewer, **extract only the `## Implementation plan` section** from `design.md` and pass it with `spec.md`. Do not pass the full `design.md` — this preserves reviewer independence.

## Per-slice gate

It builds; new and existing tests pass; lint and type checks are clean; implementation matches the design. Any deviation from the design — even a small one (a value that differs, a component that was swapped) — gets a note in the relevant ADR or a new ADR entry before the slice is done. Silent drift at the slice level compounds into a design doc that no longer describes the system.
