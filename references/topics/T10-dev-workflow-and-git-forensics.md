# T10 — Development Workflow & Git Forensics

**Purpose.** Read the history as evidence of how the team works and where risk concentrates: activity and cadence, contributor concentration and bus factor, hotspots and change coupling, branching and review practice, commit hygiene, CI/CD and release management, DORA and CHAOSS process indicators, planning signals, and AI-assisted development patterns. This topic's summary feeds T11 and T12.
**Start from.** The foundation git snapshot; `.github/` (workflows, CODEOWNERS, templates); branch protection (if visible); PR list; tags/releases; CHANGELOG.
**Cross-links.** Hotspots feed T01 and T18 · deploy safety is T06 · docs/DevEx is T08 · AI-generated-code risk is T16 · open-source community dynamics are T21 · decisions read from history are T11.

## Checklist

### A. Activity and cadence
Commits per month over the whole history · active days per week · time-of-day and weekend patterns (solo-founder and burnout signals) · gaps and bursts · whether activity is rising, stable or fading.

### B. Contributors, ownership, bus factor
Contributor count over time, bots excluded · share of the top author and top three · **Contributor Absence Factor** (CHAOSS: the smallest number of contributors responsible for 50% of contributions; formerly "bus factor") · caveat: the metric cannot tell one engaged maintainer from one departed maintainer whose token still merges bot PRs — check recency and human authorship · knowledge loss: share of current code last touched by authors who have not committed in 6–12 months · ownership per hotspot (primary/secondary owner).

### C. Hotspots and coupling (Tornhill's methods)
Hotspots = change frequency × complexity/size (the top 20 are where defects and cost concentrate) · bug-fix-prone files (commits matching fix/bug/revert) · change/temporal coupling (files that change together across commits; high coupling across module boundaries reveals hidden architecture) · sum of coupling per file · code age (stable vs churning areas) · developer congestion (many authors on the same files in the same period).

### D. Branching, PRs and review
Branching model (trunk-based, GitFlow, long-lived branches merged rarely) · PR size distribution · review depth (comments per PR, approvals, self-merge rate, rubber-stamp latency) · time to first review and to merge · CODEOWNERS · branch protection and required checks · squash/merge conventions · stale branches.

### E. Commit hygiene
Message quality and Conventional Commits · atomic commits vs "WIP"/"fix" chains · revert rate · force pushes on shared branches · large binaries or generated files committed · secrets (→T02).

### F. CI/CD
Pipeline stages and what actually gates merges · pipeline duration and flakiness (reruns) · deploy automation and environments · deploy frequency (workflow runs, tags) · manual steps and who performs them.

### G. Release management
Tags and semantic versioning · release cadence and regularity · changelog discipline · hotfix frequency · rollback events.

### H. DORA proxies (reference points, not verdicts)
Deployment frequency (deploy runs / tags) · lead time for changes (PR open → merge → deploy) · change failure rate (reverts, hotfixes, incident-linked changes / deploys) · time to restore (hotfix latency after an incident). 2024 Accelerate bands for elite: deploy on demand, lead time < 1 day, restore < 1 hour, change-failure rate 0–15%. Note the caveat that throughput metrics mislead when much code is AI-generated.

### I. CHAOSS process metrics
Time to first response on issues/PRs · change-request closure ratio · release frequency · contributor absence factor (B) — the "starter project health" model.

### J. Planning signals
Issue and PR templates · milestones and project boards · commits and PRs linked to issues · roadmap files and their currency · specs preceding large changes.

### K. AI-assisted development signals
`Co-Authored-By` trailers naming Claude/Copilot/Cursor · bursty very large commits · generated-code markers and boilerplate duplication · style inconsistencies between commits · review depth on those commits (unreviewed AI output is the risk → T16).

## Signals and red flags
One author with > 80% of commits on a critical system · long-lived branches merged quarterly · PRs approved in under a minute · CI green because tests are not run · deploys by hand from one laptop · a 30% revert rate · activity fading for months on a product still sold · hotspots owned by a departed developer.

## Commands (shell) and no-shell fallbacks
- Authors: `git shortlog -sn --no-merges` · per year: `git log --format=%aN --since=1.year | sort | uniq -c | sort -rn`.
- Cadence: `git log --date=format:%Y-%m --pretty=%ad | sort | uniq -c`.
- Hotspots: `git log --format= --name-only --since=12.month | sort | uniq -c | sort -rn | head -30` · bug-prone: `git log -i --grep='fix\|bug\|revert' --format= --name-only | sort | uniq -c | sort -rn | head -20`.
- Age: `git log --diff-filter=A --name-only --pretty=format:%ad --date=short` · reverts: `git log --oneline | grep -ci revert` · tags: `git tag --sort=creatordate`.
- Knowledge loss: `git log -1 --format=%aN -- <file>` across hotspots vs the active-author list.
- Deeper: `git log --all --numstat --date=short --pretty=format:'--%h--%ad--%aN' --no-renames > changes.log` then `code-maat -l changes.log -c git2 -a revisions|coupling|authors|age`; or `gitfault hotspots|coupling|knowledge`; `git-quick-stats`.
- No shell: GitHub **Insights → Contributors / Commit activity / Code frequency / Network / Pulse**; PR search (`is:pr is:merged review:approved`, `is:pr review:none`); **Actions** history for deploy runs; **Releases**; commit search for `revert`; tag approximations `[Inferred]`.

## Output
Findings `T10-###`, the contributor/bus-factor table, the top-20 hotspot table with owners, the coupling findings, the workflow-maturity checklist, the DORA/CHAOSS proxy table with caveats, score 1–5 with anchor, and open questions. The `## Summary` must include: cadence, bus factor, top hotspots, review practice, deploy practice, and the AI-assistance read — T11 and T12 depend on it.
