# T05 — Performance & Scalability

**Purpose.** Answer "can it hold 10× today's load without a rewrite, and where will it break first?" through data access, caching, statefulness, async work, concurrency, scaling axes, single points of failure, limits, and measured evidence.
**Start from.** Hot paths (top routes/jobs), DB access layer and schema indexes, cache config, queue/worker code, infra sizing, any load-test or APM evidence.
**Cross-links.** Cost implications go to T13 · availability and DR are T06 · load-test practice is T04 · frontend web performance details are in T19 · architecture-level trade-offs are T01.

## Checklist

### A. Hot paths and load assumptions
Identify the 5–10 request paths and jobs that carry most traffic or money. State the assumed load (users, RPS, data volume) from analytics, docs or infra sizing — tagged.

### B. Data access
N+1 query patterns (ORM lazy loading in loops) · missing indexes (compare the WHERE/JOIN/ORDER columns of hot queries with schema indexes) · unbounded queries and missing pagination · transaction scope and lock contention · connection pooling and pool sizes · large joins and aggregation in the app instead of the DB · read/write separation and replicas · query timeouts.

### C. Caching
Layers (CDN, HTTP, application, query, object) · invalidation strategy and correctness · cache stampede protection · TTLs vs data freshness requirements · caching of per-user or per-tenant data safely.

### D. Statefulness and horizontal scaling blockers
In-process sessions · local filesystem storage · in-memory queues or schedulers · sticky sessions · singletons with state · long-lived websockets without a broker · scheduled jobs that assume one instance.

### E. Async and background work
Queues and workers · idempotent job handlers · retries with backoff and dead-letter queues · backpressure · batch vs per-item processing · long-running requests that should be jobs · job monitoring.

### F. Concurrency and resource use
Thread/worker pools · event-loop blocking (sync I/O, CPU work on the loop) · memory growth and leaks · file descriptors and socket limits · graceful shutdown · CPU-heavy work without offloading.

### G. Scaling axes (AKF Scale Cube)
X — clone instances behind a balancer (what prevents it?) · Y — split by function/service (where are the natural seams?) · Z — shard by customer or data (does the schema support it?). State which axes are available today and what blocks the others.

### H. Single points of failure and limits
One DB instance, one region, one queue, one cache, one external API in the synchronous request path · rate limits and quotas of third parties on hot paths · payload size limits · timeouts (missing or too long) · storage growth without archival.

### I. Evidence
Load-test results, APM/tracing data, SLO history, incident history (→T06/T15). If none exists, say so and estimate from architecture, tagged `[Inferred]`/`[Speculative]`. Answer the 10× question explicitly: first bottleneck, second bottleneck, and the cheapest mitigation for each.

### J. Frontend performance (when a UI exists — details in T19)
Bundle size and code splitting, Core Web Vitals (LCP, INP, CLS), image handling, render-blocking resources, hydration cost, API waterfalls.

### K. Cost–performance trade-offs
Where performance is bought with over-provisioning, and where cheap performance wins are available (→T13 for the cost side).

## Signals and red flags
`SELECT *` inside loops · no indexes beyond primary keys · sessions in process memory on a multi-instance deployment · a cron job that would double-run with two instances · synchronous calls to a third party inside checkout · no timeouts on outbound HTTP · unbounded list endpoints · no load test on a product expecting launch spikes.

## Commands (shell) and no-shell fallbacks
- `EXPLAIN (ANALYZE, BUFFERS)` on hot queries · ORM query logging in tests · `k6 run` a smoke scenario if the app runs locally · language profilers (`py-spy`, `clinic`, `pprof`) · bundle analyzers (`source-map-explorer`, `webpack-bundle-analyzer`).
- No shell: static reading of queries/ORM usage vs schema indexes; infra sizing from IaC; PageSpeed Insights / Lighthouse on a public URL for frontend metrics; ask for APM dashboards and load-test reports via T15.

## Output
Findings `T05-###`, the hot-path table with the first-bottleneck analysis, the scale-cube assessment, the SPOF list, the 10× answer, score 1–5 with anchor, and open questions.
