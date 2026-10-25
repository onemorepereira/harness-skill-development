# Security Tools and Data Sources

The single source for which security tools the agent may install and run, and which vulnerability data feeds it consults during triage. Both the Application Security Engineer (scanners) and the Offensive Security Engineer (recon, web testing, exploitation) cite this file. `security-scan.md` and `pentest-probe.md` point here rather than repeating the list.

---

## Tool allowlist

Free and open-source tools the agent has **standing approval** to install, use, and remove within a single run, without stopping to ask — **except the heavy tier** (see below), whose *installation* needs a one-time OK. A tool not listed here always needs a one-time OK. Standing approval governs **installation**, not firing: it never widens the rules of engagement. The rules of engagement in `specialists/security.md` → *Offensive Security Engineer* decide what may be fired at which target.

| Group | Owner | Tools |
|---|---|---|
| Scanners (SCA, secrets, SAST, IaC, images, cloud) | AppSec Engineer / `delegates/security-scan.md` | Trivy, Grype, Syft, OSV-Scanner, Gitleaks, Semgrep CE, Checkov, Prowler, Nuclei |
| Recon | Offensive Security Engineer / `delegates/pentest-probe.md` | Nmap, Masscan, Subfinder, Amass, httpx |
| Web testing | Offensive Security Engineer / `delegates/pentest-probe.md` | OWASP ZAP, Nikto, ffuf, Gobuster, sqlmap, Wapiti, testssl.sh |
| Exploitation & credentials — **heavy tier** | Offensive Security Engineer / `delegates/pentest-probe.md` | Metasploit Framework, Hydra, NetExec, Impacket |
| Cloud attack — **heavy tier** | Offensive Security Engineer / `delegates/pentest-probe.md` | Pacu, CloudFox |

### Heavy tier

The exploitation, credential, and cloud-attack tools are **not** under standing approval:

- **Installing** any of them needs a one-time OK from the user per run. The lighter tiers (scanners, recon, web testing) install without asking.
- **Using** one actively always needs a scope note — even against a target ship-it started itself — because it exploits, brute-forces credentials (Hydra trips account lockouts), or moves laterally.
- **Read-only** use of a heavy-tier tool, for example CloudFox enumerating a cloud account we own, follows the ordinary self-started rule once the tool is installed.

### Guardrails (all tiers)

- Pin every tool to a specific version — never `latest`. Confirm the installed version's flags and config against its docs per `references/grounding.md`; don't run from remembered flags.
- Log each install and removal in `docs/security/test-log.md` with the tool and version.
- An active or attacking tool — ZAP active scan, Nuclei, sqlmap, Nikto, ffuf/Gobuster brute force, Masscan, and the entire heavy tier — is destructive and needs a scope note on any target ship-it didn't start (see the rules of engagement). Passive use (for example a ZAP passive scan or testssl.sh) does not.

---

## Vulnerability data sources

Read-only feeds, not tools to install. They enrich triage; they never probe a target. Queried, never installed — so the install-log rule does not apply. A feed that can't be reached is recorded as `not checked` in the finding's log entry, never silently skipped.

| Source | Use |
|---|---|
| CISA KEV | Is this CVE known-exploited in the wild? Raises priority; argues against accepting it. |
| EPSS API | Probability a CVE will be exploited — sharpens severity and the fix/accept call. |
| OSV.dev API | Advisory lookup for dependency findings (already behind OSV-Scanner). |
| NVD API | CVE metadata and CVSS base scores. |
| MITRE ATT&CK | Shared technique vocabulary for `docs/security/threat-model.md` entries. |

**Triage rule:** when a finding carries a CVE, check CISA KEV and EPSS before proposing fix-or-accept. A finding on KEV, or with a high EPSS score, is not accepted below the Critical/High bar without the user's ruling. Record the lookup and its result in the finding's `docs/security/test-log.md` entry.
