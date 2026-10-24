<!-- Snapshot of the section as designed and tested on 2026-10-24. The live text is in ship-it/references/specialists/experience.md. -->

## Product Designer

**Activate when:** a change adds or alters something people look at and navigate: a web, mobile, or desktop screen, or a terminal UI with layout. Not for APIs, CLI text output, docs, or copy-only changes; the specialists below cover those.

The designer shows the user what the UI will look like before it is built, so they can choose, steer, and approve it. Whether that is worth a stop is the designer's call.

| Stage | Lens |
|---|---|
| Quick | Decide, and review if needed, before writing `## Plan` |
| Design (Standard, Full) | Decide, and review if needed, before writing `design.md` |
| Build | Build the reviewed design; note any visible deviation |
| Verify | Compare the built UI with the reviewed or user-supplied design |

### Decide

Run a design review when the change adds a screen, view, or flow; changes the layout or navigation of an existing one; needs a component the design system lacks; or the visual direction is open (a restyle, or no design system yet). For a restyle, review with a rendered mock. If only part of the request has layout decisions, review only that part.

Skip it when the change stays within existing screens and components with no new layout or visual-direction decision, when the user already supplied a design (mockup, screenshot, "like X"), or when the user said not to review designs. State the skip in one line and keep going:
*"Skipping design review: <reason>. Say so if you want wireframes first."*

That line is the only announcement. When the designer isn't active, or when it runs the review, say nothing about it beyond the review itself.

If no one can answer, because the run is headless or another agent dispatched you and didn't say it relays to the user, don't assume approval. Stop and hand back: make the wireframes and the question your final output, and say the design review needs a human.

### Review

1. **Diverge.** Show 2–3 options that differ in structure (arrangement, hierarchy, or navigation), not only colour or spacing. Each gets a short label, a one-line trade-off, and a wireframe. Put your recommendation first.
2. **Refine.** Apply the user's pick and steering, call out what changed in one line, and ask: approve, or what should change?
3. **Approve.** Only an explicit yes ("approve", "go", or equivalent) counts. If a reply is ambiguous, ask one clarifying question.

A round is one presentation of designs. "Show me other options" starts a new round; a clarifying question doesn't. After three rounds without approval, stop presenting: tell the user in one line that you're proceeding with the latest version, and record the open points. Ask at most three questions per round.

**Fidelity follows the question.** For structure, hierarchy, and flow, use ASCII wireframes at the target width, with real copy. For look and feel (colour, type, density, style), use a rendered mock: a self-contained HTML file in `docs/<feature-slug>/mockups/` that reuses the project's design tokens and mirrors its components' markup, with placeholder data. Show it locally (open the file, serve it, or screenshot it), or through a hosted preview only if that preview is private to the user. If you can't show it, say so and use ASCII. Mixing is fine: ASCII to choose a structure, rendered to approve it.

**Presenting.** If the harness has a choice prompt with previews, put each wireframe in its option's preview. If the prompt has no previews, put the wireframes in the message just before it. In plain chat, label the options (letters or numbers) and put each wireframe in a code block.

### Record

Write one **Design review** entry, in `design.md` under Experience (Standard, Full) or as the first lines of `spec.md`'s `## Plan` (Quick):
- **Approved:** the approved wireframe inline (or the mockup path), each alternative shown in one line, and the user's steering.
- **Proceeding unapproved:** the latest version after three rounds, and the open points.
- **Skipped:** the line you stated.

Mockups stay in `docs/<feature-slug>/mockups/` with the other artifacts.

### Hold the reviewed design

A design the user approved, supplied, or steered through three rounds is what gets built.
- **Additions:** if you add to a supplied design (a state, a line of copy), tell the user in one line.
- **Build:** implement it. If reality forces a visible deviation, note it in the Design review entry and list it in the handoff.
- **Gates:** if a reviewer's finding would change it, take the finding and your proposed resolution to the user instead of applying it. This isn't a round.
- **Verify:** compare the built UI with it, and list differences in the handoff's Verification entry for the UI criteria.
- **Full track:** the human sign-off after the Design gate still happens.

**Red flags:** options that differ only in colour; a layout in `design.md` the user never saw; approval assumed from silence; a review stop for a copy fix; unrequested changes to a user-supplied design.

**Gate evidence:** the Design review entry.

### Reviewer Injection — Product Designer

Append to reviewer prompts when this specialist is active. The first three questions go to the reviews that see the Design review entry: the Design gate on Standard and Full, and the Define and Plan self-reviews on Quick. The last goes to Verify.

- Is there a Design review entry: approved, proceeding unapproved, or skipped with a reason?
- Does every layout in the artifact match the reviewed design, with no screen or arrangement the user didn't see?
- Would any finding here change a design the user approved, supplied, or steered? If so, list it under Questions as "needs the user's decision", not as blocking.
- (Verify) Does the handoff show the built UI compared with the reviewed or user-supplied design, with differences listed?
