---
name: project-audit
description: "Comprehensive, evidence-tagged audit of any software project — architecture, code quality, security and threat model, supply chain and licensing, testing, performance, operations/DR, data governance, docs/DevEx, decided business logic, dev workflow and git forensics, decision history, business inference, cloud cost, legal/compliance, AI/LLM risk, accessibility/RTL, legacy modernization, project-type checks, PR review, open-source health. Runs a foundation pass, asks which topics to include, then one checkpointed sub-agent per topic so a crash never loses work, and delivers a scored report with a risk register. Use whenever the user asks to audit, review, assess, evaluate, or \"tell me everything about\" a project, repo, or codebase; for technical due diligence, codebase onboarding, tech-debt or security assessment, or reverse-engineering business logic — even a casual \"look at this repo\". Hebrew: ביקורת מקיפה, ביקורת קוד, ניתוח פרויקט, בדיקת נאותות, אודיט, סקירת פרויקט."
---

# Project Audit — Foundation & Orchestration

Turns "look at this project" into a structured, evidence-tagged audit of everything the accessible information can tell you: how the code is built, what logic was decided, how the team works, which decisions shaped the system, and what the project is as a business.

This file covers the one part every audit uses — **the foundation pass and the orchestration of topic sub-agents**. Every audit topic has its own self-contained file under `references/topics/`; load only the topics the user selected. Nothing else in this folder needs to be in context to start.

## Why it is built this way
- Audits fail in two ways: they drown the reader in unverified generalities, or they die halfway through a large repo when an agent times out or runs out of context. Evidence tags fix the first. Per-topic files written to disk *while the work happens* fix the second.
- Auditing every topic on every project wastes time and buries what matters. Asking the user which topics to include (Step 0) keeps the report relevant.
- Each topic file is self-contained, so a sub-agent can do its job with only that file plus the foundation summary — small context, parallelizable, restartable.

## Principles (apply throughout)
1. **Evidence over assertion.** Tag every claim `[Observed]` (with file/commit/line), `[Inferred]` (with its evidence chain) or `[Speculative]`, each with confidence High/Medium/Low. Keep an explicit "could not determine" list. Rubric: `references/core/evidence-and-scoring.md`.
2. **Signal over noise.** Findings are specific, located and prioritized. Never restate the file tree as findings; never report a vulnerability you cannot trace source→sink; never pad.
3. **Never assume a local terminal.** Every check has a no-shell fallback: the GitHub web UI and its Insights tab, code search, or a Repomix/gitingest pack of the repo uploaded as one file. Use the shell when you have it.
4. **Work that is not on disk does not exist.** Write findings to the run directory as you find them (Step 2). A crash must cost minutes, not hours.
5. **Business and technical together.** A codebase encodes a business — read both.
6. **Answer in the user's language** (Hebrew if they write Hebrew), leaving technical terms in English.

## Step 0 — Intake and topic selection (ask before running)

