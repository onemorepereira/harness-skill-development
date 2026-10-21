# Running a gate review

Each file in this directory is the base prompt for one stage's reviewer. Which gates get a subagent and which get a self-review is set by the gate-depth table in `SKILL.md`.

## Subagent review

1. Prepare the artifact(s) the stage names.
2. Open the stage's reviewer prompt.
3. For each active specialist that covers this stage, append its `Reviewer Injection` block(s) from `references/specialists/`. Injections add questions; they never replace the base ones.
4. Replace the prompt's bracketed `[paste …]` line with the artifact's full text, or with its file path if the subagent can read files. Check the combined prompt contains no `[paste` before sending.
5. Dispatch a subagent with only the artifact(s) and the combined prompt — no conversation history, no background. If it needs explanation, the artifact isn't done.
6. Apply the convergence rule in `SKILL.md` — at most 3 passes per gate, then escalate to the human. A dispatch that failed or went out without the artifact is not a pass; fix it and resend.

If the harness has no subagent tool, do a self-review and say that the independent pass was not available.

## Self-review

Apply the same combined prompt's questions yourself, explicitly, and state what you found. This is weaker than a subagent pass — same author, same blind spots — and is accepted only where the gate-depth table says so.
