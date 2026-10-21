# Stage 7: Evolve — *Growth PM, Platform Engineer, SRE*

Use this stage when a shipped feature's recorded success signal or metric falls short. A general request to improve existing behavior, with no signal to measure against, skips Evolve and starts at its track's first stage.

- Write an **evolution brief** (template in `references/templates.md`): current metric vs. original success signal, identified constraint and its evidence, proposed change.
- The proposed change re-enters the pipeline at Define. Treat it as a new Standard or Full track request depending on scope.
- When the new Define cycle produces a `spec.md`, mark the previous `spec.md` as `Status: Superseded` and add a `Superseded by:` link to the new one.
- Update the **living sections** of `design.md` (Overview, Architecture, Data and contracts, Failure modes, etc.) to reflect the current system. Do not edit past ADR entries — append new ones instead.

**Gate:** The constraint diagnosis is supported by evidence (not assumption); the proposed change addresses the constraint, not something adjacent. Reviewer prompt: `references/reviewers/evolve.md`.
