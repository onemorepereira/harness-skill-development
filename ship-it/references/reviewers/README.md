# Running a gate review

Each file in this directory is the base prompt for one stage's reviewer. Which gates get a subagent and which get a self-review is set by the gate-depth table in `SKILL.md`.

## Subagent review

1. Prepare the artifact(s) the stage names.
2. Open the stage's reviewer prompt.
3. For each active specialist that covers this stage, append its `Reviewer Injection` block(s) from `references/specialists/`. Injections add questions; they never replace the base ones.
4. Dispatch a subagent with only the artifact(s) and the combined prompt — no conversation history, no background. If it needs explanation, the artifact isn't done.
5. Apply the convergence rule in `SKILL.md` — at most 3 passes per gate, then escalate to the human.

If the harness has no subagent tool, do a self-review and say that the independent pass was not available.

## Self-review

Apply the same combined prompt's questions yourself, explicitly, and state what you found. This is weaker than a subagent pass — same author, same blind spots — and is accepted only where the gate-depth table says so.
