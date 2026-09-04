# T06 — Operations, Reliability & Disaster Recovery

**Purpose.** Determine whether the system can be run safely: production-readiness in the SRE sense (SLOs, observability, alerting, runbooks, on-call, incident practice), deploy safety, backups and disaster recovery (RTO/RPO), resilience patterns, capacity, and operational single points of failure.
**Start from.** IaC and deployment manifests; CI/CD deploy workflows; logging/metrics/tracing initialization; backup configuration; runbooks/docs/ops; status page or incident records if any.
**Cross-links.** Resilience *testing* is T04 · performance limits are T05 · cost of over-provisioning is T13 · who can deploy (bus factor) is T10 · security monitoring is T02.

## Checklist

### A. Service objectives (production readiness review)
SLIs and SLOs defined (availability, latency, error rate, freshness) · error budgets and what happens when they burn · targets stated in docs vs implied by architecture (single AZ implies < 99.9%) · customer-facing SLAs (→T15) vs internal SLOs.

### B. Observability
Structured logging with correlation/request IDs · log levels and volume (cost, noise) · PII or secrets in logs · retention · metrics (RED for services, USE for resources) · distributed tracing (OpenTelemetry, vendor SDKs) · dashboards that answer "is it working now?" · health and readiness endpoints · synthetic checks · public or internal status page.

### C. Alerting and on-call
Alerts tied to symptoms (SLO burn) rather than every metric · actionable with a runbook link · paging vs ticket severity · noise level (alert fatigue) · on-call rotation and escalation · who is paged at 3am on a solo project (be honest about it).

### D. Runbooks, incident management, postmortems
Runbooks for deploy, rollback, restore, scale, rotate secrets, common failures · an incident process (declare, communicate, resolve, review) · blameless postmortems with tracked action items · incident history and recurring causes (→T15) · customer communication templates.

### E. Deploy safety
Automated pipeline with environments (dev/staging/prod parity) · canary or blue-green · feature flags for risky changes · database migrations with expand/contract (zero-downtime) · rollback tested and time-bounded · config changes reviewed and versioned · secrets rotation procedure · certificate and domain expiry monitoring · deploy frequency and who can deploy (→T10).

### F. Backups and disaster recovery
Automated backups for every store (DB, object storage, queues with durable state, search indexes) · encryption of backups · **tested restore** (when was the last restore drill?) · retention and point-in-time recovery · off-site/cross-region copies · **RTO/RPO stated** vs what the setup can actually deliver · multi-AZ/multi-region posture · DNS and failover plan · DR for third-party dependencies (payment provider outage, auth provider outage) · a written DR plan someone other than the author could follow.

### G. Resilience patterns in code
Timeouts on every outbound call · retries with exponential backoff and jitter · circuit breakers and bulkheads · idempotency keys for retried mutations · graceful degradation (feature off rather than page down) · queues absorbing bursts · dead-letter handling · health-based load balancing · graceful shutdown.

### H. Capacity and guardrails
Autoscaling policies and limits · headroom and quotas (cloud service limits, third-party rate limits) · cost guardrails and budgets (→T13) · capacity planning evidence.

### I. Operational single points of failure
Infrastructure: one instance, one AZ, one DB, one queue, one DNS provider. People: only one person can deploy, restore, or access the cloud account; credentials tied to a personal account (→T14). Vendors: one provider whose outage stops the product.

### J. Data-integrity operations
Reconciliation jobs (payments vs ledger, inventory) · audit logs for privileged actions · monitoring of scheduled jobs (did the nightly job run?) · queue-depth and lag alerts · data-quality checks (→T07).

## Signals and red flags
No backups or untested restores · RTO/RPO never stated on a revenue system · alerts that page on every error · no runbooks · deploys by SSH from a laptop · migrations that lock tables in production · no timeouts on outbound HTTP · the founder is the only one with cloud console access · expired-certificate incidents in history.

## Commands (shell) and no-shell fallbacks
- Read IaC and manifests for backup settings (RDS snapshots, PITR, `pgbackrest`, `velero`), multi-AZ flags, autoscaling; grep for OTel/Sentry/Datadog init, retry/circuit-breaker libraries (`resilience4j`, `tenacity`, `cockatiel`, `gobreaker`), timeouts on HTTP clients.
- No shell: same files via the GitHub UI; ask for dashboards, status page, the last restore drill date, the incident log and on-call setup via T15.

## Output
Findings `T06-###`, the production-readiness checklist result, the backup/DR table (store · backup · tested restore · RTO/RPO stated/implied), the SPOF list (infra, people, vendors), score 1–5 with anchor, and open questions.
