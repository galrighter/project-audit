# Evidence, Severity & Scoring

Contents: 1) Evidence tags · 2) Confidence · 3) Finding format · 4) Severity · 5) Priority · 6) Dimension scorecard · 7) Overall rating · 8) Deal-repricing checks (due diligence) · 9) Benchmarks are reference points

## 1) Evidence tags (every claim carries one)
- `[Observed]` — directly seen. Cite the location: `path/file.ext:line`, commit hash, PR number, config key, Insights graph. Without a location it is not Observed.
- `[Inferred]` — a conclusion from evidence. State the chain: "Stripe `customer.subscription.*` webhooks handled (src/billing/webhooks.ts) + three Price IDs in config → subscription SaaS with three tiers."
- `[Speculative]` — a hypothesis worth stating because it changes decisions. Say what evidence would confirm or refute it.
- `[Not sampled]` — an area deliberately not examined (large-repo sampling); say why.
- `[Could not determine]` — examined, but the evidence does not support a conclusion; say what input would resolve it.

Inference presented as observation is the single most damaging failure in an audit. When unsure, downgrade the tag.

## 2) Confidence
- **High** — multiple independent evidence points, or a direct observation with no plausible alternative reading.
- **Medium** — one solid evidence point, or several weak ones; an alternative reading exists.
- **Low** — pattern-matching, partial evidence, or reliance on approximations (GitHub Insights graphs, sampled files).
Security findings: after the adversarial self-check (is it reachable? what defenses exist? what is the real impact?) cap confidence at **Medium** if any of the three is unknown.

## 3) Finding format
```
### T07-004 · Personal data retained indefinitely
- Tag: [Observed] · Confidence: High · Severity: High
- Evidence: prisma/schema.prisma:88 (no deletedAt/TTL on User, Order), no deletion endpoint in src/api/**, GDPR mentioned in docs/privacy.md:12
- Why it matters: right-to-erasure requests cannot be fulfilled; backups and analytics exports compound it
- Recommendation: add soft-delete + purge job; document retention per table (effort: M)
```

## 4) Severity of a finding
- **Critical** — exploitable security hole, data-loss path, licensing/legal deal-stopper, or an imminent-outage single point of failure.
- **High** — significant risk to security, scalability, maintainability or the business, likely to bite within months.
- **Medium** — real weakness, manageable timeframe.
- **Low** — minor, cosmetic, or nice-to-have.
- **Info** — observation with no action required (useful context for the reader).

## 5) Priority (for the risk register and roadmap)
Score each finding 1–5 on **Impact** (blast radius if it goes wrong), **Likelihood** (how probable given current evidence) and **Ease of fix** (5 = trivial, 1 = major project). `Priority = Impact × Likelihood × Ease`. Rank the register by priority. Present **quick wins** (impact ≥ 4, ease ≥ 4) separately from **strategic** items (impact ≥ 4, ease ≤ 2). Effort tiers: S (hours), M (days), L (weeks), XL (quarter+).

## 6) Dimension scorecard (1–5 with anchors)
Score every audited topic; the report shows them in one table with a one-line justification each.
- **5 Excellent** — best practice, few gaps, well evidenced; nothing you would change first.
- **4 Good** — solid with minor gaps that are cheap to close.
- **3 Adequate** — works, notable gaps, some real risk; typical for a healthy early-stage product.
- **2 Weak** — significant gaps and real risk; needs planned investment.
- **1 Poor** — largely absent or broken; urgent.
State the evidence behind each score and your confidence in it. A topic marked `partial` gets a score with `(partial)` and lower confidence; a skipped topic shows `—`.

## 7) Overall rating
Roll up into an overall posture for the chosen mode: **Low / Moderate / Elevated / High risk**, driven by the 3–5 findings that most determine it (name them). Do not average the scorecard — one Critical finding outweighs ten 4s.

## 8) Deal-repricing checks (due-diligence mode)
Treat each as deal-repricing or deal-stopping until disproven, and escalate any hit to the executive summary:
- Secrets committed to the repo or its history.
- GPL/AGPL/SSPL code linked into proprietary or SaaS code; forks with unclear licenses; missing contributor IP assignment.
- Bus factor of 1 on a critical service, or only one person able to deploy/restore.
- No tests on payment, auth, tax/pricing, or data-migration paths.
- No tested backup restore; no stated RTO/RPO for a revenue-bearing system.
- Actual data handling contradicting the published privacy policy; personal data of children or health/financial data without the matching controls.
- Core platform or data owned through a personal account (domain, cloud, app-store, payment processor) rather than the entity.
- A rewrite in progress with no migration plan; EOL runtime or framework in production.
- Unit economics inverted (a feature costs more per use than it earns), or a single vendor whose loss breaks the product.

## 9) Benchmarks are reference points, not verdicts
Numbers such as "coverage < 40% is a red flag, 60–80% is good", "duplication > 10%", or the DORA elite bands (deploy on demand, lead time < 1 day, restore < 1 hour, change-failure rate 0–15%) come from vendor checklists and the Accelerate reports. Use them to frame a finding, never to replace judgment: 30% coverage concentrated on payment and auth paths beats 80% of snapshot tests. Say which benchmark you used and why it applies.
