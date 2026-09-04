# T03 — Supply Chain, Dependencies & Licensing

**Purpose.** Assess what the project pulls in and what that exposes it to: vulnerable, outdated or unmaintained dependencies; build and CI integrity; SBOM and provenance; and the intellectual-property exposure created by licenses, forks, vendored code and contributor agreements.
**Start from.** Dependency manifests and lockfiles; CI workflow files; Dockerfiles; LICENSE/NOTICE; vendor/ or third_party/ folders.
**Cross-links.** Application-level vulnerabilities are T02 · legal posture beyond licensing is T14 · AI model/provider dependencies are T16 · open-source project practices are T21.

## Checklist

### A. Dependency inventory
Direct and transitive counts per ecosystem; lockfiles present and committed; pinning strategy (exact / caret / ranges); duplicate or conflicting versions; dependency count relative to project size (bloat); install scripts (postinstall) and their provenance.

### B. Vulnerabilities
Known CVEs in direct and transitive dependencies with severity; whether the vulnerable code path is reachable (state when unknown); age of unpatched vulnerabilities; automated alerting (Dependabot/Renovate) enabled and acted upon (PRs merged or ignored?).

### C. Maintenance health of key dependencies
For the 10–20 dependencies the system cannot live without: last release date, maintainer count, open critical issues, deprecation notices, EOL status of runtimes/frameworks (Node, Python, Java, Rails, Django, React majors). Unmaintained (CWE-1104) components on critical paths are High by default.

### D. SBOM and provenance
SBOM present (CycloneDX/SPDX) or generatable; signed releases/artifacts (Sigstore/cosign); build provenance level (SLSA v1.x: is the build reproducible, hermetic, from a trusted builder?); reproducible builds; binary artifacts or vendored minified code committed without source.

### E. CI/CD pipeline integrity (OpenSSF Scorecard lens)
Branch protection and required reviews · pinned Actions/plugins by commit SHA · least-privilege workflow token permissions · dangerous patterns (`pull_request_target` with checkout of PR code, untrusted input in `run:` steps → script injection) · secrets exposure in logs · dependency review in CI · SAST enabled · fuzzing for parsers · security policy present · release signing. Third-party CI services and their access scope.

### F. Container and runtime images
Base image age and CVEs, running as root, unpinned `latest` tags, secrets in layers, multi-stage builds, registry trust.

### G. Licensing and IP exposure
- Dependency → license table (direct at least; transitive for copyleft search).
- **Copyleft in proprietary/SaaS code:** GPL/AGPL/SSPL linked or bundled; AGPL in a SaaS backend can force source disclosure or costly re-architecture. LGPL linking mode. License compatibility with the project's own license.
- Missing or contradictory license files; forks with unclear license; code copied from Stack Overflow/other repos without attribution; vendored code with stripped headers.
- Assets: fonts, icons, images, datasets, ML models and their licenses (model licenses often restrict commercial use → T16).
- Contributor agreements: CLA/DCO; contributor IP assignment for employees and contractors (→T15 to confirm); domain, app-store and cloud accounts held personally vs by the entity (→T14).
- Commercial dependencies: paid libraries/SDKs, license keys, seat limits.

## Signals and red flags
No lockfile · hundreds of transitive deps with no alerting · Actions pinned to `@main` · `pull_request_target` + checkout · unmaintained auth/crypto library · AGPL dependency in a closed SaaS · vendored code with unknown origin · secrets in Docker layers · runtime past EOL.

## Commands (shell) and no-shell fallbacks
- Audits: `npm audit` / `pnpm audit` / `yarn npm audit`, `pip-audit`, `cargo audit`, `govulncheck ./...`, `bundler-audit`, `osv-scanner -r .`.
- SBOM: `syft dir:. -o cyclonedx-json`, `cyclonedx-npm`, `pip-audit --format cyclonedx-json`.
- Licenses: `license-checker --summary`, `pip-licenses`, `cargo license`, `go-licenses report`.
- Scorecard: `scorecard --repo=github.com/owner/repo`. Images: `trivy image <name>`, `grype`.
- No shell: GitHub **Insights → Dependency graph** (and its **Export SBOM**), **Security → Dependabot alerts**, `deps.dev` for package health, `securityscorecards.dev` for public repos, read lockfiles and workflow YAML directly.

## Output
Findings `T03-###`, the dependency health table (top deps with status), the license table with copyleft/unknown entries highlighted, the pipeline-integrity checklist result, score 1–5 with anchor, and open questions (e.g., "confirm contractor IP assignment").
