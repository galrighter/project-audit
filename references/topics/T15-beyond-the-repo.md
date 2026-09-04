# T15 — Beyond the Repo: Requests to the Team

**Purpose.** A repository cannot answer everything a decision needs. This topic compiles what to ask for and whom to interview, tied to the specific open questions the other topics produced, and tracks what was received. Mandatory in due-diligence mode; available on request in self-audit mode (where it doubles as a "what we should be able to show" checklist).
**Start from.** Every topic file's `## Open questions`; the manifest; the mode.
**Cross-links.** All topics. Produced last (wave 3), usually by the orchestrator.

## Method
1. Collect every `[Could not determine]` and open question across topics.
2. Map each to a request (document, data, access, or interview question) and to the finding it would resolve.
3. Add the standard requests below that the open questions did not already cover, pruned to what matters for this project and mode.
4. Deliver the request list; as items arrive, update status and re-score the affected findings.

## Standard request bank (prune to relevance)

### Team and organization
Org chart and roles · who owns each system (compare with T10 ownership) · employees vs contractors, locations · IP assignment agreements for everyone who committed (T03/T14) · attrition in the last 24 months and planned hires · on-call arrangements (T06) · engineering budget and vendors.

### Finance and commercial
Revenue by product/plan, MRR/ARR, churn, expansion · gross margin and COGS (hosting, paid APIs, AI inference, payments fees) — compare with T13 · customer concentration (top 10 share) · pricing history and discounts in force · contracts with unusual terms (uptime SLAs, exclusivity, most-favored-customer) · runway and fundraising status.

### Customers and product
Analytics dashboards (activation, retention, feature adoption) — compare with the growth signals in T12 · NPS/CSAT · support-ticket volume and top themes (a proxy for defect areas → T01/T04) · roadmap and the reasons behind it (T11) · known customer-facing bugs and workarounds.

### Operations and reliability
Uptime history and status-page incidents · incident log and postmortems (T06) · last restore drill and DR test results · monitoring dashboards · capacity and cost bills for the last 3–12 months (T13) · deployment records.

### Security and compliance
Penetration-test reports and remediation status · vulnerability-management process · SOC 2 / ISO 27001 reports or roadmap · DPIAs, records of processing, DPAs with processors (T14) · cyber-insurance policy · access reviews and offboarding evidence · secrets-management practice.

### Legal and IP
Entity structure · trademarks, patents, domain ownership (T14) · open-source license review history · litigation or disputes · licenses and agreements for third-party data/models (T16) · terms and privacy policy versions.

### Technical
Architecture diagrams and design docs not in the repo · schema of externally managed databases (T07) · environments and access list · third-party accounts and who administers them · any code not in this repository (other repos, no-code automations, scripts on servers).

## Interview guide (30 minutes with the technical lead)
What would you rebuild first and why? · What breaks most often? · What are you afraid of? · Who could leave and hurt you most? · What decision do you regret? · What is undocumented that only you know? · How do you know the system is healthy right now? · What did the last incident teach you? · Where is the money made in the code, and who understands it? · What would 10× users break first?

## Output
The **request tracker**: item · why it matters · finding(s) it resolves · owner · status (requested / received / not available) · impact on confidence if missing. Plus the interview notes template and a sentence on how the unresolved items limit the report's confidence. This topic has no score.
