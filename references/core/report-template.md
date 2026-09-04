# Report Templates

Contents: A) Full report · B) Short form · C) Per-topic section format · D) Beyond-the-repo section · E) Evidence legend · F) Language and tone

## A) Full report (`03-report.md`)
1. **Executive summary** — 5–10 sentences. What the project is, the overall posture, the top 3 strengths, the top 3–5 risks, the headline recommendation. Lead with the bottom line; a reader who stops here must still get the verdict.
2. **Project identity card** — from `01-foundation.md`.
3. **How this audit was produced** — mode, depth, inputs available/absent, topics run / skipped (with reasons) / partial, environment (sub-agents, shell, GitHub-web-only), sampling applied. Two paragraphs at most; the manifest holds the detail.
4. **Scorecard** — one table: topic · score 1–5 · one-line justification · confidence. Then the overall rating and the 3–5 findings driving it.
5. **Cross-cutting findings** — root causes that surfaced in several topics (e.g., "no domain model" driving findings in T01, T04, T09, T18).
6. **Per-topic findings** — one section per audited topic in menu order, using format C. Include only findings of severity Medium or higher in the body; Low/Info go to the appendix.
7. **Business analysis** — T12 summary plus the cost view from T13, the legal posture from T14, and the AI exposure from T16 if run: product and users; business model and monetization; pricing/tiers; markets; growth signals; vendors and operating cost; unit economics; maturity and team; IP exposure; strategic read. Every paragraph tagged.
8. **Risk register** — table: ID · finding · topic · severity · impact · likelihood · ease · priority · tag/confidence · recommendation · effort.
9. **Recommendations roadmap** — Quick wins (now) · Short-term (this quarter) · Strategic (this year). Each with the expected benefit and the condition under which the recommendation would change.
10. **Open questions** — everything `[Could not determine]`, with the input that would resolve each.
11. **Beyond the repo** (due-diligence mode, or on request) — section D.
12. **Confidence statement** — overall confidence and the biggest sources of uncertainty.
13. **Appendices** — inventory map; git statistics; dependency and license table; SBOM notes; rules catalog (T09); decision log (T11); commands run or fallbacks used; Low/Info findings; the manifest.

## B) Short form (quick preset, or depth = short)
- Identity card (compact, 6 lines).
- Scorecard table.
- Top 5 findings (severity, evidence, fix, effort).
- Top 3 business inferences (tagged).
- Top 3 recommendations.
- Confidence and the key unknowns.

## C) Per-topic section format
```
## T04 — Testing & quality assurance · Score 2/5 (Weak) · Confidence Medium
**Verdict.** Two sentences.
**Findings.**
- T04-001 (High, [Observed], High) — title. Evidence. Recommendation (effort).
- ...
**Strengths.** What is done well here (evidence).
**Not covered / open.** Sampling gaps and open questions.
```

## D) Beyond-the-repo section
A table of requests compiled from T15 and every topic's open questions: item · why it matters · which finding it would resolve · status (requested / received / not available). Close with a sentence on how missing items limit confidence.

## E) Evidence legend (top of every report)
`[Observed]` fact with a file/commit/line reference · `[Inferred]` conclusion with its evidence chain · `[Speculative]` hypothesis · `[Not sampled]` deliberately not examined · `[Could not determine]` examined, inconclusive. Each carries confidence High / Medium / Low. Inference is never presented as fact.

## F) Language and tone
Write the report in the user's language (Hebrew if they write Hebrew) and keep technical terms, identifiers and file paths in English. Be specific and located; no moralizing about style, no padding, no praise or blame beyond what the evidence supports. In due-diligence mode, adopt a skeptical posture and name deal-repricing items explicitly. In self-audit mode, lead with what to fix first and why.
