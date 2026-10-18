# Data Specialists

Load when the change touches stored data: schemas, migrations, new data stores, pipelines or ETL, backfills, analytics events, reporting, or large data volumes.

---

## Data Engineer / DBA

**Activate when:** a schema or migration changes; a new table, store, queue, or pipeline appears; a backfill is needed; queries run over large or growing tables.

| Stage | Lens |
|---|---|
| Define | Who consumes this data downstream? What are the freshness, volume, and retention requirements? Write them as constraints in the spec. |
| Design | Data model and ownership. Indexes and access patterns. Schema evolution using expand → migrate → contract, so old and new code can run at the same time. Idempotent pipelines. A backfill plan with batch size and resumability. |
| Build | Migrations that are reversible and safe to run online. Migration deploys separately from the code that depends on it. Idempotency keys on writes. Batched backfills that can resume after failure. |
| Verify | Run migrations up and down. Test against realistic volume, not ten rows. Reconcile row counts and check for nulls and duplicates. Confirm downstream consumers still work. |
| Launch | Migration order relative to code. Monitor backfill progress and database load. |
| Sunset | Stop writes, then confirm reads are zero, then archive, then drop. Never drop in the same release that stops using the data. |

**Red flags:** adding a NOT NULL column without a default to a large table; renaming a column in one step; a migration bundled with the code that needs it; a backfill that can't be safely re-run; an unbounded query on a growing table.

**Gate evidence:** migration tested up and down; the query plan for new hot queries; data-quality check output.

### Reviewer Injection — Data Engineer / DBA

Append to reviewer prompts when this specialist is active. Cap: 5 questions.

- Are schema changes reversible, and is the migration order safe (old code can run against the new schema before cutover)?
- Is any migration bundled with the code that depends on it (it must deploy first, separately)?
- Are new queries bounded — no full-table scans on growing tables without pagination or a limit?
- Is there a backfill plan, and is it idempotent (safe to re-run after failure)?
- Have migrations been tested both up and down?

---

## Analytics Engineer

**Activate when:** the spec has success signals that need measuring; events, metrics, dashboards, or reports are added or changed.

| Stage | Lens |
|---|---|
| Define | Turn each success signal into a named metric with a precise definition (numerator, denominator, time window). |
| Design | Event schema: names, properties, when each event fires. Avoid personal data in events unless it's required and approved. |
| Build | Instrument at the point the outcome actually happens (server-side for anything that matters). Version event schemas. |
| Verify | Trigger each event and confirm it arrives with the right properties, exactly once. |
| Evolve | Compare the metric to the target in the spec. |

**Red flags:** metrics nobody defined; client-only tracking for business-critical events; events renamed without a migration for existing dashboards.

**Gate evidence:** a captured sample of each new event.

### Reviewer Injection — Analytics Engineer

Append to reviewer prompts when this specialist is active.

- Is each success signal in the spec a named metric with a precise definition (numerator, denominator, time window)?
- Are events instrumented server-side for anything business-critical?
- Do any events contain personal data that wasn't explicitly approved?
