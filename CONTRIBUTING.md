# Contributing to harness-skill-development

This repo contains agent skills — instruction documents read by LLM agents at runtime. The quality bar is different from code: the test is whether an agent following the instructions produces better outcomes, not whether the files parse correctly.

This is a single-maintainer personal project. Contributions are welcome; response time is best-effort.

## What's worth contributing

- **Bug reports from real sessions** — the most valuable input. If a skill fired wrong, produced a bad artifact, skipped a stage, or missed an obvious problem, that's a concrete finding. Document it in a file under `docs/findings/` with the session date and what went wrong, then open a PR.
- **Reviewer prompt improvements** — the prompts in `ship-it/references/reviewers/` are the load-bearing pieces. If a reviewer missed something it should have caught, that's a fixable gap.
- **Specialist Reviewer Injection block additions** — each specialist file has a compact injection block (≤5 questions) appended to reviewer prompts. If a domain-specific red flag is consistently missed, add it here.
- **New skills** — a new skill directory at the repo root with its own `SKILL.md` and `references/` structure.

## What to avoid

- **Expanding the skill for its own sake** — every addition has a cost: more to read, more to follow, more to go wrong. A skill that is longer is not a skill that is better.
- **Adding process that doesn't come from evidence** — if a stage or check hasn't been validated by a real session going wrong without it, it's speculation. Speculation adds weight without adding value.
- **Changing `lifecycle-driven-delivery/`** — that skill is in maintenance mode, kept for reference only. Improvements go into `ship-it`.

## Structure of a skill

```
skill-name/
├── SKILL.md          ← Required. Frontmatter with name + description, then instructions.
└── references/       ← Optional supporting files the skill reads when loaded.
```

The `description:` frontmatter field controls when the model offers the skill. Write it as trigger patterns — phrases and request types the model should match — not as a description of what the skill does.

### ship-it file roles

| File | Role | Edit when |
|---|---|---|
| `SKILL.md` | Index: grounding rule, tracks, stage map, gate depth, delegation, anti-patterns. Loaded every time — keep it small | Track, gate depth, or routing changes |
| `references/stages/*.md` | Full instructions for one stage, read on entering it | Stage behavior needs to change |
| `references/delegates/*.md` | Subagent prompts for heavy work, each with a fixed return shape | A delegate returns the wrong shape or misses something |
| `references/grounding.md` | Authoritative-source rules | Grounding behavior needs to change |
| `references/personas.md` | Core persona checklists and customer persona table | A persona lens needs updating |
| `references/templates.md` | Artifact templates (spec.md, design.md, launch.md, etc.) | A template is missing a field or has a wrong default |
| `references/reviewers/*.md` | Adversarial reviewer prompts, one per stage | A reviewer misses something or asks the wrong questions |
| `references/specialists/*.md` | Domain specialist lenses + Reviewer Injection blocks | A domain-specific red flag is missed; a new domain is needed |
| `references/security-tools.md` | Shared tool allowlist (install rules per tier) + read-only vulnerability data feeds, cited by the security specialist and its delegates | A tool is added or removed; an install or triage rule changes |

## Making a change

1. **Start from evidence.** What real session produced the finding? What did the agent do that it shouldn't have, or fail to do that it should have? State this in the PR description.

2. **Make the smallest change that fixes the finding.** A targeted edit to one reviewer prompt is better than a restructure of the stage it belongs to.

3. **Check cross-references.** `SKILL.md` references specific files in `references/`. If you rename or move a file, update every reference to it.

4. **Test it.** Run a session that would have triggered the original problem and verify the new instructions produce the right behavior. Describe what you tested in the PR.

5. **Commit message convention:** `type(scope): description`
   - Types: `feat`, `fix`, `style`, `docs`, `chore`
   - Scopes: `ship-it`, `reviewers`, `specialists`, `personas`, `templates`
   - Examples: `fix(reviewers): verify prompt now checks commit message accuracy`, `feat(specialists): add FinOps injection block`

## Adding a new skill

1. Create a directory at the repo root: `your-skill-name/`
2. Add `SKILL.md` with frontmatter (`name:`, `description:`) and instructions
3. Add `references/` subdirectory with supporting files if needed
4. Update `README.md` to list the new skill

The skill ID is derived from the directory name. Keep it lowercase kebab-case.

## Reviewer Injection block format

Each specialist sub-specialist block ends with a `### Reviewer Injection` section. Rules:

- **Cap at 5 questions per sub-specialist.** More dilutes focus.
- **Each question targets a specific failure mode**, not general good practice.
- **Frame as yes/no or presence checks**, not open-ended prompts. The reviewer is looking for red flags, not conducting a design review.
- **Use signal words that match the specialist's trigger signals** in `SKILL.md`'s specialist loading table.

Example:
```markdown
### Reviewer Injection — Application Security Engineer
Append to reviewer prompts when this specialist is active.

- Where is authorization enforced, and is it server-side (not UI-only)?
- What input is untrusted, and where does it flow — database, shell, template, URL fetch?
- Are secrets and tokens absent from logs, URLs, and error messages?
- Are the "must be denied" paths present as acceptance criteria?
- Have new dependencies been pinned and reviewed?
```
