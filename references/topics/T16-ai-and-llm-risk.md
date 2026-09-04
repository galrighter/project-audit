# T16 — AI / LLM Risk

**Purpose.** Audit everything that involves models: where AI is used and how, the LLM-specific threat surface (OWASP Top 10 for LLM Applications 2025), data sent to providers, agent permissions and tool scoping, model and vendor dependency, cost and rate controls, evaluation and prompt hygiene, AI-generated-code risk in the codebase itself, and regulatory exposure. Applies when the product uses models or when the codebase was substantially built with coding agents.
**Start from.** Provider SDK usage (`openai`, `anthropic`, `google-generativeai`, `cohere`, `mistral`, `transformers`, `langchain`, `llamaindex`, `vercel/ai`), prompt files and templates, tool/function definitions, vector stores, model config, the T10 AI-assistance signals.
**Cross-links.** Application security is T02 · data classification is T07 · inference cost is T13 · model/dataset licenses are T03 · regulation is T14 · quality of AI-written code is judged with T01/T04 evidence.

## Checklist

### A. AI usage inventory
Providers and models (pinned versions?) · where calls happen (request path, jobs, agents) · prompts and their location (files, DB, hard-coded) · tools/functions the model can invoke and what they can do · RAG components (embeddings, vector store, retrieval sources) · fine-tuned or self-hosted models and their licenses · human-in-the-loop points.

### B. OWASP Top 10 for LLM Applications (2025)
LLM01 Prompt injection (direct and indirect via retrieved content, files, web pages, emails) · LLM02 Sensitive information disclosure (secrets, PII, other tenants' data in context) · LLM03 Supply chain (models, datasets, plugins, hubs) · LLM04 Data and model poisoning · LLM05 Improper output handling (model output into SQL, shell, HTML, code eval, downstream tools) · LLM06 Excessive agency (over-broad tools, autonomous actions without confirmation) · LLM07 System prompt leakage (secrets or logic in system prompts) · LLM08 Vector and embedding weaknesses (access control on retrieved chunks, cross-tenant leakage) · LLM09 Misinformation (unverified outputs presented as fact in high-stakes flows) · LLM10 Unbounded consumption (no limits on tokens, loops, cost). For each: is it reachable here, what defenses exist, evidence.

### C. Data sent to providers
Which data classes leave the system (→T07 inventory) · provider terms: training opt-out, zero-data-retention, region · DPAs (→T14) · redaction/pseudonymization before sending · logging of prompts/completions (PII in logs) · user consent and disclosure that AI processes their data.

### D. Agents and tool scoping
Least-privilege tools (read vs write, scoped credentials) · confirmation for irreversible or high-impact actions (payments, deletions, sends) · sandboxing for code execution and browsing · loop and step limits · audit trail of agent actions · kill switch.

### E. Reliability and quality of AI features
Output validation (schemas, JSON parsing with fallbacks) · handling of refusals, timeouts, rate limits, provider outages (fallback model or graceful degradation → T06) · evaluation suite for prompts (golden sets, regression tests) · prompt versioning and change review · monitoring of quality drift and user feedback · hallucination containment in high-stakes contexts (citations, grounding, human review).

### F. Cost and rate controls
Max tokens and context caps · caching (prompt caching, response caching) · model routing (cheaper models for easy tasks) · per-user/per-tenant quotas · budget alerts (→T13) · unit economics: cost per AI interaction vs price (→T12).

### G. Vendor and model dependency
Single-provider lock-in · abstraction layer for swapping · pinned model versions and deprecation tracking · self-hosting feasibility · exit plan.

### H. AI-generated code in the codebase (when T10 shows heavy agent use)
Review depth on AI-authored commits (unreviewed generated code is the risk) · duplication and boilerplate sprawl · inconsistent conventions across generated areas · tests that mirror implementation · dead code and unused abstractions · secrets or placeholder credentials left by generation · license contamination (verbatim code from training data) · maintainability of code no human fully understands (ask in T15: "who understands this module?").

### I. Regulatory and responsible-use exposure
EU AI Act risk tier for the use case (prohibited / high-risk / limited / minimal) and transparency duties (disclose AI interaction, label generated content) · sector rules (health, finance, employment, education) · bias and fairness where AI affects individuals' outcomes · content provenance and moderation for generated content · records of model decisions where required (→T14).

## Signals and red flags
User content concatenated into the system prompt with no isolation · model output passed to `eval`/SQL/shell · tools with delete/pay capabilities and no confirmation · no max tokens and no budget alert on a public feature · PII sent to a provider with training enabled · prompts changed in production with no eval run · a large share of commits co-authored by an agent with zero review comments.

## Commands (shell) and no-shell fallbacks
- Grep provider SDKs and model names; locate `system`/`prompt` strings; find tool definitions (`tools=`, `functions=`, `@tool`); check for `max_tokens`, `timeout`, caching; run promptfoo or the project's eval suite if present.
- No shell: GitHub code search for the same terms; read prompt files and tool definitions; read provider config and env examples.

## Output
Findings `T16-###`, the AI usage inventory table, the LLM Top-10 assessment table (risk · reachable · defenses · evidence), the data-to-provider table, the agent-permission review, the cost-control checklist, the AI-generated-code verdict, the regulatory note, score 1–5 with anchor, and open questions.
