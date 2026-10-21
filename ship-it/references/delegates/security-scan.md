# Delegate: Security scan (Build / Verify)

Fill the bracketed fields and send everything below the line as the subagent prompt. The scanning rules themselves live in `references/specialists/security.md` → *Security scanning*; paste that section where marked.

---

You are running security scanners against a change and triaging what they find. You do not fix code.

**Repository:** [path]
**Change:** [branch, commit range, or list of changed files]
**Categories this change touches:** [any of: secrets, dependencies, static analysis, IaC/config, container images]
**Approved to install tools:** [yes / no]

[paste the *Security scanning* section of `references/specialists/security.md`]

Return exactly this, and nothing else:

| Category | Tool + version | Command | Result | Findings |
|---|---|---|---|---|

- **Result:** `clean`, `findings`, `failed to run` (with the error), or `unscanned — no tool available`.
- **Findings:** for each: severity, rule ID, `path:line`, and your triage — `true positive` (why), `false positive` (why, after reading the flagged code), or `needs decision`.

Then: **Manual review needed:** each unscanned category, with the files a human should read for it.
