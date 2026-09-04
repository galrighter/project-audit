# T14 — Legal & Regulatory Compliance

**Purpose.** Determine which laws and standards plausibly apply, whether the code and configuration match the stated policies, and where legal exposure sits beyond open-source licensing: privacy law, sector regulation, consumer and e-commerce rules, accessibility law, export control, trademarks, ownership of critical accounts and assets, contributor IP, AI regulation, and compliance evidence. This is an engineering read of legal exposure, not legal advice — say so in the report and recommend counsel where stakes are high.
**Start from.** Privacy policy/terms in the repo or site, consent and cookie code, data-flow map (T07), payment/tax code, age gates, geo-blocking, LICENSE/NOTICE (T03), config for domains/accounts, AI features (T16), accessibility state (T17).
**Cross-links.** Open-source licensing is T03 · data handling is T07 · accessibility conformance is T17 · AI-specific regulation is T16 · documents to request (DPAs, contracts, audits) are T15.

## Checklist

### A. Regulatory scope detection (mark each: applies / likely / no evidence)
Privacy: GDPR/UK GDPR (EU/UK users or data) · CCPA/CPRA (California) · other national laws relevant to the markets found in T12 (for Israel: the Privacy Protection Law as amended — verify current amendment status) · children's data (COPPA, age gates) · ePrivacy/cookie consent. Sector: PCI-DSS (card data touched or fully outsourced via tokenization?) · HIPAA/health data · financial regulation (payments, lending, crypto, gambling — prediction markets and betting products carry licensing exposure by jurisdiction) · telecom/messaging rules (SMS consent, TCPA-style) · marketplace/platform rules. Accessibility law: ADA/Section 508, EU Accessibility Act, national standards (e.g., Israel IS 5568) → T17. AI regulation: EU AI Act risk tiers → T16.

### B. Policy vs code
Privacy policy and terms exist and are versioned · what the policy promises vs what the data-flow map shows (retention, sharing, third parties, tracking before consent) · consent management implemented and honored in code paths (analytics, marketing sends, cookies) · data subject rights paths (access, export, erasure, objection) actually implemented · breach notification readiness (who, how fast) · age verification where required.

### C. Contracts and processors (evidence in repo or via T15)
Data processing agreements with processors (analytics, email, LLM providers, cloud) · sub-processor list · standard contractual clauses for cross-border transfers · terms with API providers permitting the observed use · customer contracts' SLAs vs delivered reliability (→T06).

### D. Payments and consumer rules
Refund and cancellation flows, disclosures, auto-renewal notices, receipts/invoices, tax collection and reporting per market, dark-pattern risk in checkout, currency and price transparency.

### E. Trademarks and brand
Product name, logo and domain: check for obvious conflicts in the relevant registries (USPTO, EUIPO, WIPO Global Brand Database, Israel Patent Office) and app stores; tag results `[Inferred]` and recommend a clearance search when the name is central to the business.

### F. Export control and sanctions
Encryption components and their export classification where distribution crosses borders (EAR/dual-use rules) · geo-blocking or screening for sanctioned jurisdictions where the product's sector requires it · app-store export compliance declarations.

### G. Ownership of critical assets and accounts
Domains, cloud accounts, app-store accounts, payment processor accounts, OAuth apps, code hosting: held by the entity or by an individual? Recovery paths if that individual is unavailable (→T06 SPOFs). Repository and package-registry ownership. Contributor IP assignment for employees and contractors (→T03/T15).

### H. Records and evidence of compliance programs
SOC 2 / ISO 27001 reports, pen-test reports, DPIAs, records of processing activities, security policies, training — present in repo or requested via T15. Absence is normal for early-stage; absence while claiming certification on the website is a finding.

### I. Content, IP and platform obligations
User-generated content: moderation, takedown/DMCA process, terms of use · third-party content licenses (fonts, images, datasets, maps) → T03 · platform policies (app stores, marketplaces, API providers) that the observed use might violate.

## Signals and red flags
Analytics fired before consent · "we do not sell data" while a data broker SDK is integrated · no erasure path with EU users · card numbers touching the server · a betting/prediction/lending model with no licensing discussion anywhere · the founder's personal Gmail owning the domain and the cloud account · "SOC 2 compliant" on the landing page with no evidence.

## Commands (shell) and no-shell fallbacks
- Grep consent/tracking init order, `age`, `birthdate`, `cookie`, `gdpr`, `ccpa`, `hipaa`, `pci`, refund/cancel routes; list third-party SDKs (T07/T12).
- No shell: read policy pages and the same files via GitHub; registry searches through their public websites; request documents via T15.

## Output
Findings `T14-###`, the regulatory-scope table (regime · applies? · evidence · gaps), the policy-vs-code table, the asset-ownership table, the compliance-evidence table, a clear "engineering read, not legal advice" note, score 1–5 with anchor (alignment of practice with stated obligations), and open questions for counsel and for T15.
