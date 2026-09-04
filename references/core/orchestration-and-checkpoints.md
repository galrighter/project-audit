# Orchestration & Checkpoints

Contents: 1) Why · 2) Run directory · 3) Manifest · 4) Topic file format · 5) Sub-agent brief · 6) Checkpoint rules · 7) Recovery · 8) Waves, dependencies, concurrency · 9) Context budget · 10) Environments and fallbacks · 11) Journal · 12) Optional custom agent definition

## 1) Why
A comprehensive audit of a real repository is hours of agent work across many topics. Agents time out, exhaust their context, get rate-limited, or are interrupted. If results live only inside an agent's context, every failure restarts from zero and the orchestrator cannot tell what was already covered. One rule fixes this: **the file on disk is the work; the returned message is a courtesy.** Every sub-agent writes findings as it goes, so a failure costs the last few minutes, and any agent — or a fresh conversation — can resume from the file.

## 2) Run directory
```
audit-run/<YYYY-MM-DD-HHMM>/
├── 00-manifest.md        # run config + status table (orchestrator owns)
├── 01-foundation.md      # identity card, inventory map, git snapshot, flags, pointers
├── log.md                # orchestrator journal: dispatches, returns, failures, retries, decisions
├── topics/
│   ├── T01-architecture-and-code-quality.md
│   ├── T02-application-security-and-threat-model.md
│   └── ...               # one file per selected topic (sub-agent owns)
├── 02-synthesis.md       # cross-cutting findings, risk register, recommendations
└── 03-report.md          # the deliverable
```
Location: Claude Code → `<repo>/audit-run/`, and add `audit-run/` to `.gitignore` (or put it outside the repo if the user prefers). claude.ai / Cowork → `/mnt/user-data/outputs/audit-run/...` so it survives the session and is downloadable. Never write into read-only upload directories.

## 3) Manifest (`00-manifest.md`)
Copy `assets/templates/manifest.md`. It holds: mode, depth, language, inputs table, environment (sub-agents available? shell available? GitHub-web-only?), selected topics with reason for each skip, the **status table** and the wave plan.

Status values: `pending` · `running` · `done` · `partial` (retries exhausted, findings kept) · `failed` (nothing usable) · `skipped` (with reason). The orchestrator updates the table on every dispatch and every return, and records attempt counts and timestamps. The manifest is the single place a new conversation reads to know where the run stands.

## 4) Topic file format (`topics/T##-<slug>.md`)
Copy `assets/templates/topic-file.md`. Structure:

```
---
topic: T02 Application security & threat model
status: running            # running | done
started: 2026-09-04 14:02
last_checkpoint: 2026-09-04 14:31
progress: sections A–D done, 41 files inspected
remaining: E (secrets in history), G (multi-tenancy), H (cloud IaC), summary+score
---
## Coverage log            # append one line per area examined: what, where, when
## Findings                # append as established; never reorder or delete
### T02-001 · <title>
- Tag: [Observed] · Confidence: High · Severity: High
- Evidence: src/api/orders.ts:142 → db/query.ts:88 (source→sink) ; commit a1b2c3d
- Why it matters: ...
- Recommendation: ...  (effort: S/M/L)
### T02-002 · ...
## Summary                 # written at the end: 5–10 sentences, the topic's verdict
## Score                   # 1–5 with the rubric anchor and the evidence behind it
## Open questions          # what could not be determined and what input would resolve it
```
Finding IDs are `<topic>-<3-digit sequence>` and never reused. A corrected finding gets an appended `Revision:` line; it is not silently rewritten — the audit trail matters more than tidiness.

