# Stage 5: Verify — *QA Engineer, Independent Reviewer*

- Walk every acceptance criterion and prove each one with a test or a demonstration you actually ran.
- **Run the real thing.** Exercise the change end to end the way it will actually run (the installed CLI, the deployed service, the job in its target environment) in the closest environment you can reach. Record it under "Exercised live" in the handoff; if no real environment is reachable, say so there. Before asking the human to do a live step, run every part of it you can yourself.
- Probe edges: empty and huge inputs, concurrency, unicode, permissions, network failure, a failed first attempt followed by a retry, re-running (idempotency), upgrade from the previous version.
- Run the whole suite, not just the new tests, plus lint and type checks.
- For UI: render and inspect every state, and confirm each one offers a way forward. If a design was reviewed or supplied by the user, compare against it and note the differences in the handoff.
- If the security specialist is active, run the security scan.
- If the Offensive Security Engineer is active, run the pentest probe (`references/delegates/pentest-probe.md`) against the live run.
- When something fails unexpectedly, check docs, changelog, and open issues for the installed version before changing code (`references/grounding.md`).
- **Record the result.** Fill the Verification and Security scans sections of `docs/<feature-slug>/handoff.md` (template in `references/templates.md`) with each AC and how it was proven, committed alongside the code. An unrecorded Verify pass didn't happen.
- **Advance the doc status.** Update `spec.md` Status to `Built`, or `design.md` Status to `Current` if spec was omitted. Do this before closing the gate.

**Delegation:** on Standard and Full, run the suite with `references/delegates/verify-run.md` and the scan with `references/delegates/security-scan.md`; they can run in parallel. If the Offensive Security Engineer is active, run the pentest probe with `references/delegates/pentest-probe.md` once the live run is up — alongside or after the verify run. You still own the verdict: read their evidence before claiming any criterion is proven.

**Gate:** All criteria proven and recorded; no known failing tests; review findings fixed or explicitly deferred with a reason; doc status updated. Reviewer prompt: `references/reviewers/verify.md`.
