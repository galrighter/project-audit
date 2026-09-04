# T20 — Change-Level (PR/Diff) Review

**Purpose.** Review a single change the way a strong senior reviewer would, and — inside a full audit — sample recent PRs to judge review quality. Two stages: **spec compliance** (does the change do what was asked, nothing more, nothing less) and **code quality** (correctness, cross-file impact, tests, security, performance, compatibility, migrations, docs), with a confidence filter that keeps noise out.
**Start from.** The PR description, linked issue/spec, the diff, the files the diff touches and their callers, existing tests for those areas, CI results.
**Cross-links.** Security patterns are T02 · test expectations are T04 · migration safety is T07 · review-process metrics are T10.

## Stage 1 — Spec compliance
- Restate the intent from the PR/issue/spec in one sentence. If the intent is unclear, that is the first finding.
- Check: every requirement implemented · no unrequested behavior changes (scope creep, "while I was here" refactors that hide risk) · acceptance criteria satisfied · edge cases from the spec handled · the change matches conventions already established in the repo (T01/T08).

## Stage 2 — Code quality
### A. Correctness
Logic errors, off-by-one, null/undefined paths, error handling (→A10), concurrency, resource cleanup, time-zone/rounding issues, wrong assumptions about data.
### B. Cross-file impact
Callers of changed functions (signature/behavior changes) · contracts (API schema, events, DB) · configuration and environment variables · feature flags · generated code and migrations · other packages in a monorepo. "Does a change here break a caller there?" is the reviewer's central question.
### C. Tests
New behavior tested · regression test for the bug being fixed · tests meaningful (→T04 quality criteria) · flaky or skipped tests introduced · CI green for the right reasons.
### D. Security
Input validation, authorization on new paths, secrets, injection, unsafe output handling, dependency additions (→T02/T03).
### E. Performance
New queries and their indexes, N+1, unbounded loops, large payloads, synchronous external calls (→T05).
### F. Compatibility and migrations
Backward compatibility of APIs and stored data · expand/contract migrations · rollback safety · data backfills · versioning bumps and changelog.
### G. Documentation and operability
Docs, comments where intent is non-obvious, runbooks for new operational surfaces, alerts for new failure modes.

## Finding taxonomy and confidence filter
Classify each finding: **critical bug** · **security** · **performance** · **validation/edge case** · **maintainability/refactor** · **nitpick**. Before reporting, ask: is it reachable, is it real (not a style preference), does it change what the author should do? Drop findings that fail; label nitpicks as such and keep them few. Aim for a review the author can act on in order of severity.

## Sampling mode (inside a full audit)
Pick 5–10 recent merged PRs across authors and sizes. For each, note: description quality · review depth (comments, approvals, self-merge) · whether tests were added · whether the review caught anything · time to merge. Produce a review-culture verdict for T10 and a list of representative issues that slipped through.

## Output
Standalone: a review in severity order with a one-line summary, spec-compliance verdict, findings with file:line and suggested fix, and an explicit "approve / request changes / needs discussion" recommendation. In audit mode: findings `T20-###`, the PR-sample table, the review-culture verdict, score 1–5 with anchor, and open questions.
