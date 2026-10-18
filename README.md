# harness-skill-development

Agent skills for OpenCode and Claude Code, developed and maintained by [@mpereira](https://github.com/mpereira). The primary skill is **ship-it** — a full product lifecycle framework with adversarial validation at every stage gate.

> **Status:** active single-maintainer project. Contributions welcome; response time is best-effort.

## What's here

```
ship-it/                        ← Active skill (use this)
├── SKILL.md                    ← Core skill: tracks, stages, gates, anti-patterns
└── references/
    ├── personas.md             ← Core persona checklists and customer persona table
    ├── templates.md            ← Artifact templates (spec, design, launch, etc.)
    ├── reviewers/              ← Stage-specific adversarial reviewer prompts
    │   ├── define.md
    │   ├── design.md
    │   ├── plan.md
    │   ├── verify.md
    │   ├── launch.md
    │   ├── evolve.md
    │   └── sunset.md
    └── specialists/            ← Domain specialist lenses + reviewer injection blocks
        ├── data.md
        ├── security.md
        ├── platform.md
        ├── quality.md
        ├── compliance.md
        ├── experience.md
        └── ai-ml.md

lifecycle-driven-delivery/      ← Predecessor skill, kept for reference — do not edit
docs/superpowers/plans/         ← Implementation plans used during development
```

## ship-it in 60 seconds

`ship-it` takes a request from idea to shipped code through eight stages: **Discover → Define → Design → Build → Verify → Launch → Evolve → Sunset**. Not every stage runs every time — a small UI polish is Quick track (Define → Build → Verify); a new service is Full track (all stages, human checkpoints).

What makes it different from a checklist: at each stage gate, a **reviewer subagent** receives only the artifact — no conversation history — and must find it independently understandable. If it needs explanation, it isn't done. This is the minimal-context test.

**Four tracks:**

| Track | When | Adversarial depth |
|---|---|---|
| Patch | One-liner | None |
| Quick | Small well-understood change | Self-review |
| Standard | New feature in existing product | Subagent on Design + Verify |
| Full | New product, cross-cutting, hard to reverse | Subagent on all stages |

## Installation

### OpenCode

Add this repo as a skills source in `~/.config/opencode/opencode.jsonc`:

```jsonc
{
  "skills": [
    "/path/to/harness-skill-development"
  ]
}
```

OpenCode discovers `SKILL.md` files at any depth, so `ship-it` will be found automatically. The skill ID is `ship-it`.

### Claude Code

Symlink into `~/.claude/skills/`:

```sh
ln -s /path/to/harness-skill-development/ship-it ~/.claude/skills/ship-it
```

### Invoking the skill

The model will not always fire `ship-it` automatically (it competes with other broad process skills). Invoke it explicitly:

```
@ship-it
```

or in Claude Code:

```
use @ship-it throughout
```

## The predecessor skills

`ship-it` was designed by combining two earlier skills from the [superpowers](https://github.com/superpowers) skill tree:

- **`design-to-execution`** — a four-stage adversarial pipeline (Design → Spec → Plan → Execute). Strong validation mechanism, no lifecycle awareness, no specialist coverage, ends at code review.
- **`lifecycle-driven-delivery`** — an eight-stage lifecycle framework with deep specialist files. No adversarial gates; self-assessed checklists only.

`ship-it` combines the adversarial gate mechanism from `design-to-execution` with the lifecycle breadth and specialist depth from `lifecycle-driven-delivery`. A copy of `lifecycle-driven-delivery` is kept in this repo as the design reference; it is not actively maintained here.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).
