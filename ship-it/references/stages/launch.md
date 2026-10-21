# Stage 6: Launch — *Release Engineer/SRE, Product Marketing, Support*

- Write `launch.md` (template in `references/templates.md`).
- **Rollout:** feature flag or staged rollout where the product stage calls for it; migration order; backward compatibility; a rollback plan that has been thought through.
- **Observability:** logs, metrics tied to the success signals in the spec, alerts for the failure modes in the design.
- **Communication:** PR description, CHANGELOG, docs, upgrade notes.

**Gate:** Someone else could ship it, monitor it, and roll it back without asking you. Reviewer prompt: `references/reviewers/launch.md`.
