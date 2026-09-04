# Sub-agent brief — <T## Topic name>

You are auditing exactly one topic of a software project as part of the project-audit skill.

- Mode: <self-audit | due-diligence | onboarding> · Depth: <full | short> · Report language: <he | en> (identifiers and paths stay in English)
- Read first, in full: `<skill path>/references/topics/T##-<slug>.md` — this is your checklist and output spec.
- Then read: `<run dir>/01-foundation.md` — identity card, applicability flags, and the "Pointers per topic" entry for T##. Start from those paths.
- Repository access: <local path | GitHub URL (web only) | Repomix pack at <path>> · Shell available: <yes | no> — if no, use the no-shell fallbacks in the topic file.
- Output file: `<run dir>/topics/T##-<slug>.md` — copy `<skill path>/assets/templates/topic-file.md` to create it.
<optional> - Dependency summary from T10 (paste `## Summary`): ...

## Checkpoint contract (binding)
1. Create the output file first, with the header, before reading anything else.
2. Append every finding as soon as it is established; add a coverage-log line and update `progress` / `remaining` after each section; update `last_checkpoint` at least every ~10 files inspected or ~15 tool calls, and before any long-running command.
3. Never rewrite the file from memory; append and edit the header only. Never keep results only in your context.
4. Finish with `## Summary`, `## Score` (1–5 with anchor and evidence), `## Open questions`, and set `status: done`.
5. Resume rule: if the output file already exists, read it, skip what the coverage log records, continue from `remaining`, and continue finding IDs from the highest existing number. Do not duplicate findings.

## Constraints
- Only this topic. Do not load other topic references.
- Tag every claim [Observed] / [Inferred] / [Speculative] with confidence; cite file:line, commit, or the evidence chain. Security findings need a source→sink trace.
- Specific, located, prioritized. No generic advice, no file-tree restating, no padding, no moralizing.
- On very large repos work hotspots-first and mark unexamined areas [Not sampled].
- Return only a five-line summary (status, finding count by severity, score, top finding, open questions count). The file is the deliverable.
