# T04 — Testing & Quality Assurance

**Purpose.** Establish how much of the system's behavior is actually protected, how trustworthy the tests are, and whether the pipeline enforces them. Goes beyond headline coverage: critical-path coverage, test quality, mutation testing, property-based and contract tests, load and resilience testing, and CI gating.
**Start from.** Test directories; test config (jest/pytest/go test/junit); coverage config and reports; CI workflow files; the critical paths listed in the foundation (auth, payments, pricing, state machines, integrations).
**Cross-links.** Runtime resilience patterns are T06 · performance targets are T05 · CI/CD process is T10 · accessibility tests are T17 · PR-level test expectations are T20.

## Checklist

### A. Test inventory
Types present: unit · integration · contract · end-to-end/UI · property-based · snapshot · load/performance · security · accessibility. Frameworks and runners. Test count and LOC vs source LOC. Which source areas have tests next to them and which have none (a coverage map by directory is more useful than a single percentage).

### B. Critical-path coverage matrix
For each of: authentication and session · authorization/permissions · payments, billing, refunds · pricing, tax, currency, rounding · state machines (orders, subscriptions, approvals, onboarding) · data migrations and ETL · third-party integrations and webhooks · background jobs and retries · export/deletion of user data. Record: tested? at which level? do assertions check outcomes (not just "no exception")? Missing tests here are High by default in due-diligence mode.

### C. Test quality
Meaningful assertions vs smoke tests · over-mocking that hides real behavior · tests that mirror the implementation (change-detector tests) · determinism (time, randomness, network, ordering) · skipped/xfail/`.only`/quarantined tests and how long they have been skipped · fixtures/factories and test-data hygiene (no production data) · isolation and parallel safety · test readability (a new engineer can tell what broke).

### D. Coverage numbers, used carefully
Report line/branch coverage if available. Vendor DD benchmarks: < 40% is a flag, 60–80% is good — reference points only. Weight critical-path coverage far above the headline number; 30% covering money and auth beats 80% of snapshots.

### E. Mutation testing (truth check on assertions)
If feasible, run a mutation tool on one or two critical modules (Stryker for JS/TS, mutmut/cosmic-ray for Python, PIT for JVM, cargo-mutants for Rust, go-mutesting). A low mutation score with high line coverage means the tests execute code without checking it. Report the score and the surviving mutants that matter.

### F. Property-based testing and fuzzing
For parsers, calculators, serializers, and anything with invariants: Hypothesis, fast-check, QuickCheck-style libraries, go-fuzz/cargo-fuzz. Presence is a maturity signal; absence on a calculation-heavy core is a gap.

### G. Contract tests
Consumer/provider contracts for APIs (Pact, OpenAPI schema validation in tests, GraphQL schema checks); webhook payload validation; versioned fixtures for third-party responses.

### H. End-to-end and UI tests
Playwright/Cypress/Detox coverage of the key user journeys; flakiness rate; runtime; whether they run on every PR or nightly; whether failures block merges.

### I. Performance and load tests
k6 / Locust / Gatling / JMeter / artillery present; numeric targets (p95/p99 latency, throughput, error rate, concurrency); baseline results stored and compared; run cadence (→T05 for findings about the system itself).

### J. Resilience testing
Fault injection and chaos experiments (Toxiproxy, Chaos Mesh, Litmus, cloud fault-injection services); tests for timeouts, retries, dependency-down behavior, partial failure; game-day records. Absent on a revenue-bearing distributed system is a finding (→T06 for the runtime patterns).

### K. CI gating and hygiene
Tests run on every PR and on main · merges blocked on failure · required checks configured · nightly/extended suites · total pipeline time and parallelization · flaky-test policy (quarantine, retry limits, tracking) · coverage thresholds enforced or merely reported · test results visible (reports, dashboards).

### L. Security and accessibility tests in the pipeline
SAST/DAST/secret scanning (→T02/T03) and accessibility checks (→T17) as automated gates.

## Signals and red flags
Tests exist but CI does not run them · coverage badge from a year ago · `.skip` on payment tests · e2e suite disabled for flakiness · tests use production credentials or data · no tests on the state machine that moves money · the only tests are snapshots · test suite takes so long developers avoid it.

## Commands (shell) and no-shell fallbacks
- Run the suite with coverage: `npm test -- --coverage`, `pytest --cov`, `go test -cover ./...`, `mvn verify`. Count tests and skips: `grep -rn "\.skip\|xit(\|@pytest.mark.skip\|t.Skip(" tests/`.
- Mutation: `npx stryker run`, `mutmut run --paths-to-mutate src/billing`, `mvn org.pitest:pitest-maven:mutationCoverage`.
- No shell: read CI workflows and required-checks config; GitHub **Actions** run history and durations; count test files with code search (`path:test`, `extension:spec.ts`); read committed coverage reports or Codecov/Coveralls pages; grep skip markers via search.

## Output
Findings `T04-###`, the critical-path coverage matrix, the coverage map by area, mutation results if run, the CI-gating verdict, score 1–5 with anchor, and open questions.
