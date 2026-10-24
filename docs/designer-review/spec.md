# Designer review — Spec

**Track:** Standard   **Product stage:** Introduction
**Status:** Built   **Last updated:** 2026-10-24

## Problem
On UI work, an agent following ship-it settles layout and visual direction alone, from text-only state lists. The person driving the session first sees the interface after it is built, when steering it costs a rebuild.

Evidence: a direct maintainer request (2026-10-24, Plane HSD-1). The evidence sweep found no wireframe, mockup, or design-choice step anywhere in ship-it, its predecessor, or the git history. The Design stage's Experience bullet and the `design.md` Experience section are text only (`ship-it/references/stages/design.md:5`, `ship-it/references/templates.md:72-74`).

## Users
The developer or product owner running ship-it on a change with a visual interface (web, mobile, desktop, or terminal UI). They hold product context and taste the agent lacks, and they want to steer the look before code exists.

## Goals
- The user sees and steers the visual direction of UI work before it is built.
- The step costs nothing when it adds nothing: the designer skips it, out loud, when the design is already settled.
- What the user approved is what gets built and verified.

## Non-goals
- No new stage. The review happens inside Design (Standard, Full) or just before Build (Quick).
- No dependency on a specific design tool (Figma, Excalidraw, a particular harness). The skill must run in Claude Code and OpenCode.
- No change to the Full track's existing human sign-off after the Design gate.
- No design review for non-visual surfaces: APIs, SDKs, and CLIs stay with the API Designer / DX specialist.
- No changes to `lifecycle-driven-delivery/` (maintenance mode).
- No pixel-perfect or production-grade mockups: wireframes and mocks are for choosing, not shipping.

## User stories
- As the person driving ship-it on a new screen, I want two or three different layouts to choose from, so that I pick the direction instead of discovering it after the build.
- As that person, I want to steer the chosen direction and approve it, so that the build matches what I had in mind.
- As that person making a copy fix, I want no design stop at all, so that small changes stay fast.

## Acceptance criteria
Each AC is proven by running a scenario agent against the modified skill and inspecting what it would present to the user (see Constraints for the method).

- [x] AC1: Given a Standard-track request for a new screen or flow, when the agent reaches Design, then before writing `design.md` it presents 2–3 layout directions that differ in structure (arrangement, hierarchy, or navigation, not only colour or spacing), each with a one-line trade-off and a recommendation, and asks the user to choose.
- [x] AC2: Given the user picked a direction and gave steering, when the agent continues, then it presents the refined design and asks for explicit approval before writing `design.md`.
- [x] AC3: Given an approved design, when `design.md` is written, then its Experience section records the approved direction (the wireframe inline, or a path to the mock), the alternatives shown, and the user's steering.
- [x] AC4: Given a change that stays within existing screens and components with no new layout decision (copy, spacing, a bug fix restoring the intended look), when the designer evaluates it, then it skips the review, states the skip and its reason in one line with an offer to run it, and continues without stopping.
- [x] AC5: Given the user already supplied a design (mockup, screenshot, or "make it look like X") or said not to review designs, when the designer evaluates it, then it skips, states the reason in one line, and continues without stopping.
- [x] AC6: Given a Quick-track UI change that introduces a new layout decision, when the agent is about to write the `## Plan` section, then it runs the review first and records the approved direction in that section.
- [x] AC7: Given a harness with a choice prompt that supports previews, the wireframes appear as previews in that prompt. Given a harness without one, they appear as numbered ASCII options in chat. The question asked decides the fidelity: layout and structure use ASCII; look and feel uses a rendered mock when the agent can show one.
- [x] AC8: Given three review rounds without approval, when the third ends, then the designer proceeds with the latest direction and records the unresolved points as open questions instead of starting a fourth round.
- [x] AC9: Given an approved design, when a gate reviewer's finding would change it, then the agent brings that finding back to the user rather than overriding the approval silently.
- [x] AC10: Given a change whose only user-facing surface is non-visual (a public API, CLI text output, docs, or copy), when `experience.md` loads for it, then the Product Designer does not activate and no design review happens.
- [x] AC11: Given a run where no human can answer (non-interactive or headless, or ship-it running as a subagent), when the designer would run a review, then it does not assume approval. It stops and hands back: the wireframes and the question are its final output, with a note that the design review needs a human, so whoever dispatched it can relay them.

## Success signals
- In the next UI sessions run with ship-it, every `design.md` or Quick `spec.md` records either an approved direction or a one-line skip reason.
- The maintainer reports fewer "that's not what I pictured" rebuilds after UI work. This is qualitative and recorded in `docs/findings/` when it happens.

## Constraints
- **Contribution rules** (`CONTRIBUTING.md`): smallest change that does the job, keep `SKILL.md` small, Reviewer Injection blocks at 5 questions or fewer, specialist block shape as in `ship-it/references/specialists/experience.md:7-26`.
- **Closed checkpoint list** (`ship-it/SKILL.md:108-121`): a new stop must be added to that table explicitly. It cannot be an unlisted pause.
- **Self-contained Quick reading** (`ship-it/SKILL.md`, Step 3): Quick reads stage files, reviewer prompts, templates, and loaded specialist files only. Everything Quick needs for the review must live in the specialist file.
- **Harness-agnostic:** no instruction may require a tool that only one harness has. Tool-specific affordances are phrased as "if the harness offers X".
- **Test method:** these are instructions, not code. Each AC is checked by dispatching a fresh agent with the skill files and a scenario, telling it to stop at the point where it would talk to the user and output what it would present. The same scenarios run against the unmodified skill first, as a baseline.

## Assumptions and open questions
- Assumption: a "designer subskill" means a specialist in `experience.md`, loaded like the others, not a separate external skill. If wrong, the content moves to its own skill directory and the specialist row points there.
- Assumption: ASCII wireframes are enough for layout choices. If users need visual style, the rendered rung covers it.
- Open: should a skipped review be offered again at Verify when the built UI is first shown? — owner: maintainer / default: no; the skip line already offers the review up front.
