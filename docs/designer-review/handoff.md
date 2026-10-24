**What shipped:** A Product Designer specialist in `ship-it/references/specialists/experience.md`. On visual UI work it decides whether a design review earns a stop. When it does, it shows the user 2–3 structurally different wireframes (or rendered mocks for look and feel), refines from their pick and steering, and gets explicit approval before `design.md` (Standard, Full) or `## Plan` (Quick) is written. It is wired into `SKILL.md` (specialist list and a new checkpoint row), `stages/design.md`, `stages/define.md`, `stages/verify.md`, `templates.md`, `personas.md`, and the README.

**Verification**

Method: fresh sonnet scenario agents were given only the path to `ship-it/SKILL.md` and a request in a fixture React app (`tasklet`). The human was faked with scripted replies sent to the same agent. Previews are evidenced by the choice-prompt call each agent printed. Scenario definitions are in `design.md` under Test scenarios.

Baselines on the unmodified skill (RED):
- **S1** (Standard, new settings page): the agent settled the layout alone and showed the user nothing.
- **S5** (Quick, filter bar): the layout was "left to Build" and nothing was shown.
- **S4** (supplied mockup): no stop, but the agent silently added copy to an "exactly like this" mockup.
- **S3** (copy fix): Patch, no stop.

Results on the modified skill:
- [x] **AC1** (S1, then re-run after the refactor). Before writing `design.md`, the agent printed a choice-prompt call with 3 options that differ in structure (matrix / by channel / by category), each with a trade-off and an ASCII `preview`, recommended option first.
- [x] **AC2** (S2). After "B, plus a Pause-all switch, shorter push line", it showed the refined wireframe with the changes called out and an Approve / Change prompt. No `design.md` was written until "Approved".
- [x] **AC3** (S2). The `design.md` Experience section opens with "**Design review:** Approved", followed by the alternatives shown, the user's steering, and the approved wireframe.
- [x] **AC4** (S12, Quick spacing tweak). The designer was active and printed the one-line skip, with no design stop. The skip is recorded as the first line of `## Plan`. S3 (copy fix) routed to Patch with no stop, matching the baseline.
- [x] **AC5** (S4, S13). For the supplied mockup: "Skipping design review: you supplied the mockup…", recorded in `design.md`. For "don't bother me with design questions": a skip line naming the instruction. No stops.
- [x] **AC6** (S5, Quick). The review ran before `## Plan` (3 options; refine with steering; "Approve"). `## Plan` begins with "**Design review: Approved.**", the wireframe, the alternatives, and the steering. The Plan self-review applied the injection questions.
- [x] **AC7** (S1 previews; S6 plain chat; S10 restyle). S6 (OpenCode, no choice tool): options in chat, each wireframe in a code block, labelled A/B/C. The skill text now says "letters or numbers". S10: three self-contained HTML mocks (about 2.2 KB each) in `docs/dashboard-restyle/mockups/`, inlining `tokens.css` values and mirroring the real class names, with paths offered to open. In S7 round 2, a look-and-feel steer ("cramped") also produced a rendered mock.
- [x] **AC8** (S7). Rounds were counted correctly: refine = round 2, "show me other options" = round 3. After the third non-approval there was no fourth presentation. `design.md` recorded "**Proceeding unapproved**" with each round's options, the latest version, and the open points (owner: user; default: build as drawn).
- [x] **AC9** (S8, run on S2's approved design by resuming the same agent, which had entered through `SKILL.md`; the reviewer's finding was scripted, as the stated exception describes). For the reviewer finding "move Pause-all to the bottom", the agent did not apply it. It brought the finding to the user with a recommendation and previews of both options. The real defect in the same review (a missing sign-in path) was fixed without a stop.
- [x] **AC10** (S9, then re-run). On a public API change, the API Designer, Technical Writer, and AppSec were active and the Product Designer was not. No wireframes, and after the refactor no announcement to the user.
- [x] **AC11** (S11, headless). The agent stopped at the design review, did not write `design.md`, and handed back wireframes and questions headed "Design review needs a human". It did not invent an approval.

**Exercised live:** The skill was exercised as installed, because `~/.claude/skills/ship-it` symlinks to this working tree. Every GREEN run read the edited files through `SKILL.md` routing. Nothing was run in OpenCode itself; S6 simulated it by withholding the choice tool.

**Gate history:**
- **Define:** self-review. One fix ("distinct" tightened to "differ in structure").
- **Design:** subagent, 3 passes.
  - Pass 1: 13 blockers (scenarios undefined, the human not faked, Quick timing, injection not written out, unattended runs, and more).
  - Pass 2: 7 blockers. The Quick hook had to move from `build.md` to `define.md`; the specialist text was not verbatim; the unattended signal was not observable; there was no record for an unapproved design; the skip path was not exercised; the mock rule contradicted itself; S8's entry point.
  - Pass 3: 5 blockers. AC11 conflicted with the design; the Verify reviewer lacks `design.md`; S7 checked for a `spec.md` edit; run/skip rules overlapped on a restyle; S12/S13 were not in the plan.
  - Escalated to the user at the cap. The user approved all resolutions and ruled that unattended runs stop and hand back.
- **Plan:** self-review, clean.
- **Verify:** subagent, 1 pass, no blocking issues. Minor findings applied: plain-chat labels may be letters or numbers; injection Q3 reports conflicts under the reviewer's Questions heading; anything added to a supplied design is mentioned to the user in one line (closes the S4 gap). These three wording changes and the "proceeding unapproved" line were not re-run through scenarios.

**Specialists consulted:** Quality (scenario method, baselines, real entry point) and Technical Writer (README usage line).

**Decisions and deviations:**
- Refactor after GREEN: the skip line is now the designer's only announcement (S9 and S1b had printed "designer not active" / "skipping nothing"). The designer also tells the user in one line when it proceeds unapproved (S7 asked whether it should). Both re-verified by re-running S1 and S9. The "proceeding unapproved" line was not re-run, because S7 is a 4-turn scenario.
- Unattended behavior changed from "record a recommendation and continue" to "stop and hand back" (user's ruling).
- The section is about 65 lines, longer than other specialists. This is deliberate: Quick's self-contained-reading rule means the whole procedure must live in this file.

**Follow-ups:**
- No Full-track scenario. Full shares the Design hook and keeps its existing sign-off.
- AC7's middle case (a choice prompt without previews) is untested.
- S10 offered mock paths to open, rather than opening or screenshotting the mocks itself. That's acceptable when the harness can't show them, but it's weaker than the instruction.
- AC9's injection Q3 has not run inside a real gate reviewer; S8 used a scripted finding.
- Agents sometimes amend `spec.md` ACs after a design pick (S7). Reasonable, but there's no rule on re-reviewing.
- `CONTRIBUTING.md` asks for a `docs/findings/` session record. This change came from a direct request; the baselines here are the evidence.

**Risks to watch:**
- Over-triggering: a review on changes the user considers trivial. Watch the first real UI sessions for unnecessary stops.
- Mock fidelity: standalone HTML mirrors classes and tokens, not real components, so the build can drift from the approved look. Verify's compare step is the guard.
