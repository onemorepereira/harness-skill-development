# Stage 3: Design — *UX Designer, Software Architect, Security & Privacy Reviewer*

Decide how, while changes are still cheap.

- **Experience:** Map all states: empty, loading, partial, error, success, no permission, and what the user sees after a failed first attempt. Every state offers a way forward: a link, a form, a retry, or a message that says what to do next. Write real copy. For APIs, CLIs, libraries: naming, defaults, error messages, discoverability. If the Product Designer is active, run its design review before writing `design.md`.
- **Architecture:** Start from existing patterns. For non-trivial choices, sketch two or three options and record the decision and reasoning (ADR format in `references/templates.md`). Cover data model, interfaces, failure modes, observability, migration and rollback, performance, security and privacy.
- **Sources:** every ADR that depends on how a library, API, service, or tool behaves fills its `Sources` line — see `references/grounding.md`. Batch the lookups through `references/delegates/source-lookup.md` on Standard and Full.
- Find the riskiest assumption. If uncertain, plan a spike before committing.
- Write `design.md` (template in `references/templates.md`).

**Gate:** Someone could implement from `design.md` without asking you; every acceptance criterion maps to a component; irreversible decisions are flagged; load-bearing external claims are cited or marked unverified. Reviewer prompt: `references/reviewers/design.md`.

**Persona coverage:** if `superpowers:project-lenses` is available in this session, then on Standard or Full track, for user-facing changes on Introduction or Growth stage products, invoke it after the design reviewer is clean.
