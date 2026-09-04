# T08 — Documentation & Developer Experience

**Purpose.** Judge how quickly a competent engineer (or an AI coding agent) can understand, run, change and ship this project safely: documentation inventory and currency, spec-to-code drift, onboarding, the inner development loop, tooling, AI-agent readiness, and knowledge management.
**Start from.** README, docs/, CONTRIBUTING, ADRs, API docs, runbooks, CLAUDE.md/AGENTS.md/.cursorrules, devcontainer/docker-compose/Makefile, CI config, issue templates.
**Cross-links.** Process metrics (PR cycle time, WIP) are T10 · runbooks are judged operationally in T06 · decision records are T11 · test speed and flakiness overlap T04.

## Checklist

### A. Documentation inventory and currency
README (purpose, setup, run, test, deploy) · architecture overview · ADRs · API reference (OpenAPI/GraphQL schema, published?) · runbooks · CONTRIBUTING and coding standards · CHANGELOG · inline docs and docstrings on public surfaces. For each: last-updated vs code changes in the same area, broken links, instructions that no longer work (verify the setup instructions if a shell exists).

### B. Spec-to-code drift
Compare what docs, specs, issues and marketing claim with what the code does: documented features that are absent, behavior present but undocumented, configuration options not documented, error messages that contradict the docs. Drift here misleads users, auditors and agents alike.

### C. Onboarding
The one-week test: could a new engineer run it locally and ship a small change in under a week? Evidence: devcontainer or docker-compose for dependencies, one-command setup (`make setup`, `just`, scripts), complete `.env.example`, seed data, a documented path to obtain secrets, a "good first issue" trail, and a written architecture tour.

### D. Inner loop
Build time, test-suite time, hot reload, local service dependencies (do you need five cloud accounts to run it?), flaky tests, CI feedback time, how long a trivial change takes from edit to merged.

### E. Tooling and consistency
Linters, formatters, type checking, pre-commit hooks, editorconfig, consistent scripts across the repo, CI parity with local commands, a single command that runs "everything CI runs".

### F. AI-agent readiness
CLAUDE.md / AGENTS.md / .cursorrules / copilot-instructions present and accurate · conventions, commands and task definitions agents can follow · repo shaped for agent work (small modules, clear boundaries, tests as executable spec, no hidden manual steps) · guardrails for agents (what not to touch, how to run tests). This matters most for teams that build primarily with coding agents.

### G. Knowledge management
Where decisions and tribal knowledge live (docs vs chat vs one person's head) · issue tracker hygiene (templates, labels, stale issues) · meeting notes or design docs linked from code · a glossary of the domain language (→T09).

### H. Developer-experience signals (if data exists)
Survey results, PR cycle time, review latency, WIP per developer (→T10), self-reported friction in issues or commit messages ("finally fixed the flaky setup").

## Signals and red flags
README setup that fails on a clean machine · docs last touched a year before the code · no way to run locally without production credentials · a 40-minute test suite · agents instructions that contradict the code · knowledge concentrated in one person (→T10 bus factor).

## Commands (shell) and no-shell fallbacks
- Follow the README on a clean container; time `build` and `test`; run a link checker on docs; compare `git log -1 --format=%cd -- docs/` with the code directories.
- No shell: read docs and compare their last-commit dates with the code via the GitHub history view; read CI durations from the Actions tab.

## Output
Findings `T08-###`, the documentation inventory table (doc · exists · current · gaps), the spec-to-code drift list, the onboarding verdict with evidence, score 1–5 with anchor, and open questions.
