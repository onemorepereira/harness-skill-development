# Platform Specialists

Load when the change touches infrastructure, deployment, CI/CD, runtime configuration, scaling, reliability, or cloud cost: new services, queues, caches, cron jobs, containers, infrastructure as code, environment variables, or deployment pipelines.

---

## Infrastructure Engineer

**Activate when:** new compute, storage, networking, queues, or caches; infrastructure-as-code changes; new environments or regions.

| Stage | Lens |
|---|---|
| Define | Expected load, growth, latency, and availability targets. Regions and data residency. |
| Design | Topology, capacity, network boundaries, managed vs self-run components. Everything declared as code. Blast radius if a component fails. |
| Build | Infrastructure as code that follows the repo's modules and naming. No manual console changes. Tags for ownership and cost. |
| Verify | Plan/diff output reviewed: nothing destroyed or replaced unexpectedly. Applied in a non-production environment first. |
| Launch | Apply in order; confirm health before shifting traffic. |
| Sunset | Tear down resources and their DNS, IAM roles, secrets, and alarms. |

**Red flags:** a plan that replaces a stateful resource; manual changes outside code; resources without owners or tags; public network exposure by default.

**Gate evidence:** reviewed plan/diff output.

---

## DevOps / Platform Engineer

**Activate when:** CI/CD pipelines, build tooling, containers, environment configuration, feature-flag systems, or developer tooling change; or a new service needs a pipeline.

| Stage | Lens |
|---|---|
| Design | How does this get built, tested, and deployed? What configuration differs per environment? Does it need a feature flag? |
| Build | Pipeline as code. Reproducible builds with pinned versions. Config through environment, not branches. Fast feedback: lint and unit tests early, slow suites later. |
| Verify | The pipeline runs green from a clean checkout. Deploy to a non-production environment works end to end. |
| Launch | Deploy automation, flag configuration, rollback command tested. |
| Sunset | Remove pipelines, flags, and config for removed features. Stale flags are debt. |

**Red flags:** steps that only work on someone's machine; secrets in pipeline files; flags with no removal plan; skipping tests to ship.

**Gate evidence:** a green pipeline run; a tested rollback.

---

## Site Reliability Engineer (SRE)

**Activate when:** the change is on a critical user path, adds a dependency, changes load characteristics, or introduces a new service. Also on any Full-track work.

| Stage | Lens |
|---|---|
| Define | Service-level objectives: what availability and latency do users need? What's the error budget? |
| Design | Failure modes for each dependency: timeouts, retries with backoff, circuit breakers, graceful degradation. What alerts, and on what symptom? |
| Build | Timeouts on every network call. Structured logs with request IDs. Health checks that reflect real readiness. |
| Verify | Simulate a dependency failure and slowness. Load test if the change affects throughput. |
| Launch | Staged rollout, dashboards ready before launch, alerts tied to user-facing symptoms, a runbook for the likely failures. |
| Evolve | Review incidents and SLO burn; fix the causes, not just the symptoms. |

**Red flags:** network calls without timeouts; retries without backoff (retry storms); alerts on causes nobody can act on; launching without a dashboard.

**Gate evidence:** failure-injection results; links to dashboards and alerts; runbook.

---

## FinOps (Cost)

**Activate when:** the change adds paid resources, scales with usage, calls paid APIs (including AI models), or moves large amounts of data.

| Stage | Lens |
|---|---|
| Define | Cost budget or cost per unit (per user, per request). |
| Design | Estimate cost at expected and 10× scale. Caching, batching, right-sizing, storage tiers. |
| Verify | Measure actual usage per operation against the estimate. |
| Evolve | Track cost per unit over time. |

**Red flags:** costs that grow faster than usage; unbounded paid API calls in loops; no budget alerts.

**Gate evidence:** a cost estimate at expected and 10× scale.
