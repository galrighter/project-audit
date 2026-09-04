# Audit run manifest

- Project: <name> · Repo: <path or URL>
- Started: <YYYY-MM-DD HH:MM> · Orchestrator: <agent/session>
- Mode: self-audit | due-diligence | onboarding | pr-review · Depth: full | short · Report language: <he/en>
- Environment: sub-agents: yes/no · shell: yes/no · repo access: local / GitHub web only / Repomix pack (<path>)
- User confirmation of topic list: <quote or "preset used, unattended run"> at <time>

## Inputs
| Input | Status (Present / Partial / Absent) | Notes |
|---|---|---|
| Repo tree | | |
| Full source | | |
| Git history | | |
| Issues / PRs | | |
| Docs | | |
| CI/CD config | | |
| IaC / infra | | |
| Dockerfiles | | |
| DB schema / migrations | | |
| Running app / URL | | |
| Analytics / dashboards | | |
| Dependency manifests | | |
| Licenses | | |

## Applicability flags (from foundation)
has_ui: · has_persistent_data: · has_infra: · has_ai: · is_open_source: · has_prs: · legacy_signals: · project_types:

## Topic plan and status
| ID | Topic | Selected | Wave | Status | Attempts | Last update | Notes / skip reason |
|---|---|---|---|---|---|---|---|
| F0 | Foundation | yes | 0 | | | | |
| T01 | Architecture & code quality | | 1 | | | | |
| T02 | Application security & threat model | | 1 | | | | |
| T03 | Supply chain & licensing | | 1 | | | | |
| T04 | Testing & QA | | 1 | | | | |
| T05 | Performance & scalability | | 1 | | | | |
| T06 | Operations, reliability & DR | | 1 | | | | |
| T07 | Data model & governance | | 1 | | | | |
| T08 | Documentation & DevEx | | 1 | | | | |
| T09 | Business logic & domain rules | | 1 | | | | |
| T10 | Dev workflow & git forensics | | 1 | | | | |
| T11 | Decision archaeology | | 2 | | | | needs T10 |
| T12 | Business & commercial inference | | 2 | | | | needs T10 |
| T13 | Cloud cost & sustainability | | 1 | | | | |
| T14 | Legal & compliance | | 1 | | | | |
| T15 | Beyond the repo | | 3 | | | | needs all |
| T16 | AI / LLM risk | | 1 | | | | |
| T17 | Accessibility & localization | | 1 | | | | |
| T18 | Legacy modernization | | 1 | | | | |
| T19 | Project-type-specific | | 1 | | | | sections: |
| T20 | Change-level review | | 1 | | | | |
| T21 | Open-source community health | | 1 | | | | |

Status values: pending · running · done · partial · failed · skipped

## Plan changes
- <time> — <what changed and why; user informed?>
