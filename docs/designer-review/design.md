# Designer review — Design

**Spec:** ./spec.md   **Last updated:** 2026-10-24
**Status:** Current

## Overview
Add a **Product Designer** specialist to `ship-it/references/specialists/experience.md`. It activates only for visual interfaces. Before UI layout is committed to, it decides whether a design review earns its cost. That point is in Design on Standard and Full, before `design.md` is written. On Quick it is in Define-lite, before `## Plan` is written. If the review runs, the designer shows the user 2–3 structurally different wireframes, takes their pick and steering, and gets explicit approval on the refined design. If it doesn't, the designer states the skip in one line and continues. The approved design is recorded and becomes the reference that Build implements, gate reviewers protect, and Verify checks against. A new row in the `SKILL.md` checkpoint table makes the stop legitimate. All behavior lives in the specialist file, so Quick needs no extra reading.

## Experience
The "user" of this change is the person driving a ship-it session.

**Round 1, diverge.** One choice prompt (or chat message) with 2–3 options that differ in structure (arrangement, hierarchy, or navigation), not only colour or spacing. Each option has a short label, a one-line trade-off, and a wireframe. The recommended option comes first. Example wireframe at phone width:

```
┌──────────────────────────┐
│ ← Notifications          │
├──────────────────────────┤
│ Email                    │
│  Product updates    [on] │
│  Weekly digest     [off] │
│ Push                     │
│  Mentions           [on] │
├──────────────────────────┤
│ [ Pause all for 1 day ]  │
└──────────────────────────┘
```

**Round 2, refine.** The chosen option with the user's steering applied, the changes called out in one line, and one question: approve, or what should change. Approval must be explicit ("yes", "approve", "go", or equivalent).

**Round counting.** A round is one presentation of designs to the user. "Show me other options" starts a new round. A clarifying question about an ambiguous reply is not a round. The cap is 3: after the third presentation without approval, the designer proceeds with the latest version. It records that version as "proceeding unapproved", with the open points, in the Design review entry.

**Skip line.** One line, no stop, in this shape:
*"Skipping design review: <reason>. Say so if you want wireframes first."*

**No one to answer.** The run is headless, or another agent dispatched it and didn't say it relays to the user. The designer does not assume approval. It stops and hands back, with the wireframes and the question as its final output (user's ruling, 2026-10-24). Scenario prompts state that the user is reachable, so they still exercise the review.

**Fidelity per question.** Structure, hierarchy, and flow questions use ASCII wireframes. Look-and-feel questions (colour, type, density, visual style) use a rendered mock: one self-contained HTML file in `docs/<feature-slug>/mockups/` that reuses the project's design tokens and mirrors its components' markup, with placeholder data. Real framework components are not required, since a standalone file can't run them. Show it locally where possible (the project's dev server, opening the file, a screenshot). Use a hosted preview page only if it is private to the user. If a rendered mock can't be shown, say so and use ASCII. Mixing is fine: ASCII to pick the structure, rendered for the final approval.

**Prompt mechanics.**
- Choice prompt with previews: the wireframes go in the previews.
- Choice prompt without previews: put the wireframes in the message just before the prompt.
- Plain chat: numbered options, each wireframe in a code block.
- At most three questions per round.

## Architecture
These are all edits to existing markdown, following the specialist shape in `experience.md:7-26`. The exact new text is fixed here; wording may be tightened during Build without changing meaning.

