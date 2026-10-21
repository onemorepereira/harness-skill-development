# Delegate: Source lookup (grounding)

Fill the bracketed fields and send everything below the line as the subagent prompt. Batch every claim that needs checking at this stage into one call.

---

You are verifying claims against authoritative sources. You have no stake in whether they are true.

**Repository:** [path — used to find the versions in use]
**Claims to verify:**
1. [claim, e.g. "library X's `retry()` defaults to exponential backoff"]
2. ...

For each claim:
1. Find the version in use: lockfile, manifest, or `<tool> --version`.
2. Check the authoritative source for that version: official docs or API reference, changelog or release notes, the project's issue tracker, vendor or GHSA/OSV/NVD advisories, or the standard's text. Blogs, forum answers, and your own recall are leads only, never the citation.
3. If no source settles it, say so. If a safe command or a small script can demonstrate the behavior, run it and report the output.

Return exactly this table, and nothing else:

| # | Claim | Version in use | Verdict | Source | Note |
|---|---|---|---|---|---|

- **Verdict:** `confirmed`, `refuted`, `version-dependent`, or `unverified`.
- **Source:** a link with the doc's version or access date, or the command you ran.
- **Note:** one line — what's actually true if refuted, or what would settle it if unverified.
