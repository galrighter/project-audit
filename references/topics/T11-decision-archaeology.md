# T11 — Decision Archaeology

**Purpose.** Recover the decisions that shaped the system — recorded or not — with their context, rationale and consequences today, and judge how decisions get made: explicit records (ADRs), decisions inferable from history, trade-offs, consistency, planning evidence, and whether choices were product-driven or technology-driven.
**Start from.** docs/adr, RFCs, design docs, CHANGELOG, roadmap; the T10 summary (hotspots, reverts, cadence); manifest history; migration history; feature flags and constants (from T09 if available).
**Cross-links.** Git forensics is T10 (run first) · frozen decisions in flags/constants are T09 · business pivots are T12 · modernization implications are T18 · questions for decision makers go to T15.

## Checklist

### A. Explicit records
ADRs (Nygard format: title, status, context, decision, consequences; MADR with options considered; Y-statements) · RFCs and design docs · CHANGELOG rationale · status trails (superseded ADRs show evolution). Absence of any decision record on a system of this size is itself a finding; presence with stale statuses is another.

### B. Decisions inferable from history
- **Pivots and rewrites:** large add/delete commits, new top-level directories, framework or language swaps, renamed core concepts.
- **Reverted changes:** what was tried and abandoned, and how quickly.
- **Abandoned modules and dead code:** directories with no recent commits, commented-out blocks, `@deprecated` never removed, half-finished features behind flags.
- **Dependency swaps:** manifest history (moved from X to Y) → infer the pain that motivated it.
- **Schema evolution:** migration timeline showing how the data-model decisions changed and which were reversed.
- **Frozen decisions:** feature flags permanently on/off, constants that encode policy, environment-specific branches in code.
- **Build-vs-buy choices:** hand-rolled components where services exist, and vice versa.

### C. Trade-offs and their consequences today
For each major decision: what it optimized for (speed, cost, simplicity, control, performance), what it gave up, and whether the trade-off still holds given current scale and team. Decisions that were right at the time and wrong now are the most useful findings.

### D. Consistency and governance
Are decisions applied uniformly (one way to do auth, errors, config, data access) or does each module do its own thing? Consistency signals a decision process; drift signals none. Note who appears to make decisions (from PR discussions, ADR authors, commit patterns) and whether reviews change outcomes.

### E. Planning evidence
Specs preceding large changes · roadmap/PROGRESS/TODO files and their currency · milestones and issue links · estimates vs actuals where visible · evidence of prioritization (what was deferred and why).

### F. Product-driven vs technology-driven
Classify major decisions: driven by a product or market need (evidence: issue, customer request, metric) vs technical preference or novelty. Note technical choices disconnected from product goals, and product decisions made without technical assessment.

### G. Decision quality signals
Decisions reversed within weeks · repeated migrations of the same concern · long-running "temporary" solutions · decisions documented after the fact · irreversible decisions (data model, vendor lock-in) taken casually.

## Output
Findings `T11-###`, plus the **decision log**: decision · date/range · evidence (ADR/commit/PR/file) · inferred rationale · trade-off · consequence today · still valid? · confidence. Add the pivot timeline, the frozen-decisions list, the consistency verdict, a short read of how decisions get made on this project, a list of **questions to ask the decision makers** (→T15), score 1–5 with anchor (decision hygiene: recorded, consistent, revisited), and open questions.