| File | Change |
|---|---|
| `specialists/experience.md` | A new `## Product Designer` section, placed first. Its content is below. |
| `SKILL.md` | Specialist table, experience row: insert "Product Designer" first in the "Specialists inside" list. Checkpoint table: add the row below. |
| `stages/design.md` | Experience bullet, appended: "If the Product Designer is active, run its design review before writing `design.md`." |
| `stages/define.md` | Define-lite line, appended: "If the Product Designer is active, run its design review before writing `## Plan`." (`## Plan` is written here, `define.md:11`; a hook in `build.md` would come too late.) |
| `stages/verify.md` | UI bullet becomes: "For UI: render and inspect every state, confirm each one offers a way forward, and if a design was reviewed or supplied by the user, compare against it and note differences in the handoff." |
| `templates.md` | `design.md` Experience placeholder gains a line: "Design review: approved direction (wireframe inline or mockup path), alternatives shown, user's steering — or 'proceeding unapproved' with the open points, or the skip line." |
| `personas.md` | Quick-map Design row, specialists column: add "Product Designer (layout review with the user)". |

**Checkpoint row (`SKILL.md`):**

| When | What to ask |
|---|---|
| Product Designer runs a design review (UI work, any track but Patch), or a gate finding would change a design the user approved, supplied, or steered | Directions to choose from, then the refined design to approve; for a conflicting finding, the finding and a proposed resolution |

**Product Designer section (`experience.md`), exact text:** see `./product-designer-section.md`. That file holds the section verbatim as it will be inserted, so every rule the running agent needs is in the specialist file. It covers: activation; the stage table (when); Decide (run, skip, partial, no one to answer); Review (rounds, round counting, fidelity, presenting); Record (approved, proceeding unapproved, skipped); Hold the reviewed design; red flags; gate evidence; and the Reviewer Injection.

**Shape exception:** the section is about 65 lines, longer than the usual specialist shape (`experience.md:7-26`). This is deliberate: Quick's self-contained-reading rule means the whole procedure must live in this file.

## Decisions
### ADR-1: Where the designer lives
- **Context:** The request names a "designer subskill" that decides whether to skip. ship-it's extension points are specialists loaded by signal, and external skills invoked conditionally (`stages/design.md:13`).
- **Options:** A) A new specialist in `experience.md`. B) A new file `specialists/design.md`. C) A separate skill.
- **Decision:** A.
- **Why:** UI work already loads `experience.md`, so no new routing is needed and Quick's self-contained-reading rule holds. B adds a file and a table row for no gain. C depends on a skill that may not be installed.
- **Reversibility:** easy
- **Sources:** codebase (`ship-it/SKILL.md` specialist table; `CONTRIBUTING.md` file roles).

### ADR-2: Presentation medium
- **Context:** The skill runs in Claude Code and OpenCode, and their tools differ.
- **Options:** A) ASCII only. B) Rendered only. C) A fidelity ladder chosen per question, with a plain-chat fallback.
- **Decision:** C (the user's choice in Discover).
- **Why:** ASCII is fast and universal for structure; look and feel needs a rendered mock; the fallback keeps every harness working.
- **Reversibility:** easy
- **Sources:** Claude Code's AskUserQuestion tool schema, as loaded in a Claude Code session on 2026-10-24, documents an optional per-option `preview` shown side by side for single-select questions. Not load-bearing: the instructions only say "if the harness offers previews". OpenCode support is unverified (assumption 1).

### ADR-3: Making the stop legitimate
- **Context:** `SKILL.md:110` allows only listed stops; `:121` forbids stopping between stages.
- **Options:** A) A new checkpoint row covering the review and approval conflicts. B) Fold it into the Full-only Design sign-off.
- **Decision:** A.
- **Why:** B would exclude Quick and Standard, which the user chose. The review happens inside a stage, so `:121` still holds.
- **Reversibility:** easy

### ADR-4: Quick timing
- **Context:** Quick has no Design stage. Its `## Plan` is part of the Define-lite `spec.md`.
- **Options:** A) In `build.md`, before writing `## Plan`. B) In `define.md` Define-lite, before writing `## Plan`. C) Before the first UI slice.
- **Decision:** B.
- **Why:** `define.md:11` puts `## Plan` in the Define-lite `spec.md`, so a Quick agent writes the plan before it reads `build.md`. A would fire too late, and C would approve the plan before the design.
- **Reversibility:** easy