## 5) Sub-agent brief
Copy `assets/templates/subagent-brief.md`. A brief contains exactly:
1. Role and mode (`self-audit` / `due-diligence` / `onboarding`), depth (`full` / `short`), report language.
2. The topic reference to read: `references/topics/T##-....md` (path). Read it fully before starting.
3. The foundation file path (`01-foundation.md`) — read the identity card, flags and this topic's pointers.
4. Repository access: path, or GitHub URL, or the Repomix pack path; whether a shell exists.
5. Output file path and the checkpoint contract (section 6, verbatim).
6. The resume instruction: "If the output file exists, read it first and continue from `remaining`; do not redo recorded work; continue finding IDs from the highest existing number."
7. Constraints: only this topic; do not load other topic references; tag every claim; no shell assumption; return only a 5-line summary — the file is the deliverable.
8. Optional: the `## Summary` of a dependency topic (e.g., T10's summary for T11/T12).

## 6) Checkpoint rules (binding on every sub-agent)
- **Create the output file first**, with the header, before reading anything else. A file that exists with `status: running` and an empty findings list is a valid checkpoint — it proves the agent started and tells the orchestrator what to resume.
- **Checkpoint triggers:** a finding is established → append it now. An area (section of the topic checklist) is finished → add a coverage-log line and update `progress`/`remaining`. Every ~10 files inspected or ~15 tool calls → update `last_checkpoint`. Before any long-running command (test suite, scan) → checkpoint. Before returning → final checkpoint with `status: done`.
- **Append, don't rebuild.** Never rewrite the whole file from memory. Use append or targeted edits to the header. Rewriting is how work gets lost when the agent's memory is already partial.
- **Idempotent resume.** On start, if the file exists: read it, skip areas listed in the coverage log, continue from `remaining`, and continue IDs from the highest one present. Duplicated findings are worse than a slightly uneven report — check before adding.
- **Small, frequent writes beat one big write.** A finding written in 30 seconds is safe; a perfect finding held for an hour is at risk.
- **Never checkpoint into chat only** when a file system exists. When no file system exists, see section 10.

## 7) Recovery (orchestrator procedure)
On every sub-agent return, in this order:
1. Read the topic file from disk. Ignore the returned message except as a hint.
2. `status: done` and `## Summary` + `## Score` present → mark `done` in the manifest, log it.
3. `status: running` (agent crashed, timed out, or returned early) → log the failure, increment attempts, re-dispatch the **same brief** with `RESUME` prefixed. Maximum 2 retries per topic.
4. File missing → the agent never checkpointed. Log it, re-dispatch from scratch (attempt counts), and consider lowering concurrency if several agents fail this way (a sign of environment limits).
5. After the third failure → mark `partial`, keep every finding on disk, and state in the report which sections of that topic were not completed and why.
6. Never delete or overwrite a topic file. If a rerun is needed from scratch, rename the old file with a `.attempt1` suffix and log it.

## 8) Waves, dependencies, concurrency
- **Wave 1 (independent):** T01, T02, T03, T04, T05, T06, T07, T08, T09, T10, T13, T14, T16, T17, T18, T19, T20, T21.
- **Wave 2 (needs T10 done):** T11 decision archaeology, T12 business inference — pass them T10's `## Summary`.
- **Wave 3 (needs everything):** T15 beyond-the-repo — compiles every topic's `## Open questions` into the request list. The orchestrator may write this one itself.
- Concurrency: 3–4 sub-agents at a time is a good default; drop to 2 when agents fail with timeouts; go higher only when the environment clearly supports it.
- Order within a wave: start the heaviest topics first (usually T02, T10, T01, T04) so they don't become the long tail.

## 9) Context budget
- A sub-agent receives: its brief, its topic reference, `01-foundation.md`, and optionally one dependency summary. Nothing else. Topic references are self-contained for exactly this reason.
- The orchestrator keeps in context: `SKILL.md`, the core references, the manifest, the log, and at synthesis time the topic **outputs**. For very large topic files, read `## Summary`, `## Score`, `## Open questions`, and findings of severity Medium or higher; leave Low/Info in the appendix.
- On very large repos (> ~100k LOC or > ~2k files), every sub-agent works hotspots-first: rank by churn × size from the foundation git snapshot, examine the top 20 plus the whole domain/schema/security surface, and mark the rest `[Not sampled]`.

## 10) Environments and fallbacks
| Environment | Sub-agents | Files | How to run |
|---|---|---|---|
| Claude Code | Task/Agent tool (several Task calls in one message run in parallel) | repo filesystem | Full protocol. `audit-run/` at repo root, gitignored. |
| Cowork | its sub-agents | working folder / outputs | Full protocol. |
| claude.ai chat with code execution | none | `/mnt/user-data/outputs` | Sequential: orchestrator runs each topic itself, writing the topic file before moving on. Offer the run directory as a download at the end. |
| claude.ai chat without code execution | none | none | Checkpoint messages: emit each finished topic as one complete block using the topic-file format; keep the manifest as a running message updated after each topic. To resume in a new chat, the user pastes the manifest and the last topic block. |
| GitHub-web-only access to the repo | any | any | Prefer a Repomix pack: `npx repomix --remote owner/repo --compress` (Secretlint scanning included) or repomix.com, then upload/attach the single file. Otherwise browse via the GitHub UI and Insights; tag approximations `[Inferred]`. |

Automation / GitHub Actions runs: use the mode preset (no user to ask), write the run directory as a workflow artifact, and post the short-form report.

## 11) Journal (`log.md`)
One line per event, newest last: `2026-09-04 14:02 DISPATCH T02 attempt 1 (wave 1)` · `14:31 RETURN T02 status=running → RESUME attempt 2` · `14:58 RETURN T02 done (14 findings, score 2)` · `15:10 DECISION: T13 skipped — no IaC found, confirmed with user`. Also log plan changes, user confirmations, and anything a reader would need to trust the report's provenance.

## 12) Optional custom agent definition (Claude Code)
If the user wants a reusable sub-agent, create `.claude/agents/audit-topic.md`:
```
---
name: audit-topic
description: Runs one project-audit topic with checkpointing. Use with the project-audit skill.
---
You audit exactly one topic of a software project. Read the brief you were given, then the topic
reference and the foundation file it names. Create your output file first, then work through the
topic checklist, appending findings as you establish them and updating the header after each area.
Tag every claim [Observed]/[Inferred]/[Speculative] with confidence. Finish with Summary, Score,
Open questions and status: done. If the output file already exists, resume from `remaining`.
Return only a five-line summary; the file is the deliverable.
```
