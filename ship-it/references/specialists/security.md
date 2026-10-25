# Security Specialists

Load when the change touches authentication, authorization, user input, secrets, cryptography, file uploads, external integrations, new dependencies, or personal or sensitive data. When in doubt, load it; security issues are cheapest to fix in Design.

---

## Application Security Engineer

**Activate when:** new endpoints or entry points; any auth or permission logic; untrusted input reaches a database, shell, file system, template, or URL fetch; new third-party dependencies; secrets or keys are handled.

| Stage | Lens |
|---|---|
| Define | Who is allowed to do what? Write authorization rules as acceptance criteria, including the "must be denied" cases. |
| Design | Draw the trust boundaries. For each entry point, list who can call it, what input is untrusted, and where authorization is enforced (server-side). Apply least privilege to services and tokens. Choose vetted libraries over custom crypto or parsing. |
| Build | Parameterized queries, output encoding, input validation at the boundary. Secrets from the environment or a secret manager, never in code or logs. Pin and review new dependencies; check them against advisories (OSV, GHSA) before adding. Run the secrets scanner before every commit. |
| Verify | Test the denied paths: other users' resources, missing or expired tokens, escalated roles. Try injection and oversized input on new inputs. Run every available scanner for the categories the change touches (see *Security scanning* below). |
| Launch | Rate limits and abuse controls on new public surfaces. Audit logs for sensitive actions. |
| Sunset | Revoke credentials and keys the feature used. Remove routes, not just links to them. |

**Red flags:** authorization checked only in the UI; user input concatenated into a query or command; secrets or tokens in logs, URLs, or error messages; "temporary" admin bypasses; disabling TLS verification.

**Gate evidence:** tests for each denied path; for each scanning category the change touches, the command run and its result, with findings fixed or accepted with a reason — or the category listed as *unscanned (no tool available)*.

### Security scanning

Use security scanners whenever they are available. Your own reading of the diff is not a substitute for a scanner, and a scanner is not a substitute for testing denied paths.

**Find what's available, in this order:**
1. What the repo already wires up: CI workflows, pre-commit hooks, Makefile / package scripts. Run these first — they encode the project's accepted config and baselines.
2. Installed CLIs: check with `command -v <tool>`.
3. Scanning tools in the session: MCP servers, or a security-review skill or command.

**Cover the categories the change touches:**

| Category | Run when | Example tools |
|---|---|---|
| Secrets | Every change | `gitleaks`, `trufflehog` |
| Dependencies (SCA) | Dependencies added, updated, or lockfile changed | `osv-scanner`, `npm audit`, `pip-audit`, `cargo audit`, `trivy fs` |
| Static analysis (SAST) | New code paths handling untrusted input, auth, or crypto | `semgrep`, CodeQL, `bandit`, `gosec`, `brakeman` |
| IaC and config | Terraform, Kubernetes, Helm, CloudFormation, Dockerfiles | `checkov`, `trivy config`, `tfsec`, `kube-linter` |
| Container images | Base image or image contents changed | `trivy image`, `grype` |

On Standard and Full, run the scan through `references/delegates/security-scan.md`.

**Rules:**
- Scanners on the allowlist in `references/security-tools.md` install under standing approval — no need to stop and ask each time. A scanner not on the allowlist, or any change to CI, still needs the user's approval. Pin every scanner's version either way (never `latest`), and log installs and removals in `docs/security/test-log.md` when the Offensive Security Engineer's evidence folder is in use. The tool names in the table above are illustrative examples per category; `security-tools.md` is the authority on what may be installed without asking.
- Confirm a tool's flags and config against its docs for the installed version (see `references/grounding.md`) — don't run a scanner with remembered flags.
- Triage every finding: fix it, or accept it with a written reason. A finding isn't a false positive until you've read the flagged code.
- If no tool exists for a category the change touches, record it as unscanned in the gate evidence and compensate with a targeted manual review of that category. Never report a category as clean that wasn't scanned.

### Reviewer Injection — Application Security Engineer

Append to reviewer prompts when this specialist is active.

- Where is authorization enforced, and is it server-side (not UI-only)?
- What input is untrusted, and where does it flow — database, shell, template, URL fetch?
- Are secrets and tokens absent from logs, URLs, and error messages?
- Are the "must be denied" paths (wrong user, missing token, escalated role) present as acceptance criteria?
- Are new dependencies pinned, and is there scanner output (secrets, dependencies, static analysis, IaC as applicable) with every finding fixed or accepted with a reason — and are unscanned categories named rather than silently omitted?

