# Product stages

Infer the stage from repo signals: age and activity, versioning, test coverage, CHANGELOG, deprecation markers, README tone, issue volume.

| Product stage | Optimize for | Watch out for |
|---|---|---|
| **Introduction** (new, pre-PMF) | Learning speed, simplest working thing, instrumentation to learn from users | Premature abstraction, platform-building, gold-plating |
| **Growth** (adoption rising) | Scalability, reliability, onboarding, extensibility, performance budgets | Debt that blocks scaling; one-off hacks that will multiply |
| **Maturity** (stable, many users) | Backward compatibility, consistency with conventions, small reversible changes, zero-downtime migrations, cost | Breaking changes, novel patterns that fragment the codebase |
| **Decline / Sunset** | Minimal safe change, migration paths, deprecation notices, data export | Investing in features nobody will use; stranding users |

The stage also sets the primary adopter audience — see the adoption section of `references/personas.md`.
