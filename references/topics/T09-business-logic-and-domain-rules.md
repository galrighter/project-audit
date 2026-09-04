# T09 — Business Logic & Domain Rules

**Purpose.** Extract the logic the project actually enforces — the decisions frozen in code: the domain model, business rules and invariants, state machines, calculations, validation and edge cases, feature flags and configuration-driven behavior, magic values, integration contracts, implicit assumptions, and the permission model. Produce a rules catalog that documents the system as it is, not as the docs say.
**Start from.** Schema and core types, domain/service layer, validators and guards, middleware, controllers, configuration and flags, tests (tests document intended behavior).
**Cross-links.** Data integrity constraints are T07 · pricing/tiers feed T12 · flags and constants as decisions feed T11 · calculations without tests feed T04 · authorization enforcement is also T02.

## Method (in this order)
Schema → core domain services → validators → middleware/guards → controllers/handlers → tests → configuration and flags → docs. Record each rule with its location as you go; tag `[Observed]`. Where a rule is only implied (by a test or a constant), tag `[Inferred]`.

## Checklist

### A. Domain model and language
Entities, value objects, aggregates; the ubiquitous language (do code, DB and docs use the same terms?); where the domain lives (a domain layer, or scattered through controllers and SQL).

### B. Business rules and invariants
What must always be true (balances non-negative, one active subscription per user, an order cannot ship unpaid) · where each is enforced (DB constraint, service guard, UI only) · consistency: enforced on every path, or only on the happy path? Rules enforced only in the UI are findings.

### C. State machines and workflows
Status fields (order, subscription, onboarding, approval, delivery, ticket) · allowed transitions and guards · transitions with no guard · transition logic duplicated in several places · terminal states and recovery paths · time-based transitions (expiry, grace periods).

### D. Calculations
Pricing, discounts, tax/VAT, currency conversion, rounding, proration, quotas, scoring/ranking, commissions, payouts. Locate each, note the precision and rounding rules, whether tests exist (→T04), and where constants live.

### E. Validation and edge cases
Null/empty handling, boundaries (0, negative, max), timezone and date arithmetic, Unicode and RTL text (→T17), concurrency (double-submit, double-refund), partial failures in multi-step operations, retries producing duplicates.

### F. Feature flags and configuration-driven behavior
Every flag/toggle and what it gates · flags on/off permanently (decisions frozen in config → T11) · experiments and gradual rollouts · plan or tier gating (`if plan == 'pro'`, entitlement checks → T12) · environment-specific behavior differences.

### G. Hard-coded constants and magic values
Limits, thresholds, price points, retry counts, timeouts, cron schedules, allow-lists, email addresses, IDs. Each encodes an undocumented decision; list them with location and a guess at the intent.

### H. Integration contracts
Per external system: request/response shape expected, versioning, idempotency handling, webhook events consumed and verification, failure behavior and retries, data ownership (who is the source of truth).

### I. Permission model
Roles and capabilities, ownership checks, admin overrides, impersonation, per-tenant admin — as business rules (enforcement quality is T02).

### J. Implicit assumptions
Single currency, single timezone, single tenant, English-only, always online, "every user has an email", "prices never change mid-cycle", "one warehouse", "quantities are integers". Each is a constraint on the business and a future-work item.

### K. Divergences
Rules that contradict the docs, each other, or the tests; rules only reachable through dead code; behaviors that exist for one client or one customer (special cases hard-coded).

## Output
Findings `T09-###`, plus the **rules catalog**: ID · rule · location · enforced where (DB/service/UI) · consistently? · tested? · decision source if known (ADR/commit/PR) · confidence. Add the state-machine transition tables, the constants table, the integration-contract table, the assumptions list, score 1–5 with anchor (how well the logic is modeled, centralized, consistent and tested), and open questions.