---

## Offensive Security Engineer

The external tester's view, kept in-house: *"I'm the pentester with a week and a scope letter — what do I write up first?"* It thinks in findings (severity, reproduction, evidence), not best practices. AppSec makes the code right; this persona tries to prove it wrong and keeps a living evidence trail so an audit or external pentest finds its ducks in a row. Its whole procedure lives in this file, so Quick (which reads no delegate files) still has everything it needs.

**Activate when:** the attack surface changes on Standard or Full — new or changed endpoints, routes, or entry points; auth, session, or permission logic; tenant or user boundaries; anything newly exposed publicly (ports, buckets, webhooks, CORS). Never on Patch. On **Quick**: update `docs/security/attack-surface.md` inline only — no probe, no threat-model pass.

**Checklist source:** OWASP ASVS (requirements) and the OWASP Web Security Testing Guide (WSTG, test cases), because external testers report against them. Cite WSTG test IDs in probe-log entries where one fits, so your evidence lines up with a pentest report section by section. Per `references/grounding.md`, confirm the current ASVS and WSTG versions and ID formats from owasp.org — don't cite them from memory.

| Stage | Lens |
|---|---|
| Discover | Read `docs/security/` if present. Prior open findings and previous external-pentest findings in the areas this change touches are re-tested first in Verify — a returning finding is the worst kind. Read re-test candidates from `findings.md` rows with status `open` or `accepted` in the touched components. |
| Define | Add abuse cases next to the user stories ("as a user of another tenant, I request…"), each with its expected denial. These become acceptance criteria alongside AppSec's "must be denied" cases. |
| Design | Lightweight STRIDE pass on each new or changed entry point; update `threat-model.md` and the `attack-surface.md` delta. |
| Build | Nothing new — AppSec owns it. |
| Verify | Probe the live build via `references/delegates/pentest-probe.md`: the abuse cases, cross-user / cross-tenant access (IDOR), authn and session handling, security headers / CORS / TLS, error and stack-trace leakage, rate limits and enumeration, plus the re-tests from Discover. Run a DAST tool (ZAP, Nuclei) if one is available, under the same install rules as the scanners. Triage every finding. Append to `test-log.md`. |
| Launch | Every finding from this change has a `findings.md` row; accepted ones carry owner, reason, and review-by date. Flag new public surface as "include in next external pentest scope" in `attack-surface.md`. |
| Sunset | Mark removed entry points in `attack-surface.md` with the removal date and confirm the routes are gone (a probe returns 404 / connection closed), logged in `test-log.md`. |

### Rules of engagement

- It only attacks environments **we own**.
- **Tools:** install only from the allowlist in `references/security-tools.md` without asking; anything else, or the heavy tier, needs a one-time OK (see that file). Pin versions; log installs and removals in `test-log.md`.
- **Scope note:** before probing any target ship-it didn't start itself, write one line — target, tests to run, and anything destructive excluded (bulk writes, load/DoS, account lockout, data deletion) — and wait for the user's OK. Copy every scope note into `test-log.md`.
  - **Self-started** means an instance started in this session, by the agent or one of its delegates, that is local or ephemeral and that nobody else uses. A shared environment (staging, a preview others use, production) is never self-started, even if ship-it deployed to it. The probe delegate may start its own local instance, which counts as self-started.
- **Destructive tests** are never run against a target ship-it didn't start, even with a scope note, unless the user explicitly names them. An active DAST scan (ZAP active scan, Nuclei, sqlmap, Nikto), ffuf/Gobuster brute force, Masscan, and the entire heavy tier used actively count as destructive; passive scanning does not. The heavy tier used *actively* needs a scope note even against a self-started target (read-only heavy-tier use, for example CloudFox enumerating an account we own, follows the ordinary self-started rule). The Sunset removal check against a shared environment needs a scope note too (one line, read-only).

### Evidence folder: `docs/security/`

Versioned in the repo so it travels with the code. Each run updates only the files its change affects. Starter templates are in `references/templates.md`.

