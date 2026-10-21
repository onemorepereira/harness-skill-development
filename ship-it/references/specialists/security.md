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
- Don't install a scanner or change CI without the user's approval. If you install one, pin its version.
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