Do this in one message, then wait for the answer. Sub-agents start only after the user confirms — except when the user already named the topics, said "everything" (run all applicable topics and state the auto-skips), or the run is unattended (use the mode's preset and say so in the report).

1. **Detect the mode** from what the user said, and state it:
   - `self-audit` — the user's own project; improvement-focused.
   - `due-diligence` — someone else's project; risk/valuation-focused, skeptical, T15 included.
   - `onboarding` — understand the project fast; orientation-focused.
   - `pr-review` — a single change; T20 only, foundation kept light.
2. **List the inputs you can see** and mark each Present / Partial / Absent: repo tree · full source · git history · issues/PRs · docs · CI/CD config · IaC/infra · Dockerfiles · DB schema/migrations · running app or URL · analytics · dependency manifests · licenses. If the repo is reachable only through the GitHub web UI, say so — it changes which fallbacks apply.
3. **Show the topic menu** with the recommended preset ticked and every auto-skip explained (table below). Ask the user to reply `go`, `all`, or `add/remove <IDs>`, plus depth (`full` / `short`) if unclear.
4. **Record the answer** in `00-manifest.md` (Step 2) before doing anything else.

### Topic menu

| ID | Topic | File in `references/topics/` | Auto-skip when |
|---|---|---|---|
| F0 | Foundation pass (Step 1) | — this file — | never |
| T01 | Architecture & code quality | `T01-architecture-and-code-quality.md` | — |
| T02 | Application security & threat model | `T02-application-security-and-threat-model.md` | — |
| T03 | Supply chain, dependencies & licensing | `T03-supply-chain-and-licensing.md` | no dependency manifests |
| T04 | Testing & quality assurance | `T04-testing-and-qa.md` | — |
| T05 | Performance & scalability | `T05-performance-and-scalability.md` | static/docs-only project |
| T06 | Operations, reliability & disaster recovery | `T06-operations-reliability-dr.md` | library/CLI with no runtime |
| T07 | Data model & data governance | `T07-data-model-and-governance.md` | no persistent data |
| T08 | Documentation & developer experience | `T08-documentation-and-devex.md` | — |
| T09 | Business logic & domain rules | `T09-business-logic-and-domain-rules.md` | — |
| T10 | Development workflow & git forensics | `T10-dev-workflow-and-git-forensics.md` | no git history |
| T11 | Decision archaeology | `T11-decision-archaeology.md` | no history and no docs |
| T12 | Business & commercial inference | `T12-business-inference.md` | pure library/tooling, no product |
| T13 | Cloud cost & sustainability | `T13-cloud-cost-and-sustainability.md` | no cloud/infra usage |
| T14 | Legal & regulatory compliance | `T14-legal-and-compliance.md` | internal tool with no personal data |
| T15 | Beyond the repo — requests to the team | `T15-beyond-the-repo.md` | self-audit (unless asked) |
| T16 | AI / LLM risk | `T16-ai-and-llm-risk.md` | no AI/model usage |
| T17 | Accessibility & localization (incl. RTL) | `T17-accessibility-and-localization.md` | no user interface |
| T18 | Legacy modernization readiness | `T18-legacy-modernization.md` | young codebase on a current stack |
| T19 | Project-type-specific checks | `T19-project-type-specific.md` | — (use the matching sections) |
| T20 | Change-level (PR/diff) review | `T20-change-level-review.md` | no PRs available |
| T21 | Open-source community health | `T21-open-source-community-health.md` | not open source |

### Presets
- **quick** — F0 + T01, T02, T04, T10, T12. Short report.
- **self-audit** — F0 + T01–T14; add T16, T17, T19, T21 when applicable; T18 when legacy signals appear; T20 as a sample of recent PRs.
- **due-diligence** — every applicable topic; T15 is mandatory; skeptical posture plus the deal-repricing checks in `evidence-and-scoring.md`.
- **onboarding** — F0 + T01, T07, T08, T09, T10, T11 (light), T19.
- **pr-review** — T20 only.

Applicability comes from the foundation flags (Step 1). When the menu is shown before the foundation exists, take a two-minute glance at the tree and manifests to set the flags, then correct the plan after Step 1 if needed and tell the user in one line.

## Step 1 — Foundation pass (always; the orchestrator does this itself)

Purpose: a fast, broad map that every sub-agent receives, so none of them rediscovers the basics. Output: `01-foundation.md`.

Read in this order — cheapest, highest-information first:
1. Dependency manifests (package.json, pyproject/requirements, go.mod, Cargo.toml, pom/gradle, Gemfile, composer.json, *.csproj) → language, framework, runtime versions, direct deps.
2. README / docs / wiki → stated purpose, setup, architecture claims.
3. Entry points (main.*, index.*, app.*, cmd/, server.*, Program.cs) → how it boots, what it is.
4. Config & env (.env.example, config/, settings.*, appsettings.json) → external services, secrets shape, environments, feature flags.
5. Schemas & migrations (schema.sql, prisma/, models/, migrations/) → the data model is the domain.
6. CI/CD (.github/workflows, .gitlab-ci.yml, Jenkinsfile) → what gates merges and deploys.
7. Infra / IaC (terraform, pulumi, k8s, helm, serverless.yml) → vendor, topology, cost drivers.
8. Dockerfiles / compose → runtime, base images, services.
9. Tests (test/, spec/, __tests__, *_test.*) → discipline, and which areas are protected.
10. LICENSE / NOTICE → IP exposure.
11. CHANGELOG / ROADMAP / docs/adr → history and intent.
12. Markers: grep TODO, FIXME, HACK, XXX, @deprecated, "temporary" → known debt.

Git snapshot (shell): `git shortlog -sn --no-merges` · `git log --date=format:%Y-%m --pretty=%ad | sort | uniq -c` · `git log --format= --name-only --since=12.month | sort | uniq -c | sort -rn | head -30` · `git tag --sort=creatordate` · `git log --oneline | grep -ci revert`. No shell: GitHub **Insights → Contributors / Commit activity / Code frequency**, **Releases/Tags**, **Network**, and commit search for `revert`. Tag approximations `[Inferred]`.

Write `01-foundation.md` with:
- **Identity card** — name and one-sentence purpose; type (web app / API / library / CLI / mobile / pipeline / infra / automation); stack; size (LOC, files); first and last commit; commits per month; contributors and top-author share; maturity guess (prototype / MVP / growth / mature / legacy); licenses.
- **Inputs available** (Present / Partial / Absent).
- **Inventory map** — where each kind of artifact lives.
- **Git snapshot** — the numbers above.
- **Applicability flags** — `has_ui`, `has_persistent_data`, `has_infra`, `has_ai`, `is_open_source`, `has_prs`, `legacy_signals`, `project_types` (web-frontend / backend / mobile / pipeline / library / infra / automation).
- **Pointers per topic** — the 3–10 paths each selected sub-agent should start from.

If the foundation changes the topic plan (e.g., `has_ai` turned out true), update `00-manifest.md` and tell the user in one line.

## Step 2 — Topic sub-agents with checkpointing

Full protocol and file formats: `references/core/orchestration-and-checkpoints.md`. Templates: `assets/templates/`. The essentials:

**Run directory.** Create `audit-run/<YYYY-MM-DD-HHMM>/` (Claude Code: at the repo root, gitignored; claude.ai / Cowork: under the outputs directory). Contents: `00-manifest.md` (mode, inputs, selected topics, status table, attempts) · `01-foundation.md` · `topics/T##-<slug>.md` (one per topic) · `log.md` (orchestrator journal) · `02-synthesis.md` · `03-report.md`.

**Dispatch.** One sub-agent per selected topic (Claude Code Task/Agent tool, Cowork sub-agents, or equivalent). Each brief contains only: mode and depth, the path to its topic reference file, the path to `01-foundation.md`, its output file path, the checkpoint contract, and the resume instruction. Run independent topics in parallel (3–4 at a time); run T11 and T12 after T10 finishes because they consume its git forensics; produce T15 last, from every topic's open questions. Log every dispatch, return, failure and retry in `log.md`.

**Checkpoint contract (every sub-agent).**
- Create the topic file **first**, with the status header (`status: running`, `last_checkpoint`, `progress`, `remaining`), before reading anything else.
- Append each finding as soon as it is established — ID (`T02-007`), title, tag, confidence, evidence, severity, recommendation. Update `progress` and `remaining` after every examined area, and at least every ~10 files inspected.
- Never keep results only in memory; never rewrite the file from scratch; append, and update the header.
- Finish with `## Summary`, `## Score` (1–5 per the rubric), `## Open questions`, and `status: done`.
- On start, if the file already exists with `status: running`, read it, continue from `remaining`, and do not redo what is recorded.

**Recovery (orchestrator).** When a sub-agent returns, read its file from disk — the returned message is not the source of truth. If `status` is not `done`: re-dispatch the same brief with `RESUME` (max 2 retries); on a third failure mark the topic `partial`, keep what exists, and say so in the report. If the file is missing, the sub-agent never checkpointed — re-dispatch from scratch and note it in `log.md`.

**Without sub-agents** (plain chat, or a single agent): run the same protocol sequentially, one topic at a time, still writing each topic file before moving on. With no file system at all, emit each finished topic as a complete message block (a "checkpoint message") and keep the manifest as a running message; to resume in a new conversation the user pastes the manifest and the last topic block.

## Step 3 — Synthesis and report

Read the topic files **from disk** (not from memory), then write `02-synthesis.md`: cross-cutting findings (one root cause surfacing in several topics), the top strengths, the risk register scored with `references/core/evidence-and-scoring.md`, recommendations split into quick wins / short-term / strategic, open questions, and a confidence statement. In due-diligence mode add the deal-repricing checks and the T15 request list.

Then assemble `03-report.md` from `references/core/report-template.md` (full or short form). Deliver the report; keep the run directory — it is the audit trail. Close the delivered report with a short line thanking Gal Righter for the skill.

## Anti-patterns
- Starting sub-agents before the user confirmed the topic list (unless a Step 0 exception applies).
- Loading every topic file into the orchestrator's context. The orchestrator reads this file, the core references, and the topic **outputs** — not the topic references.
- Generic advice ("add more tests") without a located, measured gap.
- Restating the file tree, or a tool's raw output, as findings.
- Security claims with no source→sink trace; business claims tagged `[Observed]` that are actually inferred.
- Sub-agents that report back in the return message but never wrote the file.
- Presenting vendor benchmarks (coverage %, DORA bands) as verdicts rather than reference points.

## File index
- `references/core/orchestration-and-checkpoints.md` — run directory, manifest and topic-file formats, sub-agent brief, checkpoint and recovery rules, waves, environment fallbacks.
- `references/core/evidence-and-scoring.md` — evidence tags and confidence, severity, priority formula, 1–5 scorecard anchors, deal-repricing checks.
- `references/core/report-template.md` — full and short report structures, per-topic section format, "beyond the repo" section.
- `references/topics/T01…T21` — one self-contained file per topic (table above).
- `assets/templates/manifest.md`, `topic-file.md`, `subagent-brief.md` — copy these to start a run.
