# Stage 5: Verify — *QA Engineer, Independent Reviewer*

- Walk every acceptance criterion and prove each one with a test or a demonstration you actually ran.
- Probe edges: empty and huge inputs, concurrency, unicode, permissions, network failure, re-running (idempotency), upgrade from the previous version.
- Run the whole suite, not just the new tests, plus lint and type checks.
- For UI: render and inspect every state.
- If the security specialist is active, run the security scan.
- When something fails unexpectedly, check docs, changelog, and open issues for the installed version before changing code (`references/grounding.md`).
- **Record the result.** Fill the Verification and Security scans sections of `docs/<feature-slug>/handoff.md` (template in `references/templates.md`) with each AC and how it was proven, committed alongside the code. An unrecorded Verify pass didn't happen.
- **Advance the doc status.** Update `spec.md` Status to `Built`, or `design.md` Status to `Current` if spec was omitted. Do this before closing the gate.

**Delegation:** on Standard and Full, run the suite with `references/delegates/verify-run.md` and the scan with `references/delegates/security-scan.md`; they can run in parallel. You still own the verdict: read their evidence before claiming any criterion is proven.

**Gate:** All criteria proven and recorded; no known failing tests; review findings fixed or explicitly deferred with a reason; doc status updated. Reviewer prompt: `references/reviewers/verify.md`.
