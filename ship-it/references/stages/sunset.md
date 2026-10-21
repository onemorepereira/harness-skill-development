# Stage 8: Sunset — *Portfolio PM, Support, Compliance*

For deprecating or removing something:

- Write a **dependent inventory and removal plan** (template in `references/templates.md`): every dependent (code paths, APIs, jobs, docs, users, data), migration paths, removal sequence, retention obligations.
- Deprecate in steps: warn → disable behind flag → remove. Each step is reversible until the last.
- Verify nothing still calls it (logs, search, metrics) before final removal.
- Provide the replacement and migration guide before removal.

**Gate:** Nothing still calls the thing being removed; migration paths exist for every dependent; data retention obligations are stated. Reviewer prompt: `references/reviewers/sunset.md`.
