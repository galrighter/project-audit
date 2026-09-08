# T05 — Performance & Scalability

**Purpose.** Answer "can it hold 10× today's load without a rewrite, and where will it break first?" through data access, caching, statefulness, async work, concurrency, scaling axes, single points of failure, limits, and measured evidence.
**Start from.** Hot paths (top routes/jobs), DB access layer and schema indexes, cache config, queue/worker code, infra sizing, any load-test or APM evidence.
**Cross-links.** Cost implications go to T13 · availability and DR are T06 · load-test practice is T04 · frontend web performance details are in T19 · architecture-level trade-offs are T01.

## Checklist

### A. Hot paths and load assumptions
Identify the 5–10 request paths and jobs that carry most traffic or money. State the assumed load (users, RPS, data volume) from analytics, docs or infra sizing — tagged.

### B. Data access (reads)
N+1 query patterns (ORM lazy loading in loops) · missing indexes (compare the WHERE/JOIN/ORDER columns of hot queries with schema indexes) · unbounded queries and missing pagination · transaction scope and lock contention · connection pooling and pool sizes · large joins and aggregation in the app instead of the DB · read/write separation and replicas · query timeouts.

### C. Write amplification and self-inflicted load
Section B asks what the system reads for its users. This asks what it **writes, declares, invalidates or notifies that nobody requested** — load that no user-facing metric will ever show. Repeated DDL or schema self-healing on every boot · cache-invalidation storms (one write dropping a snapshot the next reader must rebuild) · log lines emitted at tick frequency · re-uploads and re-derivations of unchanged artifacts · polling loops whose normal answer is "nothing" · database triggers, schema-cache reloads, materialized-view refreshes and replication churn fired by routine writes · retry sweeps that re-select the same unresolvable rows forever.

**The identifying signal: an operation whose cost is `O(boots)`, `O(ticks)` or `O(deploys)` rather than `O(users)`.** Such an operation is invisible in load testing, scales with the *team's* activity rather than the product's, and is the commonest cause of a metered-resource surprise. For each one, state the rate and hand the multiplication to T13 (→T13 §B).

### D. The boot path as a recurring cost
On a PaaS that redeploys on every push, boots are frequent — **count them** (deploy history, restart logs, process-start counters) rather than assuming a boot is rare. Audit everything that runs before the app serves traffic and price it `× boot rate`, not once: migrations or schema self-healing · warm-up scans and cache priming · connection probing · rehydration of timers and state. The boot path raises three independent questions with three different fixes — *readiness* (how long until it answers), *safety* (concurrent boots during a zero-downtime rollover), and *cost* (what it consumes every time) — so answer all three; finding one is not finding the others.

### E. Caching
Layers (CDN, HTTP, application, query, object) · invalidation strategy and correctness · cache stampede protection · TTLs vs data freshness requirements · caching of per-user or per-tenant data safely · what each invalidation *costs the next reader* (a shared snapshot dropped on every write is a latency bomb, not a cache).

### F. Statefulness and horizontal scaling blockers
In-process sessions · local filesystem storage · in-memory queues or schedulers · sticky sessions · singletons with state · long-lived websockets without a broker · scheduled jobs that assume one instance.

### G. Async and background work
Queues and workers · idempotent job handlers · retries with backoff and dead-letter queues · backpressure · batch vs per-item processing · long-running requests that should be jobs · job monitoring.

### H. Concurrency and resource use
Thread/worker pools · event-loop blocking (sync I/O, CPU work on the loop) · memory growth and leaks · file descriptors and socket limits · graceful shutdown · CPU-heavy work without offloading.

### I. Scaling axes (AKF Scale Cube)
X — clone instances behind a balancer (what prevents it?) · Y — split by function/service (where are the natural seams?) · Z — shard by customer or data (does the schema support it?). State which axes are available today and what blocks the others.

### J. Single points of failure and limits
One DB instance, one region, one queue, one cache, one external API in the synchronous request path · rate limits and quotas of third parties on hot paths · payload size limits · timeouts (missing or too long) · storage growth without archival.

### K. Evidence
Load-test results, APM/tracing data, SLO history, incident history (→T06/T15). If none exists, say so and estimate from architecture, tagged `[Inferred]`/`[Speculative]`. Answer the 10× question explicitly: first bottleneck, second bottleneck, and the cheapest mitigation for each.

### L. Frontend performance (when a UI exists — details in T19)
Bundle size and code splitting, Core Web Vitals (LCP, INP, CLS), image handling, render-blocking resources, hydration cost, API waterfalls.

### M. Cost–performance trade-offs
Where performance is bought with over-provisioning, and where cheap performance wins are available (→T13 for the cost side).

## Signals and red flags
`SELECT *` inside loops · no indexes beyond primary keys · sessions in process memory on a multi-instance deployment · a cron job that would double-run with two instances · synchronous calls to a third party inside checkout · no timeouts on outbound HTTP · unbounded list endpoints · no load test on a product expecting launch spikes · **a near-100% buffer cache hit rate next to a large IO figure** (the IO is writes, and self-inflicted writes are the likeliest source) · **an idempotent-but-not-free operation on a startup or tick path** ("it's guarded by `IF NOT EXISTS`" answers correctness, not cost).

## Commands (shell) and no-shell fallbacks
- `EXPLAIN (ANALYZE, BUFFERS)` on hot queries · ORM query logging in tests · `k6 run` a smoke scenario if the app runs locally · language profilers (`py-spy`, `clinic`, `pprof`) · bundle analyzers (`source-map-explorer`, `webpack-bundle-analyzer`).
- **Ask the database what it actually did** — do not infer runtime behaviour from source alone. Postgres: `pg_stat_statements` ordered by `total_exec_time` **and separately by `calls`** · `pg_stat_database` (`temp_files`, `temp_bytes`, `blks_read` vs `blks_hit`, `xact_commit`) · `pg_stat_user_tables` (`seq_scan` vs `idx_scan`, `n_tup_*`) · `pg_statio_user_tables`. MySQL: `performance_schema.events_statements_summary_by_digest` · `sys.schema_tables_with_full_table_scans`. MongoDB: profiler level 1 · `$indexStats` · `$currentOp`. These views answer what static reading cannot: **a top query that no application file contains** (it came from an extension, a trigger, the ORM or the platform's own tooling), write volume no feature explains, and the cache-hit rate that decides whether an IO bill is reads or writes.
- No shell: static reading of queries/ORM usage vs schema indexes; infra sizing from IaC; PageSpeed Insights / Lighthouse on a public URL for frontend metrics; ask for APM dashboards, load-test reports and a statistics-view dump via T15.

## Output
Findings `T05-###`, the hot-path table with the first-bottleneck analysis, the scale-cube assessment, the SPOF list, the 10× answer, score 1–5 with anchor, and open questions. Every `O(boots)`/`O(ticks)` mechanism found under §C or §D is also handed to T13 with its rate.
