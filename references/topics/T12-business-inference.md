# T12 — Business & Commercial Inference

**Purpose.** Read the business out of the code: what the product is, for whom, how it makes (or intends to make) money, at what prices and tiers, in which markets, how it grows, which vendors and partners it depends on, how mature it is, how the team works, what its unit economics look like, where IP and strategic risks sit — the commercial half of due diligence, produced from repository evidence and tagged accordingly.
**Start from.** README and marketing/landing pages in the repo; routes and pages; roles/permissions; billing and payment code; pricing/plan config; i18n, currency and tax code; analytics/CRM SDKs; integrations; IaC (vendors); the T10 summary (team, cadence); T09 rules if available; T13 for costs.
**Cross-links.** Cost estimate and unit-economics inputs come from T13 · legal posture is T14 · AI cost and exposure are T16 · team dynamics are T10 · pivots are T11 · questions the repo cannot answer go to T15.

## Checklist

### A. Product and users
What it does and the core job it solves · B2C / B2B / B2B2C / marketplace / internal · user types from roles and permission models (admin, staff, customer, partner, reseller) · onboarding flow and activation moment · core loop.

### B. Business model and monetization (read the payment code)
Payment SDKs and providers (Stripe, PayPal, Paddle, Chargebee, Lemon Squeezy, RevenueCat/Adapty, local processors) · webhook handlers reveal the model precisely: subscription events → recurring SaaS; one-time payment intents only → transactional; trial events → free-trial funnel; usage/metering events → usage-based; marketplace splits/transfers → take rate; ads SDKs → ad-supported · invoices, contracts, "contact sales" paths → sales-led motion vs self-serve checkout · no billing code on a live product → pre-revenue, external monetization, or an internal tool.

### C. Pricing, tiers and gating
Price IDs and plan definitions · number of tiers and what each gates (entitlement checks, feature flags keyed by plan → T09) · trials, coupons, referral credits · usage limits and overage · currency handling and regional pricing · grandfathered plans (legacy price IDs).

### D. Markets and geography
UI languages (locale files) vs transaction markets (currency codes, tax logic, address formats) · tax/VAT code (EU VAT validation, US sales-tax nexus, Israel VAT) → where it sells · compliance code as a market tell (GDPR → EU, CCPA → California, HIPAA → US healthcare, PCI → card payments) · regions in IaC → latency/data-residency targets · RTL support → Hebrew/Arabic markets (→T17).

### E. Growth and marketing signals
Analytics SDKs (Segment → data-mature; Mixpanel/Amplitude/PostHog → product-led growth; GA4 → acquisition focus; session replay → UX research) · referral, affiliate, coupon and invite mechanics → growth loops · SEO artifacts (sitemaps, structured data, blog generators) → content strategy · email/CRM/lifecycle tooling (Customer.io, HubSpot, Mailchimp, Resend/SendGrid sequences) → funnel stages and retention mechanics · social/OAuth logins → acquisition channels · A/B testing frameworks → experimentation maturity.

### F. Integrations, partners and vendors
Third-party APIs and what each provides (auth, payments, messaging, maps, search, data, AI) · partner-facing APIs or webhooks → ecosystem play · vendor concentration: which single vendor's loss breaks the product · contractual signals (rate limits, keys per environment) · white-label or reseller mechanics.

### G. Maturity and stage
Prototype / MVP / growth / mature / legacy from: feature completeness, test and CI maturity (T04/T10), observability (T06), docs (T08), migration history (T07), release cadence, presence of billing and support tooling. Note the gap between the stage the product claims (marketing) and the stage the code shows.

### H. Team and operating capacity (from T10)
Team size and shape, bus factor, velocity trend, contractors vs core, working hours pattern, AI-assisted share of development.

### I. Unit economics (with T13)
Per-user or per-transaction cost drivers (infra, paid APIs, LLM inference, messaging) vs price points → gross-margin band with assumptions. Flag inverted economics (a feature costs more per use than it earns) and costs that scale faster than revenue.

### J. IP and ownership flags
Copyleft exposure (T03) · unclear-origin code · assets, domains, cloud/app-store/payment accounts held personally (T14) · trademark conflicts in the product name (T14) · ML models or datasets with restrictive licenses (T16).

### K. Strategic read (tagged Speculative where it is)
Moat signals: proprietary data, integrations, switching costs, network effects, workflow depth · competitive position implied by the feature set vs known alternatives · dependence on a platform's goodwill (app stores, a single marketplace, an API provider) · roadmap credibility (T11 planning evidence vs stated ambitions) · regulatory exposure of the model itself (e.g., prediction markets/betting, lending, health, children → T14).

### L. Risk register inputs
Single vendor dependence · key-person risk · compliance gaps · unit-economics risk · platform risk · market concentration (one big customer visible in code or config) · anything a buyer or investor would reprice on (→ evidence-and-scoring §8).

## Evidence discipline
Almost everything here is `[Inferred]` or `[Speculative]`. Show the chain for each inference (which files, which events, which config), state the alternative reading (sandbox keys, dead billing code, a provider used only for testing), and give confidence. Never present a business conclusion as `[Observed]` unless a document in the repo states it.

## Commands (shell) and no-shell fallbacks
- Grep for providers: `stripe|paypal|paddle|chargebee|braintree|revenuecat|adapty|lemonsqueezy`, `segment|mixpanel|amplitude|posthog|gtag|hotjar|fullstory`, `hubspot|customerio|mailchimp|sendgrid|resend|twilio`, `i18n|locales|Intl\.|currency|vat|tax`, `plan|tier|entitlement|feature_flag`.
- No shell: GitHub code search with the same terms; read config/env examples for service names; read pricing pages or landing copy in the repo.

## Output
Findings `T12-###`, plus a one-paragraph **business thesis** ("what this business is") with confidence, the business-model table (model · evidence · confidence), the pricing/tier table, the markets table, the vendor-concentration table, the growth-signal list, the maturity verdict, the unit-economics band (with T13), the IP/strategic risk list, score 1–5 with anchor (commercial health and clarity as evidenced by the code), and open questions for T15.
