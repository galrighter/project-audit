# T13 — Cloud Cost & Sustainability

**Purpose.** Estimate what the system costs to run, where the money goes, what scales with usage, which optimizations are available, how mature the cost governance is (FinOps), and the environmental footprint — from IaC, manifests, config and vendor usage, with assumptions stated.
**Start from.** IaC (terraform/pulumi/CDK/serverless), Kubernetes manifests and resource requests, Dockerfiles, autoscaling config, storage lifecycle rules, paid-API clients, LLM usage, logging/monitoring vendors, CI minutes, **and any warning the providers themselves have already sent** (§B).
**Cross-links.** Performance trade-offs are T05 · capacity guardrails are T06 · unit economics combine with T12 pricing · LLM inference cost details are T16 · vendor contracts and actual bills come via T15.

## Checklist

### A. Cost drivers inventory
Compute (instances, containers, functions, sizes, counts, on-demand vs committed) · databases and caches (instance classes, storage, IOPS, replicas, backups) · object storage and tiers · egress and CDN · queues/streams · search/analytics services · logging and monitoring vendors (volume-priced) · CI/CD minutes and runners · paid third-party APIs (messaging, maps, search, data, email) · AI/LLM inference and embeddings (→T16) · licenses and SaaS seats visible in config.

### B. Metered dimensions — enumerate, then measure each one
A cost driver is a *service*. A **metered dimension** is what the provider actually counts, and a service usually has several. For each vendor, list every dimension it **bills or throttles on** — read it off the provider's own pricing/limits page, not from memory — then record a measured value against each. Storage is the dimension everyone remembers; the ones that get skipped, and therefore the ones that get exhausted first, are typically:

| service | remembered | routinely missed |
|---|---|---|
| managed database | storage (GB) | **disk IO / throughput and its burst budget**, CPU-seconds, connections, row reads, request count |
| object storage | bytes stored | operations (PUT/GET/LIST), egress, early-deletion minimums |
| serverless functions | invocations | GB-seconds, concurrency ceiling, cold-start duration |
| CI/CD | minutes | concurrency, runner class multipliers, storage for artifacts and caches |
| LLM APIs | input/output tokens | **cache writes**, thinking tokens, per-tool surcharges (web search), retries |

Three rules:
- **A dimension the provider's dashboard names and the audit did not measure is itself a finding.** Put it in the "could not determine" list; never let the dimensions you did measure imply the set was complete.
- **Measure consumption against the quota, not in absolute units.** "271 GB written" says nothing on its own; "271 GB against a burst budget that refills at X/hour" is the finding.
- **Read consumption from the system where it exposes it** — database statistics views (→T05 commands), usage/billing APIs, quota endpoints — rather than pricing it from IaC. IaC gives the bill you *should* get; the usage views give the one you *will*.

### C. Provider warnings already received
Ask for, and account for, everything the vendors have said in the last ~90 days: quota and throttling warnings, deprecation notices, billing alerts, incident mail, dashboard banners. **A provider that has already warned the owner has done a measurement for you, on the exact dimension that matters, for free.** Every such warning must map to a finding that explains it — or to an explicit statement that the audit could not reproduce it. An unexplained warning is a red flag in its own right. (Self-audit/onboarding: ask the owner directly, and check whether the foundation pass already recorded them. Due-diligence: this belongs in the T15 request list.)

### D. Fixed vs variable
Which costs are flat (reserved DB, base instances) and which scale with users/requests/data (functions, egress, per-message, per-token). Identify costs that scale faster than revenue (→T12 unit economics). Add a third column the usual split omits: costs that scale with **neither** — with boots, deploys, ticks or cron cadence (→T05 §C/§D). These grow with engineering activity, are invisible to load testing, and are the ones nobody has an owner for.

### E. Estimate
Produce a monthly cost band (low/expected/high) with every assumption listed (region, sizes, traffic, data volume). Use `infracost breakdown` on Terraform when possible; otherwise price from IaC/manifests by hand. Compare with any budget or billing evidence (→T15).

### F. Optimization opportunities (with estimated savings)
Right-sizing over-provisioned instances and DBs · idle or orphaned resources (unattached volumes, old snapshots, unused load balancers, dev environments running 24/7) · storage tiering and lifecycle rules · log volume and retention (often the surprise bill) · egress reduction (CDN, compression, region co-location) · commitment discounts (reserved/savings plans/committed use) once usage is stable · autoscaling and scale-to-zero for bursty workloads · caching to cut DB and API calls (→T05) · batching and cheaper models for AI calls (→T16) · CI caching and runner sizing · **eliminating self-inflicted load** (→T05 §C) — usually the cheapest saving available, because it removes work rather than buying capacity for it.

### G. Cost governance (FinOps maturity)
Tagging/labels for allocation · budgets and alerts · anomaly detection · cost visibility per environment/team/feature · a named owner · regular review evidence. Absence of budgets and alerts on pay-per-use services is a finding (runaway-cost risk).

### H. Environment hygiene
Number of environments and their sizing relative to production · preview environments' lifecycle · test data volume.

### I. Sustainability
Region carbon intensity (choose low-carbon regions where latency allows) · right-sizing and scale-to-zero as the main lever · efficient compute (ARM instances, newer generations) · data-retention minimization · scheduled off-hours for non-production · cloud provider sustainability reports/tools if referenced. Keep this proportionate: for most small projects it is one paragraph.

## Signals and red flags
No budgets or alerts on serverless/LLM usage · logs retained forever at debug level · dev/staging sized like production · unbounded storage growth · a per-request paid API on the hottest path · no tagging, so nobody knows what costs what · reserved capacity for a workload that scales to zero · **a vendor quota warning or throttle notice that no finding accounts for** · **storage measured on a database whose IO and compute were not** · **a resource that is metered but appears in no monitoring, alert or report** (nobody will see the next exhaustion either).

## Commands (shell) and no-shell fallbacks
- `infracost breakdown --path .` (Terraform) · `kubectl top` / resource requests vs limits from manifests · `aws-nuke --dry-run`/cloud CLIs for orphaned resources if credentials exist · count log statements and levels.
- Usage and quota APIs where credentials exist — GitHub `/settings/billing/actions`, cloud cost explorers, database and platform usage endpoints — plus the database's own statistics views (→T05 commands), which are usually readable when billing APIs are not.
- No shell: read IaC and manifests via GitHub; price by hand from provider calculators; ask for the last three months of bills, budgets, alerts **and provider warning mail** via T15.

## Output
Findings `T13-###`, the cost-driver table (driver · fixed/variable/per-boot · metered dimension · measured value vs quota · evidence), the list of metered dimensions that could **not** be measured, the monthly cost band with assumptions, the optimization table (action · estimated saving · effort · risk), the FinOps-maturity checklist result, the sustainability note, score 1–5 with anchor (cost efficiency and governance), and open questions.
