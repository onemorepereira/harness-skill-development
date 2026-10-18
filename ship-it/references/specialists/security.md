# Security Specialists

Load when the change touches authentication, authorization, user input, secrets, cryptography, file uploads, external integrations, new dependencies, or personal or sensitive data. When in doubt, load it; security issues are cheapest to fix in Design.

---

## Application Security Engineer

**Activate when:** new endpoints or entry points; any auth or permission logic; untrusted input reaches a database, shell, file system, template, or URL fetch; new third-party dependencies; secrets or keys are handled.

| Stage | Lens |
|---|---|
| Define | Who is allowed to do what? Write authorization rules as acceptance criteria, including the "must be denied" cases. |
| Design | Draw the trust boundaries. For each entry point, list who can call it, what input is untrusted, and where authorization is enforced (server-side). Apply least privilege to services and tokens. Choose vetted libraries over custom crypto or parsing. |
| Build | Parameterized queries, output encoding, input validation at the boundary. Secrets from the environment or a secret manager, never in code or logs. Pin and review new dependencies. |
| Verify | Test the denied paths: other users' resources, missing or expired tokens, escalated roles. Try injection and oversized input on new inputs. Run the dependency scanner and any static analysis the repo has. |
| Launch | Rate limits and abuse controls on new public surfaces. Audit logs for sensitive actions. |
| Sunset | Revoke credentials and keys the feature used. Remove routes, not just links to them. |

**Red flags:** authorization checked only in the UI; user input concatenated into a query or command; secrets or tokens in logs, URLs, or error messages; "temporary" admin bypasses; disabling TLS verification.

**Gate evidence:** tests for each denied path; scanner output with findings resolved or accepted.

### Reviewer Injection — Application Security Engineer

Append to reviewer prompts when this specialist is active.

- Where is authorization enforced, and is it server-side (not UI-only)?
- What input is untrusted, and where does it flow — database, shell, template, URL fetch?
- Are secrets and tokens absent from logs, URLs, and error messages?
- Are the "must be denied" paths (wrong user, missing token, escalated role) present as acceptance criteria?
- Have new dependencies been pinned and reviewed?

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
