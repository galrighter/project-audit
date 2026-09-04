# T01 — Architecture & Code Quality

**Purpose.** Determine how the system is built, how well its structure serves change, and where quality risk sits: architecture and boundaries, complexity, duplication, error handling, configuration, API design, conventions, and a tech-debt inventory. Includes an ATAM-style evaluation so architecture findings tie to business goals rather than taste.
**Start from.** Foundation pointers for T01; entry points; the largest and most-changed files (git snapshot); any architecture docs.
**Cross-links.** Hotspots use T10 churn data · runtime resilience is T06 · scaling is T05 · modernization paths are T18 · domain rules are T09.

## Checklist

### A. Architecture read (C4 levels)
- **Context:** external actors and systems (users, admins, partners, third-party APIs).
- **Containers:** deployable units (web, API, workers, DB, cache, queue, functions). Style: monolith / modular monolith / microservices / serverless / event-driven. Is the style justified by the team size and load?
- **Components:** modules inside each container and their responsibilities; ownership of each business capability.
- **Boundaries:** layering respected? data layer leaking into UI? integrations isolated behind adapters/ports? shared kernel vs copy-paste between services?
- **Dependency map:** module-to-module dependencies; flag circular dependencies, god modules (hundreds to thousands of lines with many responsibilities), business logic scattered across controllers/services/DAL, and the absence of any domain model.

### B. ISO/IEC 25010:2023 lens (nine product-quality characteristics)
Use as a completeness checklist: functional suitability · performance efficiency · compatibility · interaction capability · reliability (maturity, availability, fault tolerance, recoverability) · security · maintainability (modularity, reusability, analyzability, modifiability, testability) · flexibility (adaptability, scalability, installability, replaceability) · safety. Score maintainability sub-characteristics explicitly; hand security to T02, reliability to T06, performance to T05.

### C. ATAM-style evaluation (business-anchored)
1. Write 5–8 **quality-attribute scenarios** from the business context (T12/foundation): e.g., "10× traffic on a launch day", "add a second payment provider in one week", "a region outage; recover within one hour", "a new developer ships a change in a week", "a tenant's data must be exported and deleted on request".
2. Build a small **utility tree**: attribute → scenario → (business importance H/M/L, technical difficulty H/M/L).
3. For the H/H scenarios, identify **sensitivity points** (architectural decisions that strongly affect the attribute), **trade-off points** (decisions affecting several attributes in conflict), **risks** and **non-risks**. Each becomes a finding with the decision, the scenario, and the evidence.

### D. Complexity and duplication
- Long functions, deep nesting, high cyclomatic/cognitive complexity, long parameter lists, feature envy, primitive obsession.
- Duplication (copy-paste blocks across modules or services). Vendor DD benchmark: duplication > 10% is a flag — a reference point, not a verdict.
- Hotspots: complexity × change frequency (from the T10 git snapshot). Audit hotspots first; a complex file nobody touches is less urgent than a medium one changed weekly.

### E. Error handling and exceptional conditions (OWASP A10:2025 lens)
Swallowed or empty catches; fail-open paths; unchecked return values; inconsistent error contracts between layers; retries without idempotency; race conditions and TOCTOU; resource leaks (connections, handles); errors that leak internals to users; missing timeouts on I/O.

### F. Configuration and 12-factor adherence
Config in environment, not code · backing services as attached resources · strict build/release/run separation · stateless processes · port binding · disposability · dev/prod parity · logs as streams · admin tasks as one-off processes. Note hard-coded environment differences and config sprawl (multiple partial config systems).

### G. API design (when the project exposes one)
Consistency of resources and naming; versioning strategy; pagination; auth; rate limiting; input validation; error shape; idempotency for mutations; OpenAPI/GraphQL schema presence and currency; backward-compatibility and deprecation policy; webhook signing and retries.

### H. Conventions and consistency
Uniform style and naming; typing discipline (TS `strict`, mypy, etc.); linting/formatting config; dead and commented-out code; TODO/FIXME density and age; mixed paradigms within one module.

### I. Fitness functions
Any automated architecture checks (dependency-cruiser, ArchUnit, import-linter, layer rules in CI)? Absence is a finding when the architecture depends on discipline alone.

### J. Tech-debt inventory
Table: item · location · type (design / test / security / dependency / doc / infra) · severity · effort (S/M/L/XL) · interest (how much it slows work now). Feed the top items into the risk register.

## Signals and red flags
Circular dependencies · god classes/modules · shared mutable global state · hand-rolled auth/crypto/payments where proven services exist · logic in controllers or UI · no domain model · a rewrite underway with no migration plan · framework or runtime past EOL · "architecture chosen for the résumé, not the load" (microservices for a two-person team) · configuration that differs per environment in code · silent error swallowing on money or data paths.

## Commands (shell) and no-shell fallbacks
- Size and languages: `scc` / `tokei` / `cloc`. Largest files: `git ls-files | xargs wc -l | sort -rn | head -30`.
- Complexity: `lizard`, `radon cc`, `eslint` complexity rule, `gocyclo`, `rubocop --only Metrics`.
- Duplication: `jscpd`, PMD CPD. Circular deps: `madge --circular`, `dependency-cruiser`, `import-linter`, `go vet`/`pyflakes` for unused code.
- No shell: GitHub tree view for file sizes; code search for `catch {}`/`except: pass`/`TODO`; a Repomix `--compress` pack to sample the largest and hottest files; read the architecture docs and compare with the actual tree.

## Output
Findings in the standard format (IDs `T01-###`), the ATAM table (scenarios, sensitivity and trade-off points), the tech-debt inventory, a score 1–5 with anchor and evidence, and open questions.
