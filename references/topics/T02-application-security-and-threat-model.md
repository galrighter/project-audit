# T02 — Application Security & Threat Model

**Purpose.** Find real, traceable security weaknesses and model the threats the system actually faces: assets, entry points, trust boundaries, STRIDE per boundary, OWASP Top 10:2025 and ASVS-style verification, secrets, multi-tenancy isolation, and cloud/infra security from IaC. Every finding needs a source→sink trace.
**Start from.** Foundation pointers for T02; routes/controllers; auth middleware; DB access layer; .env.example; IaC; CI workflows.
**Cross-links.** Dependencies and CI supply chain are T03 · data classification and PII flows are T07 · LLM/agent-specific threats are T16 · incident history and pen-test reports come via T15 · runtime hardening/DR is T06.

## Checklist

### A. Assets, entry points, trust boundaries
- **Assets:** data classes (PII, credentials, payment data, health, secrets), money flows, privileged actions, reputation-bearing content.
- **Entry points:** HTTP routes, GraphQL resolvers, webhooks, message queues, cron/jobs, CLI, admin UIs, file uploads, third-party callbacks.
- **Trust boundaries:** internet↔app, app↔DB, app↔third parties, tenant↔tenant, user↔admin, CI↔production, developer laptop↔repo.

### B. Data-flow diagram (textual is fine)
Actors → processes → stores → external services, with boundaries marked. Note where validation and authorization happen relative to each boundary crossing.

### C. STRIDE per boundary crossing, plus abuse cases
For each crossing, ask: Spoofing (who can pretend?), Tampering (what can be altered in transit/at rest?), Repudiation (is there an audit trail?), Information disclosure (what leaks?), Denial of service (what exhausts?), Elevation of privilege (how does a user become admin?). Add abuse cases the business cares about: account takeover, payment/refund abuse, scraping, promo abuse, referral fraud, spam through notification systems. Build attack trees for the top three assets.

### D. OWASP Top 10:2025
A01 Broken access control (incl. SSRF) · A02 Security misconfiguration · A03 Software supply chain failures (→T03) · A04 Cryptographic failures · A05 Injection · A06 Insecure design · A07 Authentication failures · A08 Software and data integrity failures · A09 Logging and monitoring failures · A10 Mishandling of exceptional conditions. For each: where would it occur in this codebase, and is there evidence it does?

### E. ASVS 5.0-style verification areas
- **Authentication:** password storage (argon2/bcrypt/scrypt), MFA, session/token lifetimes and rotation, reset and verification flows, brute-force protection, OAuth/OIDC correctness (state, PKCE, redirect allow-lists).
- **Access control:** object-level checks (IDOR), function-level checks, tenant scoping, ownership on every mutation, admin paths, deny-by-default.
- **Input/output:** parameterized queries, output encoding, XSS (incl. `dangerouslySetInnerHTML`/`innerHTML`), SSRF, path traversal, template injection, unsafe deserialization, file upload validation and storage, prototype pollution.
- **Cryptography:** approved algorithms, randomness sources, key storage and rotation, TLS configuration, JWT algorithm pinning.
- **Data protection:** PII at rest and in transit, PII in logs and error messages, caching of sensitive responses.
- **Configuration:** security headers, CORS, debug endpoints, default credentials, verbose errors, exposed metrics/health endpoints.
- **Business-logic abuse:** rate limits, replay, race conditions on balance/inventory, quota bypass.
- **API security:** auth on every endpoint, mass assignment, pagination limits, GraphQL depth/complexity limits.

### F. Secrets
Search code **and history** for API keys, tokens, private keys, connection strings, `.env` files. A secret in history counts even if removed later (rotation needed). Check CI logs and workflow files for echoed secrets, and Dockerfiles for baked-in credentials.

### G. Multi-tenancy isolation (when tenants exist)
Tenant identifier on every query and mutation; row-level security or an enforced scoping layer; background jobs and caches scoped per tenant; storage paths and signed URLs scoped; cross-tenant tests present; admin "impersonation" audited.

### H. Cloud and infrastructure security (from IaC, manifests, compose)
IAM least privilege (wildcard actions/resources), public buckets/blobs, security groups open to 0.0.0.0/0, unencrypted stores or snapshots, secrets manager vs plaintext env, audit logging (CloudTrail equivalents) enabled, network segmentation (DB not internet-reachable), exposed admin ports, containers running as root, image provenance (→T03), Kubernetes RBAC and network policies.

### I. Security process signals
SECURITY.md and disclosure path · dependency alerts enabled · SAST/secret scanning in CI · branch protection · pen-test or bug-bounty evidence (→T15) · security training/ownership · incident history (→T06/T15).

## Evidence discipline (non-negotiable)
Report a vulnerability only with a source→sink trace (where untrusted input enters, where it does harm) or a concrete misconfiguration line. Run the adversarial self-check before writing: **reachable?** (is the path actually exposed) · **defenses?** (framework protections, middleware, WAF) · **impact?** (what an attacker gains). If any is unknown, cap confidence at Medium and say what would confirm it. Use CVSS v4.0 vocabulary for severity when helpful. Never write "looks insecure".

## Commands (shell) and no-shell fallbacks
- SAST: `semgrep --config auto`, `bandit -r`, `gosec ./...`, `brakeman`, `npm audit` (→T03).
- Secrets: `gitleaks detect --log-opts=--all`, `trufflehog git file://.`.
- IaC: `checkov -d .`, `tfsec`, `trivy config .`, `kube-linter`.
- No shell: GitHub **Security → Code scanning / Secret scanning / Dependabot** alerts; code search for `AKIA`, `BEGIN PRIVATE KEY`, `password=`, `eval(`, `exec(`, `.raw(`, `dangerouslySetInnerHTML`, `verify=False`, `0.0.0.0/0`, `"Action": "*"`; read IaC and workflow files directly.

## Output
Findings `T02-###` with source→sink evidence, the threat model (assets, boundaries, STRIDE table, top-3 attack trees), a secrets verdict, the multi-tenancy and cloud-security sub-scores, score 1–5 with anchor, and open questions.
