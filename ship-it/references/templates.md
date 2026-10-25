# Artifact Templates

Short is better. Delete sections that don't apply instead of writing "N/A". These are living documents: update them when decisions change during the build.

## Contents

- spec.md (Define)
- design.md (Design), with ADR format and implementation plan section
- launch.md (Launch)
- deprecation.md (Sunset) — legacy format
- evolution-brief.md (Evolve)
- dependent-inventory.md (Sunset)
- docs/security/ evidence files (Offensive Security Engineer active)
- Handoff report (end of every track)

---

## spec.md

```markdown
# <Feature name> — Spec

**Track:** Patch | Quick | Standard | Full   **Product stage:** Introduction | Growth | Maturity | Decline
**Status:** Draft | Agreed | Built | Superseded   **Last updated:** <date>
**Superseded by:** <link to newer spec, if Status is Superseded>

## Problem
<One or two sentences. Who has the problem, and what it costs them. No solution here.>

## Users
<Primary audience and adopter type. Secondary audiences, if any.>

## Goals
- <Outcome, not activity>

## Non-goals
- <What this deliberately won't do, and why if not obvious>

## User stories
- As a <user>, I want <capability> so that <benefit>.

## Acceptance criteria
- [ ] AC1: Given <context>, when <action>, then <observable result>.
- [ ] AC2: ...

## Success signals
<How we'll know it worked after launch: a metric, a behavior, a reduction in tickets.>

## Constraints
<Performance budgets, compatibility, compliance, deadlines, tech constraints.>

## Assumptions and open questions
- Assumption: <what we're assuming, and what changes if it's wrong>
- Open: <question> — owner / default if unanswered
```

---

## design.md

```markdown
# <Feature name> — Design

**Spec:** ./spec.md   **Last updated:** <date>
**Status:** Draft | Current | Superseded   **Superseded by:** <link, if Status is Superseded>

<!-- Living sections (update in place during Evolve): Overview, Experience, Architecture, Data and contracts, Failure modes, Security and privacy, Performance, Migration and rollback, Traceability, Risks and spikes, Implementation plan -->
<!-- Reference sections (append-only; do not edit past entries): Decisions / ADRs -->

## Overview
<A paragraph a reviewer can read in 30 seconds: the approach and why.>

## Experience
<Design review (UI work): the approved wireframe or mockup path, the alternatives shown, and the user's steering; or "proceeding unapproved" with the open points; or the skip line.
Flows and states (empty, loading, error, success, edge). Real copy for key messages.
For APIs/CLIs: signatures, defaults, error messages, examples.>

## Architecture
<Components touched or added and how they interact. A small diagram if it helps.
Note which existing patterns this follows.>

## Data and contracts
<Schema changes, API contracts, events. Who depends on them.>

## Decisions
### ADR-1: <Decision title>
- **Context:** <the forces at play>
- **Options:** A) ... B) ... C) ...
- **Decision:** <chosen option>
- **Why:** <reasoning, trade-offs accepted>
- **Reversibility:** easy | costly | one-way
- **Sources:** <authoritative docs, changelogs, or issues this decision relies on — link + version or access date; or "Unverified — see assumption N">

## Failure modes
| Failure | Detection | Behavior | Recovery |
|---|---|---|---|

## Security and privacy
<Trust boundaries, authz checks, sensitive data handling.>

## Performance
<Budgets and how they'll be checked.>

## Migration and rollback
<Order of operations, coexistence of old and new, how to undo.>

## Traceability
| Acceptance criterion | Component(s) | Test(s) |
|---|---|---|
| AC1 | | |

## Risks and spikes
<Riskiest assumption and how it's being tested.>

## Implementation plan

<!-- ship-it: before dispatching the plan reviewer, extract this section
     and pass it alongside spec.md only. Do not pass the full design.md. -->

1. <Slice 1 — riskiest first, independently testable>
2. ...
```

---

## launch.md

```markdown
# <Feature name> — Launch

## Rollout
- Mechanism: <flag / staged % / direct>
- Order: <migrations, services, clients>
- Compatibility window: <how old and new coexist>

## Rollback
<Exact steps, and whether data changes are reversible.>

## Observability
- Metrics: <tied to success signals from spec.md>
- Logs: <key events>
- Alerts: <tied to failure modes, with thresholds>

## Communication
- [ ] PR description
- [ ] CHANGELOG / release notes
- [ ] User or API docs
- [ ] Upgrade / migration notes (if breaking)

## Known limitations
- <What it doesn't do yet; what users might trip on>

## Post-launch checks
<What to look at, when, and what result would mean rollback.>
```

---

## evolution-brief.md

```markdown
# <Product/Feature> — Evolution Brief

**Status:** Draft | Reviewed   **Date:** <date>

## Current vs. target

| Success signal (from spec) | Target | Actual | Source |
|---|---|---|---|

## Constraint
<One sentence: what is preventing the product from meeting its success signals.
Evidence: where this comes from — a specific metric, an incident, a pattern in the code, user feedback.>

## Proposed change
<What would address the constraint. Scope only — no solution design here.>

## Risk if wrong
<What changes if the constraint diagnosis is incorrect.>
```

