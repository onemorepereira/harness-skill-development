# Persona Lenses

Each persona is a set of questions and a list of failures it is good at catching. Apply them silently: the user should see better decisions, not role-play. Use the ones the current stage calls for; skip any that don't apply to the work.

These are the **core personas**, one or more per stage, who own that stage's decisions. **Specialists** (data engineer, SRE, compliance officer, QA automation, beta tester, and others) live in `specialists/` and are pulled in by what the change touches; see Step 2 in SKILL.md. Where a core persona here overlaps a specialist (for example, the Security & Privacy Reviewer and `specialists/security.md`), this file is the quick check and the specialist file is the deep one.

## Contents

- Internal personas (who does the work), by stage
- Customer personas (who the work is for)
- Quick map: stage → personas

---

## Internal personas

### Product Manager — Discover, Define, Evolve
Owns *why* and *what*.
- What problem are we solving, for whom, and how do we know it's real?
- What does success look like, and how would we observe it?
- What are we deliberately *not* doing?
- What's the smallest version that delivers the core value?
- **Catches:** solutions in search of a problem, scope creep, unmeasurable goals, missing non-goals.

### UX Researcher — Discover
Owns understanding of the user.
- What is the user trying to accomplish, and what do they do today instead?
- Where is the evidence: issues, logs, support tickets, existing usage in the code?
- Which assumptions about the user are we least sure of?
- **Catches:** designing for an imagined user, ignoring current workarounds.

### UX Designer — Design
Owns how it feels to use.
- What are all the states: empty, loading, partial, error, success, no permission, offline?
- What does the copy actually say? Do errors say what happened and what to do next?
- Is it accessible (keyboard, screen reader, contrast, focus order)?
- For APIs, CLIs, and libraries: are names obvious, defaults safe, errors actionable?
- **Catches:** happy-path-only design, placeholder copy, inaccessible UI, confusing APIs.

### Software Architect — Design
Owns structure and trade-offs.
- How does this fit existing patterns? What would a maintainer expect to find?
- What are two or three viable options, and why this one?
- What are the contracts (interfaces, schemas, events), and who depends on them?
- How does it fail, and how do we find out it failed?
- What's hard to reverse? What's the migration and rollback story?
- **Catches:** novel patterns that fragment the codebase, hidden coupling, missing failure modes, one-way doors taken casually.

### Security & Privacy Reviewer — Design, Verify
Owns trust.
- What's the trust boundary? What input is untrusted?
- Who can do what (authentication, authorization), and is it checked server-side?
- What sensitive data is touched, logged, stored, or sent elsewhere?
- What happens with injection, replay, oversized input, or a malicious user?
- **Catches:** missing authorization checks, secrets in logs, injection, over-collection of data.

### Senior Engineer — Build
Owns the code.
- What's the thinnest vertical slice that proves the riskiest part?
- What do neighboring files do, and am I matching them?
- Is this the simplest code that meets the criteria? Would a newcomer understand it?
- Are tests written alongside, and do they test behavior rather than implementation?
- **Catches:** big-bang changes, clever code, convention drift, untested logic.

### QA Engineer — Verify
Owns confidence.
- Is every acceptance criterion proven by something I ran?
- What breaks it: empty, huge, concurrent, unicode, slow network, re-run, upgrade?
- Does the whole suite still pass?
- **Catches:** "works on the happy path", regressions elsewhere, untested edge cases.

### Independent Reviewer — Verify
Owns a fresh look. Sees only the spec and the diff, not the reasoning behind them.
- Does the diff do what the spec says, and nothing it doesn't?
- What would I push back on in code review?
- **Catches:** blind spots the author can't see, scope creep, unclear code.

### Release Engineer / SRE — Launch, Evolve
Owns running it safely.
- How does this roll out, and how does it roll back?
- What's the migration order? Is there a window where old and new code coexist?
- What metrics and alerts tell us it's working or broken?
- What happens under load or when a dependency is down?
- **Catches:** irreversible deploys, blind launches, migrations that lock tables.

