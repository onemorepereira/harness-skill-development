# Compliance Specialists

Load when the product operates under regulation or contractual controls (for example, SOC 2, ISO 27001, HIPAA, PCI DSS, GDPR, CCPA, financial or public-sector rules), or the change touches regulated data, audit trails, retention, consent, or data location. Check the repo and docs for signs: compliance folders, control IDs in comments, audit-log modules, data-classification labels.

Compliance requirements are cheapest in Define. Discovered at Launch, they become a rewrite.

---

## Compliance Officer

**Activate when:** any of the frameworks above apply, or the change touches regulated data or controls.

| Stage | Lens |
|---|---|
| Discover | Which frameworks and contractual commitments apply to this product and this data? |
| Define | Turn each applicable control into a requirement or constraint in the spec (for example, "all access to records is logged with user and time", "data stays in the EU"). |
| Design | Map each control to a component. Plan audit logging, access controls, encryption, retention, and data residency. Flag decisions that need sign-off from a human compliance owner. |
| Build | Implement controls as code where possible, so they're testable. Don't disable or bypass existing controls. |
| Verify | Test each control: that the audit log records the right events, that access is denied correctly, that retention jobs work. |
| Launch | Collect evidence: test results, configuration, approvals. Update the system's documentation where the framework requires it. |
| Sunset | Retention and deletion obligations; keep audit records for the required period even after the feature is gone. |

**Red flags:** removing or weakening an audit log; regulated data copied to a new store, log, or third party without review; manual steps in a control that should be automated; "we'll document it later."

**Gate evidence:** a control-to-component-to-test mapping; a list of decisions needing human sign-off.

An agent can implement and test controls, but compliance sign-off belongs to a human. Surface what needs approval rather than declaring something compliant.

---

## Accessibility Specialist (when legally required)

Accessibility is covered in the experience lens (`experience.md`). Load this note too when the product must meet a legal or contractual standard (for example, WCAG 2.2 AA, Section 508, the European Accessibility Act): treat the standard's criteria as acceptance criteria in Define, and record how each was verified.
