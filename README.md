# harness-skill-development

Agent skills for OpenCode and Claude Code, developed and maintained by [@onemorepereira](https://github.com/onemorepereira). The primary skill is **ship-it** — a full product lifecycle framework with adversarial validation at every stage gate.

> **Status:** active single-maintainer project. Contributions welcome; response time is best-effort.

## What's here

```
ship-it/                        ← Active skill (use this)
├── SKILL.md                    ← Index: grounding, tracks, stage map, gate depth, delegation, anti-patterns
└── references/
    ├── grounding.md            ← Authoritative-source rules (applies on every track)
    ├── product-stages.md       ← What each product stage optimizes for
    ├── personas.md             ← Core persona checklists and customer persona table
    ├── templates.md            ← Artifact templates (spec, design, launch, handoff, etc.)
    ├── stages/                 ← Full instructions per stage, read on entering it
    │   ├── discover.md  define.md  design.md  build.md
    │   └── verify.md  launch.md  evolve.md  sunset.md
    ├── delegates/              ← Subagent prompts with fixed return shapes
    │   ├── evidence-sweep.md
    │   ├── source-lookup.md
    │   ├── security-scan.md
    │   └── verify-run.md
    ├── reviewers/              ← Stage-specific adversarial reviewer prompts
    │   ├── README.md           ← How to run a subagent or self review
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

On UI work, a Product Designer specialist shows you wireframes (or a rendered mock for look and feel) to choose from and approve before anything is built, unless it judges the design already settled. Say "skip design review" or "show me wireframes" to overrule it.

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
