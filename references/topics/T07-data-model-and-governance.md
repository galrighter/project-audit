# T07 — Data Model & Data Governance

**Purpose.** Reconstruct the data model (the most honest statement of the domain), judge its integrity and migration hygiene, and audit how personal and sensitive data is classified, flows, is protected, retained and deleted — including the data-flow map, DPIA triggers, lineage and data quality.
**Start from.** Schema files, ORM models, migrations, seed data, data-access layer, analytics/export code, backup config, privacy docs.
**Cross-links.** Business rules living in constraints are T09 · encryption/access findings overlap T02 · backups/restore are T06 · legal obligations are T14 · pipelines/warehouses are T19 · migrations as decision history are T11.

## Checklist

### A. Schema reconstruction
Entities, relationships and cardinalities; aggregate roots; the core nouns of the business; naming consistency; an ER sketch (text is fine). Note tables nobody references (dead data) and tables with no clear owner.

### B. Integrity and design
Primary/foreign keys, unique and check constraints, NOT NULL discipline, enums vs free text, soft deletes, timestamps and audit columns, tenant columns on every tenant-scoped table, JSON blobs hiding un-modeled structure, denormalization with no sync mechanism, missing indexes on foreign keys (→T05).

### C. Migration hygiene
Reversible migrations; ordering and numbering; schema vs data migrations separated; migrations tested in CI; zero-downtime patterns (expand/contract, backfills in batches); drift between migrations and the live schema; migration count and dates as a product-evolution timeline (→T11).

### D. Personal and sensitive data inventory
Classify every field/store: identifiers · contact · financial/payment · health · location · biometrics · children's data · credentials/secrets · behavioral/analytics · content authored by users. Note where each appears beyond the primary DB: logs, caches, search indexes, analytics, exports, backups, third parties, LLM prompts (→T16).

### E. Data-flow map
Sources → stores → processors/jobs → egress (analytics SDKs, email/SMS providers, CRMs, warehouses, LLM providers, partners, backups). Mark cross-border transfers and the legal basis where relevant (→T14). This map is what a privacy review, a breach response and a due-diligence buyer all need.

### F. Retention and deletion
Retention policy per data class (stated vs implemented) · TTLs and purge jobs · right-to-erasure path covering DB, backups (or documented exception), logs, analytics, third parties · anonymization/pseudonymization for analytics and test data · production data in dev/test environments.

### G. Access control on data
DB roles with least privilege (the app should not run as superuser) · row-level security or an enforced scoping layer · admin access logged · secrets for DB access handled properly · exports and reports scoped.

### H. Encryption and key management
At rest for DB, object storage, backups, search · in transit everywhere (internal too) · field-level for high-risk fields (tokens, government IDs) · key management and rotation.

### I. DPIA / privacy-impact triggers
High-risk processing indicators: profiling or scoring, large-scale sensitive data, systematic monitoring, children's data, new technology (AI), matching datasets. If present and no assessment exists, recommend one (→T14).

### J. Data quality, contracts and lineage
Validation at ingestion; uniqueness and referential checks; reconciliation jobs; data contracts between producers and consumers; schema evolution strategy; lineage from source to report (essential for pipelines and warehouses → T19); freshness monitoring.

### K. Analytics, warehousing and consent
What is shipped to analytics/warehouse tools, whether it respects consent and opt-out, and whether consent is stored, versioned and honored in the sending code paths (marketing, tracking, LLM training opt-out).

## Signals and red flags
No foreign keys · free-text status columns · PII in logs or analytics events · no deletion path · backups that contain data the product claims to delete · production dumps in the repo · JSON columns doing the work of five tables · migrations that lock large tables · analytics events sent before consent.

## Commands (shell) and no-shell fallbacks
- Dump schema (`pg_dump --schema-only`, `prisma db pull`, `sqlite3 .schema`); count tables/columns; list migrations with dates; grep PII column names (`email`, `phone`, `ssn`, `national_id`, `dob`, `address`, `ip_address`, `card`).
- No shell: read schema/ORM/migration files in the repo; Prisma/Supabase/Drizzle schema files; the migrations folder as a timeline; ask for a schema export via T15 when the schema is managed outside the repo.

## Output
Findings `T07-###`, the ER sketch, the sensitive-data inventory, the data-flow map, the retention/deletion table (class · stated · implemented · gaps), score 1–5 with anchor, and open questions.
