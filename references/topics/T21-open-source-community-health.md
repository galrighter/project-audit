# T21 — Open-Source Community Health

**Purpose.** For projects published as open source (or depending on the health of an upstream), assess sustainability and governance: responsiveness, activity, contributor base and retention, governance and decision-making, funding, licensing and contribution rules, security policy, adoption signals, and abandonment risk — using CHAOSS metrics with their known caveats.
**Start from.** Issues and PRs (response times, closure), contributors over time (T10), GOVERNANCE/CONTRIBUTING/CODE_OF_CONDUCT/SECURITY files, releases, sponsors/funding files, download/dependents data, the OpenSSF Best Practices badge if present.
**Cross-links.** Git forensics is T10 · supply-chain hygiene (Scorecard) is T03 · licensing is T03/T14 · documentation is T08.

## Checklist

### A. Responsiveness (CHAOSS starter model)
Time to first response on issues and PRs · change-request closure ratio (opened vs closed over a window) · issue age distribution and triage practice (labels, templates, stale bots) · maintainer tone and clarity in responses.

### B. Activity and releases
Commit and PR activity over 12–24 months (rising, flat, fading) · release frequency and regularity · time since last release vs open critical issues.

### C. Contributors and retention
Contributor count and trend · new contributors per quarter and how many return (retention) · contributor absence factor (bus factor) with the caveat that a departed maintainer's automated merges look like activity — check human authorship and recency · share of contributions from one company · maintainer succession plan.

### D. Governance
GOVERNANCE.md or equivalent (roles, how maintainers are added/removed, decision process, voting) · CONTRIBUTING with expectations and review SLAs · CODE_OF_CONDUCT and enforcement contact · roadmap and RFC process · foundation membership or fiscal host · transparency of decisions (public discussions vs private chats).

### E. Funding and sustainability
Sponsors (GitHub Sponsors, Open Collective, foundation), commercial backing or a company employing maintainers, paid support or a commercial edition (open-core) and the boundary between free and paid · concentration of funding · evidence of burnout (maintainer notes, long gaps, "looking for maintainers").

### F. Licensing and contribution rules
License clarity and OSI approval · CLA or DCO requirement and friction · license changes in history (relicensing to source-available is a strategic signal) · trademark policy for the project name.

### G. Security policy and process
SECURITY.md with a disclosure path · published advisories and response times · signed releases and provenance (→T03) · OpenSSF Best Practices / Scorecard badge and results · dependency-update automation.

### H. Adoption and ecosystem signals (with caveats)
Stars/forks (weak, inflatable), dependents count, package downloads (bots skew them), notable users, integrations, conference talks, third-party tutorials · competing projects and momentum.

### I. Abandonment and fork risk
Signals: no releases in 12+ months with open security issues · maintainers unresponsive · a healthy fork gaining adoption · the project marked as looking for maintainers. For a dependency, what the exit plan would be (→T18).

## Output
Findings `T21-###`, the CHAOSS metric table (metric · value · window · caveat), the governance checklist result, the funding summary, the adoption table, the abandonment-risk verdict, score 1–5 with anchor (sustainability and governance), and open questions.