---

## dependent-inventory.md

```markdown
# Deprecating <thing> — Dependent Inventory and Removal Plan

## Why
<Reason and what replaces it.>

## Dependent inventory

| Dependent | Type (code/API/job/doc/user/data) | Owner | Migration path | Status |
|---|---|---|---|---|

## Removal sequence
1. Stop new writes — <date>
2. Confirm reads are zero (check logs/metrics) — <date>
3. Archive data — <date>
4. Remove code and routes (not just links) — <date>
5. Drop storage — <date>
6. Revoke credentials and keys — <date>

## Migration guide
<Step-by-step for users of the old thing. Published before step 4.>

## Retention obligations
<What must be kept, for how long, and why (compliance framework if applicable).>

## Verification before removal
<How we confirm nothing still calls it: log query, metric, code search command.>
```

---

## deprecation.md (legacy format)

```markdown
# Deprecating <thing>

## Why
<Reason, and what replaces it.>

## Dependents inventory
| Dependent | Type (code/API/job/user/data) | Owner | Migration path | Status |
|---|---|---|---|---|

## Timeline
1. Announce + deprecation warnings — <date>
2. Disable behind flag (reversible) — <date>
3. Remove code — <date>
4. Delete data (if applicable) — <date>

## Migration guide
<Step-by-step for users of the old thing.>

## Data
<Export, retention, deletion obligations.>

## Verification before removal
<How we confirm nothing still uses it: logs, metrics, code search.>
```

---

## docs/security/ evidence files

Starter templates for the living security evidence folder, created and kept current by the Offensive Security Engineer (`references/specialists/security.md`). Each run updates only the files its change affects. Field names here match that specialist's evidence contract exactly.

### docs/security/README.md

```markdown
# Security evidence

What an auditor or pentester should read, and in what order. Inventory started <date>; covers entry points touched by ship-it runs since then — not a full-repo baseline.

| File | What it holds | Last updated |
|---|---|---|
| threat-model.md | Threats per component and their mitigations | <date> |
| attack-surface.md | Inventory of entry points and their exposure | <date> |
| findings.md | Every finding, its status, and accepted-risk decisions | <date> |
| test-log.md | Dated record of every security test run | <date> |
```

### docs/security/threat-model.md

```markdown
# Threat model

## <Component or entry point>
- **Threats (STRIDE):** <spoofing / tampering / repudiation / info disclosure / DoS / elevation — the ones that apply>
- **Mitigation:** <how each is addressed>
- **Covered by:** <link to the test or test-log.md entry that proves it>
```

### docs/security/attack-surface.md

```markdown
# Attack surface

| Entry point | Exposure (public/internal) | Auth required | Added | Removed | Next pentest scope |
|---|---|---|---|---|---|
| <method + path, port, bucket, webhook…> | public | yes | <date> | — | yes |
```

### docs/security/findings.md

```markdown
# Findings register

Severity: Critical / High / Medium / Low / Info. Unfixed findings are redacted (class + component + observed outcome, no reproduction). Reproduction steps are added to test-log.md only after status is `fixed`.

| ID | Source | Severity | Component | Class | Status | Found | Accepted: reason / owner / review-by | Fixed: commit / re-test date |
|---|---|---|---|---|---|---|---|---|
| F-001 | ship-it probe | High | <component> | IDOR | open | <date> | — | — |
```

### docs/security/test-log.md

```markdown
# Security test log

Append-only; newest at the bottom.

## <date> — <change / feature slug>
- **Scope note:** <target, tests run, destructive tests excluded — or "self-started, none needed">
- **Target:** <url or command>
- **Tools:** <tool + version installed / removed>
- **Tests run:** <WSTG IDs and plain description>
- **Results:** <per test: pass / finding (F-00N) / not probed — reason>
- **CVE triage:** <if any finding carried a CVE: KEV + EPSS lookup results, or "not checked — reason">
```

---

## Handoff report

```markdown
**What shipped:** <one or two sentences>

**Verification**
- [x] AC1 — <how it was proven: test name, command run, screenshot>
- [x] AC2 — ...

**Exercised live:** <what was run end to end, where, and what happened — or why no real environment was reachable>

**Security scans:** <per category: tool + version, command, result — or "unscanned (no tool available)"; omit if the security specialist was not active>

**Pentest probe:** <if the Offensive Security Engineer was active: findings by severity (redacted for anything not fixed), each fixed / accepted / open; categories listed "not probed" with reason; or omit>

**docs/security/ updated:** <the evidence files this change touched — or omit if the Offensive Security Engineer was not active>

**Gate history:** <per gate: review type, number of passes, blockers found and how each was resolved>

**Specialists consulted:** <e.g., Data Engineer, AppSec — with their gate evidence, or a note on what was skimmed>

**Decisions and deviations:** <changes from the design, and why>

**Follow-ups:** <deferred work, refactors noted, ideas outside scope>

**Risks to watch:** <what to monitor after launch>
```
