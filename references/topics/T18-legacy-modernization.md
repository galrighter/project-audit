# T18 — Legacy Modernization Readiness

**Purpose.** For codebases with legacy signals — EOL runtimes or frameworks, unmaintained dependencies, frozen modules, a rewrite under way, or "nobody touches that part" — assess how safely and cheaply the system can be modernized: legacy zones, seams, characterization-test coverage, strangler-fig candidates, data migration paths, cutover mechanics, upgrade blast radius, and the realistic options with their trade-offs.
**Start from.** The T10 hotspot and age analysis (stable-but-critical vs churning areas), dependency and runtime versions (T03), architecture boundaries (T01), test coverage of legacy areas (T04), schema and migration history (T07), any rewrite or migration branches/directories.
**Cross-links.** Architecture and seams are T01 · dependency EOL is T03 · characterization tests are T04 · data migration is T07 · decision history of previous rewrites is T11.

## Checklist

### A. Legacy zones
Map areas by: runtime/framework version and EOL date · dependency staleness · code age and churn (old and untouched vs old and churning) · ownership (author still present?) · test coverage · defect density (T10 bug-prone files). Classify each zone: keep / refactor in place / extract / replace / retire.

### B. Seams and boundaries
Where can behavior be intercepted without editing the legacy code (interfaces, adapters, message boundaries, HTTP edges, DB views)? Where is coupling so tight that extraction needs the whole module? Use T10 change coupling to find hidden seams that cross directories.

### C. Safety net
Characterization tests (capture current behavior before changing it) · golden-master/approval tests for outputs · contract tests at the seams · feature flags for dual-running old and new · observability to compare behaviors (shadow traffic, diffing).

### D. Strangler-fig candidates
Capabilities that can be routed to a new implementation incrementally (an edge/router/facade in front, moving one capability at a time) · order by value and risk (start with a bounded, well-understood, high-pain capability) · what must stay shared during the transition (auth, data).

### E. Data migration paths
Schema changes needed · dual-write or change-data-capture options · backfill strategy and volume · cutover with rollback · data-quality verification · downtime tolerance (→T06/T07).

### F. Upgrade paths and blast radius
For each EOL component: the target version, breaking changes, transitive dependency impact, estimated effort tier, whether the ecosystem provides codemods · order of upgrades (runtime first? framework first?) · CI support for running both during transition.

### G. Rewrite-in-progress assessment
If a rewrite exists: scope, elapsed time vs plan, feature parity tracking, migration plan for data and users, whether old and new are diverging, who decides done · the classic risk: a big-bang rewrite with no parity tracking and no incremental delivery.

### H. Options and trade-offs
Present 2–3 realistic options — refactor in place, strangle incrementally, targeted replacement of one subsystem, full rewrite, or retire — each with effort tier, risk, prerequisites (safety net first), and the business condition under which it is the right choice. Recommend one, tagged with confidence.

## Signals and red flags
Runtime past EOL in production · a "v2" directory older than a year with no cutover · no characterization tests on the module everybody fears · a framework upgrade blocked by one abandoned plugin · the only person who knew the legacy module has left · a rewrite tracked in nobody's roadmap.

## Commands (shell) and no-shell fallbacks
- Version and EOL checks (`endoflife.date` data, `npx npm-check-updates`, `pip list --outdated`, framework upgrade guides); coverage per legacy directory; `git log --format=%aN -- <legacy-dir>` for ownership; codemod availability.
- No shell: read manifests and lockfiles; GitHub history per directory; Dependency graph; compare with endoflife.date by hand.

## Output
Findings `T18-###`, the legacy-zone map (zone · versions/EOL · churn/age · owner · tests · classification), the seam inventory, the safety-net gap list, the strangler candidate order, the data-migration sketch, the upgrade table (component · target · breaking changes · effort), the options comparison with a recommendation, score 1–5 with anchor (readiness to modernize safely), and open questions.
