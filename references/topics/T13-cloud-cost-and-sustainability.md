# T13 — Cloud Cost & Sustainability

**Purpose.** Estimate what the system costs to run, where the money goes, what scales with usage, which optimizations are available, how mature the cost governance is (FinOps), and the environmental footprint — from IaC, manifests, config and vendor usage, with assumptions stated.
**Start from.** IaC (terraform/pulumi/CDK/serverless), Kubernetes manifests and resource requests, Dockerfiles, autoscaling config, storage lifecycle rules, paid-API clients, LLM usage, logging/monitoring vendors, CI minutes.
**Cross-links.** Performance trade-offs are T05 · capacity guardrails are T06 · unit economics combine with T12 pricing · LLM inference cost details are T16 · vendor contracts and actual bills come via T15.

## Checklist

### A. Cost drivers inventory
Compute (instances, containers, functions, sizes, counts, on-demand vs committed) · databases and caches (instance classes, storage, IOPS, replicas, backups) · object storage and tiers · egress and CDN · queues/streams · search/analytics services · logging and monitoring vendors (volume-priced) · CI/CD minutes and runners · paid third-party APIs (messaging, maps, search, data, email) · AI/LLM inference and embeddings (→T16) · licenses and SaaS seats visible in config.

### B. Fixed vs variable
Which costs are flat (reserved DB, base instances) and which scale with users/requests/data (functions, egress, per-message, per-token). Identify costs that scale faster than revenue (→T12 unit economics).

### C. Estimate
Produce a monthly cost band (low/expected/high) with every assumption listed (region, sizes, traffic, data volume). Use `infracost breakdown` on Terraform when possible; otherwise price from IaC/manifests by hand. Compare with any budget or billing evidence (→T15).

### D. Optimization opportunities (with estimated savings)
Right-sizing over-provisioned instances and DBs · idle or orphaned resources (unattached volumes, old snapshots, unused load balancers, dev environments running 24/7) · storage tiering and lifecycle rules · log volume and retention (often the surprise bill) · egress reduction (CDN, compression, region co-location) · commitment discounts (reserved/savings plans/committed use) once usage is stable · autoscaling and scale-to-zero for bursty workloads · caching to cut DB and API calls (→T05) · batching and cheaper models for AI calls (→T16) · CI caching and runner sizing.

### E. Cost governance (FinOps maturity)
Tagging/labels for allocation · budgets and alerts · anomaly detection · cost visibility per environment/team/feature · a named owner · regular review evidence. Absence of budgets and alerts on pay-per-use services is a finding (runaway-cost risk).

### F. Environment hygiene
Number of environments and their sizing relative to production · preview environments' lifecycle · test data volume.

### G. Sustainability
Region carbon intensity (choose low-carbon regions where latency allows) · right-sizing and scale-to-zero as the main lever · efficient compute (ARM instances, newer generations) · data-retention minimization · scheduled off-hours for non-production · cloud provider sustainability reports/tools if referenced. Keep this proportionate: for most small projects it is one paragraph.

## Signals and red flags
No budgets or alerts on serverless/LLM usage · logs retained forever at debug level · dev/staging sized like production · unbounded storage growth · a per-request paid API on the hottest path · no tagging, so nobody knows what costs what · reserved capacity for a workload that scales to zero.

## Commands (shell) and no-shell fallbacks
- `infracost breakdown --path .` (Terraform) · `kubectl top` / resource requests vs limits from manifests · `aws-nuke --dry-run`/cloud CLIs for orphaned resources if credentials exist · count log statements and levels.
- No shell: read IaC and manifests via GitHub; price by hand from provider calculators; ask for the last three months of bills, budgets and alerts via T15.

## Output
Findings `T13-###`, the cost-driver table (driver · fixed/variable · estimated monthly · evidence), the monthly cost band with assumptions, the optimization table (action · estimated saving · effort · risk), the FinOps-maturity checklist result, the sustainability note, score 1–5 with anchor (cost efficiency and governance), and open questions.