| File | Contents |
|---|---|
| `README.md` | The auditor's index: what's here, last-updated date per file, the coverage note, how to read it. |
| `threat-model.md` | Per component or entry point: STRIDE threats, mitigation, link to the test or probe that covers it. |
| `attack-surface.md` | Inventory table: entry point, exposure (public/internal), auth required, added date, removed date, "next pentest scope" flag. |
| `test-log.md` | Dated, append-only: scope note, target, tests run (WSTG IDs), tool + version, results, findings. Newest at the bottom. |
| `findings.md` | The findings register (what an auditor calls a "risk register"): every finding from ship-it probes and external pentests — ID, source, severity, component, class, status (`open` / `accepted` / `fixed`), found date; for `accepted`: reason, owner, review-by date; for `fixed`: fix commit and re-test date. |

**Severity scale:** Critical / High / Medium / Low / Info, matching typical pentest reports. CVSS is optional.

**Sensitive detail:** committed files (`findings.md`, `test-log.md`, `handoff.md`) carry a *redacted* form for any finding not yet `fixed` — class, endpoint or component, and the observed outcome (e.g. "GET /orders/:id as user B returned user A's order, 200"). That is a statement of what was found, not a payload. The full request/response goes only to the gate reviewer (in the dispatch prompt) and to the user in chat, never to a committed file. Once a finding is `fixed`, its reproduction and the passing re-test go into `test-log.md`. Accepted findings stay redacted while they stay accepted. External pentest reports are not committed raw: each finding becomes a `findings.md` row under the report's own ID, with re-tests in `test-log.md`.

**Bootstrapping:** on the first run in a repo without `docs/security/`, create `README.md` and only the files this change touches. The README states "Inventory started <date>; covers entry points touched by ship-it runs since then" so nobody mistakes it for full coverage. A one-off baseline inventory of the whole repo is a follow-up, not part of a normal run.

### Accepting a finding

- **Medium, Low, Info:** may be accepted as risks. The agent may accept one itself with a written reason; the owner defaults to the user unless they name someone else.
- **Critical, High:** must not be accepted — they are fixed before the Verify gate passes. Only under extenuating circumstances (for example, the fix depends on a vendor patch or a change outside this repo) may one be accepted, and then only the user can accept it: stop, bring the finding, its evidence, the circumstances, and any compensating controls; record all of that in the `findings.md` row alongside reason, owner, and review-by date. The agent never accepts a Critical or High on its own.
- A finding the agent can neither fix nor accept stays `open` and is listed in the handoff.
- **CVE triage:** when a finding carries a CVE, check CISA KEV and EPSS (`references/security-tools.md`) before proposing fix-or-accept. A KEV-listed or high-EPSS finding is not accepted below the Critical/High bar without the user's ruling.

**Red flags:** a new public route with no `attack-surface.md` entry; an abuse case without a matching test; a finding "accepted" with no reason, owner, or review date; a Critical or High accepted without the user's recorded ruling; a probe result reported without the request/response that shows it (to the reviewer — committed files stay redacted); an expired review-by date in the findings register; an unprobed category silently omitted rather than named.

**Gate evidence:** the `docs/security/` diff for this change; the probe findings table with each finding fixed, accepted (in `findings.md`), or needing a decision; untested categories named as `not probed` with the reason.

### Reviewer Injection — Offensive Security Engineer

Append to reviewer prompts when this specialist is active (Design and Verify gates).

- Is every new or changed entry point in `attack-surface.md`, with exposure and auth stated?
- Does each abuse case have a test or probe that ran, with recorded evidence?
- Was every prior open finding in the touched area re-tested?
- Is each finding fixed, or accepted in `findings.md` with reason, owner, and review-by date — and is no Critical or High accepted without the user's recorded ruling?
- Are untested categories named as `not probed`, rather than silently omitted?

---

## Privacy Engineer

**Activate when:** personal data is collected, stored, displayed, logged, exported, or shared with a third party.

| Stage | Lens |
|---|---|
| Define | What personal data is needed, and is all of it necessary? State the purpose for each field. |
| Design | Data minimization, retention period, where it's stored, who can access it, and how a user's data is exported or deleted. |
| Build | Keep personal data out of logs and analytics. Mask it in non-production environments. |
| Verify | Search logs and events for personal data. Test export and deletion. |
| Sunset | Delete or anonymize per the retention policy. |

**Red flags:** collecting fields "in case we need them"; personal data in log lines; no deletion path.

**Gate evidence:** a list of personal data fields with purpose and retention.

### Reviewer Injection — Privacy Engineer

Append to reviewer prompts when this specialist is active.

- Is each personal data field justified by a stated purpose?
- Is there a deletion and export path for user data?
- Is personal data absent from logs and analytics events?
