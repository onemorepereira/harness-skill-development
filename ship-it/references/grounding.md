# Grounding: verify against authoritative sources

Training knowledge is a hypothesis, not evidence: versions, APIs, defaults, limits, and advisories drift. Before a claim about anything outside your own reasoning shapes a spec, design, plan, fix, or diagnosis, check it against an authoritative source. Applies on every track, including Patch.

| Claim about… | Authoritative source |
|---|---|
| This codebase | The code, tests, config, lockfiles, CI, git history — read them |
| A library, framework, language, or tool | Official docs for the version actually pinned or installed; its changelog and release notes; `--help` / `man` of the installed binary |
| A cloud service, API, or SaaS | The provider's official docs and API reference |
| Known bugs, behavior changes, workarounds | The project's issue tracker, release notes, migration guides |
| Vulnerabilities | Vendor advisories, GHSA, OSV, NVD |
| Standards and regulations | The standard or regulation text |

Blog posts, forum answers, and model recall are leads to a source, not sources.

**Find the version first.** Read the lockfile, manifest, or `<tool> --version` before reading docs — docs for the wrong version are a wrong source.

**Record it.** Every ADR carries a `Sources` line (link + version or access date). Diagnoses in Verify and Evolve cite the doc, changelog, or issue that confirms them.

**When no source is reachable** (no network, no docs tool, undocumented behavior): say so, and record the claim as an assumption with an owner and a default — the same treatment as an unvalidated Discover assumption. Then prove it empirically (a spike, a test, running the command) before relying on it.

**When something doesn't behave as expected:** check the docs, changelog, and open issues for the installed version before changing code. Version mismatch is the first hypothesis, not the last.

**Delegation:** on Standard and Full, batch the claims that need checking and send them through `references/delegates/source-lookup.md`. One lookup round per stage beats one per claim.