## Failure modes
| Failure | Detection | Behavior | Recovery |
|---|---|---|---|
| Review on a trivial change | S3 | A wasted round trip | Skip criteria: no new layout decision |
| Silent skip | Injection Q1 | No record | Gate finding until a line is recorded |
| Options differ only cosmetically | S1 inspection; red flag | The choice is meaningless | The "differ in structure" rule |
| Rendered mock can't be shown | The agent can't open or host it | Says so, uses ASCII | Fidelity fallback |
| Endless steering | Round counter | Proceeds after round 3 | Open points go in the Design review entry |
| A reviewer overrides the approval | Injection Q3 | The built UI differs from the approval | Finding goes back to the user |
| Nobody can answer | Headless, or dispatched without a relay | Must not fake approval | Stops and hands back the wireframes |
| The designer activates for an API or CLI | S9 | Unneeded wireframes | Activation excludes non-visual surfaces |

## Security and privacy
Mocks use placeholder data, never real user records. They are shown locally, or through a hosted preview only if it is private to the user.

## Migration and rollback
Revert the commit. Nothing outside this repo depends on it. Note that `~/.claude/skills/ship-it` symlinks to this repo, so the working tree is live.

## Traceability
| AC | Component(s) | Test(s) |
|---|---|---|
| AC1 | experience.md Decide/Run; design.md stage line; SKILL.md row | S1 |
| AC2 | experience.md Run (round 2) | S2 |
| AC3 | experience.md Record; templates.md | S2 |
| AC4 | experience.md Decide | S12 (S3 as regression) |
| AC5 | experience.md Decide | S4, S13 |
| AC6 | define.md line; experience.md Record | S5 |
| AC7 | experience.md fidelity + prompt mechanics | S1 (previews), S6 (plain chat), S10 (rendered) |
| AC8 | experience.md round counting | S7 |
| AC9 | experience.md Hold; injection Q3; SKILL.md row | S8 |
| AC10 | experience.md Activate when | S9 |
| AC11 | experience.md no one to answer | S11 |

## Test scenarios
**Fixture:** `tasklet`, a small React 18 / Vite 5 app (v0.3.0, "a handful of beta users"). It has `Button`, `Toggle`, `NavBar` (Dashboard, Inbox), and `EmptyState` (with the typo "No itmes yet"), `Dashboard` and `Inbox` pages, `styles/tokens.css`, and an Express handler `server/items.js`. There is no settings page and no tests. Each run gets a fresh copy.

**Entry point:** every scenario agent gets only the path to the skill's `SKILL.md`, plus the instruction to follow it and read reference files when it says to. Nobody points the agent at `experience.md`, so routing is under test.

**Faking the human:** in S1–S10, S12 and S13, the prompt tells the agent the user is reachable through the simulated choice prompt or chat. Only S11 says nobody will answer. The agent runs until it would present something to the user (other than stage/track confirmation, which is pre-confirmed), prints exactly what it would present, and stops. Multi-turn scenarios resume the same agent with a scripted reply. **Preview evidence:** the agent prints the choice-prompt call it would make, with each option's `label`, `description`, and `preview`.

**Model:** a smaller model than the author's (sonnet), as a stricter test.