### Product Marketing / DevRel — Launch
Owns how people learn about it.
- Can a user understand what changed and why it matters in one sentence?
- Are docs, CHANGELOG, and upgrade notes updated?
- Does the PR description let a reviewer understand it quickly?
- **Catches:** features nobody can find, undocumented breaking changes.

### Support — Launch, Sunset
Owns the user after launch.
- What will users get confused by? What are the known limitations?
- When something goes wrong, can support diagnose it from logs and messages?
- **Catches:** cryptic errors, undocumented limits, stranded users.

### Growth PM / Platform Engineer — Evolve
Owns improving what exists.
- Is it meeting the original success signals? Where's the constraint?
- What workarounds have accumulated, and what need do they reveal?
- What debt is now blocking growth?
- **Catches:** optimizing the wrong thing, piling features on a weak base.

### Portfolio PM / Compliance — Sunset
Owns ending things well.
- Who depends on this, and what's their path off it?
- What are the data retention and deletion obligations?
- What's the deprecation timeline, and how will people be told?
- **Catches:** breaking hidden dependents, orphaned data, surprise removals.

---

## Customer personas

Who the work is for changes what "good" means. Identify the main audience in Discover and keep it in mind through Design and Verify.

The product stage inferred in Step 0 of SKILL.md determines which adopter type is the primary audience. Introduction-stage products primarily serve early adopters who want capability over polish; Growth-stage products serve the early majority who want reliability and onboarding; Maturity-stage products serve the late majority who want no surprises and backward compatibility.

| Adopter type | Typical product stage | Wants | Implications for the work |
|---|---|---|---|
| **Innovators / design partners** | Development | Early access, influence | Rough edges are OK; feedback loops and instrumentation matter most |
| **Early adopters** | Introduction | Capability and advantage | Power and flexibility over polish; good escape hatches |
| **Early majority** | Growth | Proven, reliable, integrates with what they use | Sensible defaults, onboarding, docs, reliability, integrations |
| **Late majority** | Maturity | Familiar, cheap, low risk | Backward compatibility, consistency, no surprises, performance |
| **Laggards / loyalists** | Decline | Not to be disrupted | Clear migration paths, long notice, data export |

For developer-facing work, the "customer" may be another engineer, a downstream service, or a future maintainer. The same table applies: a public API used by many teams is a late-majority audience even if the product is new.

---

## Quick map: stage → personas

Core personas own the stage. The specialist column shows where specialists most often contribute when their domain is triggered; it isn't a list to apply every time.

| Stage | Core personas | Specialists that often contribute |
|---|---|---|
| Discover | Product Manager, UX Researcher | Compliance Officer (which rules apply), Beta Tester (existing feedback) |
| Define | Product Manager | QA Automation (testable criteria), Compliance Officer (controls as requirements), Performance Engineer (budgets), Analytics Engineer (metrics), SRE (SLOs) |
| Design | UX Designer, Software Architect, Security & Privacy Reviewer | Data Engineer, Infrastructure Engineer, SRE, AppSec, API Designer, Accessibility, ML Engineer, FinOps |
| Build | Senior Engineer | Data Engineer (migrations), DevOps (pipelines), Infrastructure (IaC), Frontend/Mobile, QA Automation |
| Verify | QA Engineer, Security & Privacy Reviewer, Independent Reviewer | Exploratory Tester, Beta Tester (zero-context walkthrough), Performance Engineer, AppSec, AI Safety Reviewer |
| Launch | Release Engineer/SRE, Product Marketing/DevRel, Support | DevOps, Technical Writer, Compliance Officer (evidence) |
| Evolve | Growth PM, Platform Engineer, SRE | Analytics Engineer, FinOps, QA Automation (regression tests) |
| Sunset | Portfolio PM, Support, Compliance | Data Engineer (archive and drop), Infrastructure (teardown), Privacy Engineer (deletion) |
