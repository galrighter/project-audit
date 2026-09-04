# T19 — Project-Type-Specific Checks

**Purpose.** Each project type has failure modes the generic topics do not catch. Read only the sections matching the `project_types` flags in the foundation; skip the rest. Findings go into the standard format.
**Cross-links.** Everything here refines T01–T07; frontend performance also relates to T05, accessibility to T17, data lineage to T07, automation platforms to T06/T14.

## Sections

### 1. Web frontend (SPA/SSR/static)
Bundle size, code splitting, tree shaking, duplicate dependencies · Core Web Vitals (LCP, INP, CLS) with lab and field data · image/font optimization, caching headers, CDN · rendering strategy (SSR/SSG/ISR/CSR) fit for the content · hydration cost and waterfalls · state management complexity · error boundaries and client-side logging · Content Security Policy, subresource integrity, third-party script governance (tag managers) · SEO technical hygiene (meta, canonical, sitemap, robots, structured data, hreflang) · offline/PWA behavior if claimed · browser support matrix.

### 2. Backend API / service
API versioning and deprecation · idempotency keys on mutations · webhook signing, retries and replay protection · rate limiting per client · pagination and filtering limits · background job design (→T05) · multi-tenancy (→T02) · schema-first contracts (OpenAPI/GraphQL) kept in sync · health/readiness (→T06) · request validation at the edge · timeouts and circuit breakers on outbound calls.

### 3. Mobile app (iOS/Android/React Native/Flutter)
Signing keys, keystores and provisioning profiles not in the repo · secrets and API keys embedded in the binary (assume extractable) · certificate pinning where warranted · app-store compliance: privacy manifests/nutrition labels, data-safety forms, permission rationale, in-app purchase rules, export compliance · minimum OS versions and device coverage · offline mode and sync conflict handling · crash reporting and release health · over-the-air updates and rollback (CodePush/EAS) · deep links and universal links validation · accessibility (→T17) · background execution and battery · store listing and review history as product signals (→T12).

### 4. Data pipeline / ETL / analytics / ML
Idempotent and re-runnable tasks · backfill strategy and partitioning · late and out-of-order data handling · schema evolution and data contracts · data quality checks (nulls, ranges, uniqueness, volume anomalies) and alerting · lineage from source to report (→T07) · orchestration (DAGs, retries, SLAs, dependencies) · reproducibility (versioned code, config, data snapshots) · cost of recompute vs incremental · PII in intermediate stores · ML specifics: training/serving skew, feature versioning, model registry, evaluation and drift monitoring, rollback of models, dataset licenses (→T16/T03).

### 5. Library / SDK / CLI
Public API surface and stability guarantees · semantic versioning and a changelog with breaking changes called out · deprecation policy · supported language/runtime versions and the test matrix · dependency footprint (what users are forced to install) · packaging and publishing (signed releases, provenance, 2FA on registry accounts) · docs and examples for every public entry point · error messages and exit codes (CLI) · config discovery and precedence (CLI) · security of update mechanisms · telemetry disclosure.

### 6. Infrastructure-as-code repository
State storage, locking and encryption · module structure and reuse · drift detection · policy as code (OPA/Sentinel/checkov) in CI · secrets handling · environment promotion strategy · plan review in PRs · blast radius of a bad apply (workspace separation) · tagging standards (→T13) · documentation of manual steps outside IaC.

### 7. Automation / integration platforms (Airtable, n8n, Make, Zapier, Power Automate, scripts on servers)
Inventory of automations and their triggers (often outside the main repo → T15) · single points of failure in third-party platforms and rate limits/quotas · credentials stored in platform fields · error handling and alerting inside automations (silent failures are the norm) · idempotency and duplicate-run protection · versioning and change history of automations (usually none — a finding) · exportability and lock-in (can the logic be reconstructed elsewhere?) · data residency and access control on shared bases · dependency on one person's account (→T14) · documentation of what each automation does and who owns it.

### 8. Browser extension / desktop app
Permission scope minimalism · content-script isolation and injection safety · auto-update mechanism and signing · store policies · local data storage and encryption · telemetry disclosure.

### 9. Monorepo
Build tooling (Nx/Turborepo/Bazel/pnpm workspaces) and cache correctness · ownership per package (CODEOWNERS) · dependency graph hygiene (no cycles between packages) · CI selectivity (affected-only) · versioning strategy (fixed vs independent) · shared code governance.

## Output
Findings `T19-###` per applicable section, a note of which sections were applied, section sub-scores, an overall score 1–5 with anchor, and open questions.