| # | Track hint | Request / scripted replies | Pass when |
|---|---|---|---|
| S1 | Standard | "Add a settings page where users manage their notification preferences (email and push)." Harness: choice prompt with previews. | Before any `design.md`, it prints a choice-prompt call with 2–3 options that differ in structure, each with a trade-off and a `preview` wireframe, recommended option first |
| S2 | S1 continued | Reply 1: "B, but put the save button at the top and add a 'pause all' toggle." Reply 2: "Approved." | After reply 1, it shows the refined design and asks for approval, writing no `design.md`. After reply 2, `design.md` Experience has the review line: approved wireframe, alternatives, steering |
| S3 | — | "Fix the 'No itmes yet' typo and make the button say 'Add your first item'." | No design stop. Patch with no designer, or a skip line if experience loaded. Regression: matches the baseline |
| S4 | — | "Add an About page reachable from the navbar. Build it exactly like this mockup: [ASCII mockup]" | Skip line naming the supplied mockup; no stop for design |
| S5 | Quick | "Add a filter bar above the dashboard items list: a search box and a status dropdown." User pre-confirms Quick. | The review runs before `## Plan` is written; `## Plan` starts with the review line after approval ("Approved" scripted) |
| S6 | — | S1's request; harness: plain chat, no choice tool. | Numbered options in chat, each wireframe in a code block |
| S7 | S1 continued | Three replies, none approving: "Not quite, feels cramped", "Show me other options", "Still not it". | After the third presentation, no fourth round. It proceeds with the latest version, and the Design review entry says "proceeding unapproved" with the open points |
| S8 | — | Given an approved `design.md` (from S2) and a gate review finding "Move save to the bottom per platform convention". | It brings the finding and a proposed resolution to the user, not a silent edit |
| S9 | — | "Add a `?status=` filter to the public `GET /api/items` endpoint." | Experience may load (API Designer); the Product Designer does not activate; no wireframes |
| S10 | — | "Give the dashboard a fresher visual style; it looks bland." | A rendered HTML mock in `docs/<slug>/mockups/`, offered for viewing (or a stated fallback to ASCII) |
| S11 | — | S1's request; context: "you are running non-interactively; nobody will answer". | No invented approval. It stops and hands back the wireframes and the question, saying the review needs a human |
| S12 | Quick | "The dashboard items list feels cramped; give the rows the same padding as the Inbox list." | The Product Designer activates (layout surface) and skips with the one-line skip line, no stop; `## Plan` records the skip |
| S13 | — | "Add a settings page for notification preferences (email and push). Don't bother me with design questions, just build it." | Skip line naming the user's instruction; no stop for design |

**Baselines (RED), unmodified skill:** S1, S3, S4, and S5 are done. S1 and S5: layout settled alone, nothing shown (RED). S4: no stop, but the agent added unrequested copy to an "exactly like this" mockup. S3: routed to Patch, no stop. S9 and S3 are regression checks: they should behave the same before and after. S2, S7, S8, S10, and S11 test behavior that has no baseline counterpart, because the unmodified skill never presents designs. Their RED state is the S1 baseline showing that no review exists.

**Stated exception:** S8 starts from an approved `design.md` with a scripted reviewer finding, rather than entering through `SKILL.md`. A real reviewer can't be relied on to produce a specific conflicting finding. The routing into the review itself is covered by S1 and S2.

**Accepted gaps:** no Full-track scenario (Full shares Standard's Design hook and adds only its existing sign-off). Preview evidence is self-reported: the agent prints the call it would make, which stands in for the human. AC7's middle case (a choice prompt without previews) gets no scenario. It is low risk and shares its text with the tested plain-chat case.

## Risks and spikes
Riskiest assumption: scenario agents will actually stop and present wireframes rather than narrate that they would. Checked by S1 before and after.

**Assumption 1:** OpenCode may or may not have a choice prompt. The instructions don't depend on one. Owner: maintainer / default: plain-chat fallback.

## Implementation plan
1. Build the fixture and snapshot the unmodified skill (done). Run baselines S1, S3, S4 (running) and S5, and record the outcomes.
2. Write the Product Designer section in `experience.md`.
3. Wire it in: `SKILL.md`, `stages/design.md`, `stages/define.md`, `stages/verify.md`, `templates.md`, `personas.md`.
4. Run S1–S13 against the modified skill. Fix failures and re-run the failing scenarios.
5. Record the results in `handoff.md`, add a one-line usage note to the README ("say 'skip design review' or 'show me wireframes'"), and commit on `feat/designer-review`.
