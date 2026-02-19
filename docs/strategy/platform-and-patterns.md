# Platform and Patterns

*Why standardising on approved platforms, trusted models, proven patterns, and consistent data access is the single biggest force multiplier for AI governance.*

> Part of [AI Strategy](./)

---

## Why This Matters

The [Use Case Filter](use-case-filter.md) answers "what kind of solution?" This article answers "how does it run?"

An enterprise with twenty AI systems running on eight platforms, using fifteen models, with twelve different logging formats and six different approaches to RAG has twenty governance problems. The same enterprise with those twenty systems running on two approved platforms, using four trusted models, following three reference patterns, with one logging schema has a manageable governance problem.

Standardisation is not about restricting innovation. It's about making governance affordable. Every unique combination of platform, model, pattern, and data access method is a new surface to secure, a new set of controls to configure, a new operational playbook to write, and a new skill set for operations teams to learn. Multiply that by the number of AI systems and the governance cost becomes unmanageable.

![Platform and Patterns](../../images/strategy-platform-patterns.svg)

The principle is straightforward: **approved platforms, trusted models, proven patterns, standardised data access, consistent operations.** Teams get freedom to solve novel business problems. They don't get freedom to invent novel infrastructure.

---

## The Five Layers of Standardisation

### Layer 1: Approved Platforms

An approved platform is infrastructure that has been evaluated, configured, and hardened for AI workloads. The governance function has verified that the platform supports the framework's control requirements, that logging and monitoring are configured to standard, and that the operations team knows how to run it.

**What "approved" means:**

| Criterion | Why It Matters |
|-----------|---------------|
| Guardrail integration is verified | You know the platform can enforce input/output controls |
| Judge deployment pattern exists | You know how to run evaluation on this platform |
| HITL routing is configured | You know how to get AI output to human reviewers |
| Logging meets LOG-01 through LOG-10 | You know what telemetry you'll get |
| Network zones are mapped | You know where trust boundaries sit |
| IAM delegation model is defined | You know how identities and permissions flow |
| Incident response playbook exists | You know what to do when something goes wrong |
| Operations team has trained | You know someone can actually run this |

**The typical approved set is small — two or three platforms:**

| Platform Type | Example | Typical Use |
|---------------|---------|-------------|
| Managed AI service | AWS Bedrock, Azure AI, GCP Vertex AI | Customer-facing applications, standard GenAI |
| Data + ML platform | Databricks, Snowflake + partners | Analytics, traditional ML, data-intensive workloads |
| Specialist platform | Palantir Foundry, domain-specific | Regulated industries, operational AI, defence |

Each platform has a [platform adapter](../extensions/regulatory/platform-integration-guide.md) that translates the framework's control standards to platform-native configuration. The adapter is built once and reused across every system on that platform.

**What happens when a team wants a new platform:**

The platform must be evaluated against the criteria above before any AI system ships on it. This isn't a blocker — it's an investment. Once the platform is approved, every subsequent system gets the benefit of the work already done. The question is: does this platform offer something the approved set genuinely can't deliver? If yes, invest in the evaluation. If the answer is "we just prefer it" or "we used it at my last company," that's not sufficient.

---

### Layer 2: Trusted Models

A trusted model is one the organisation has evaluated for capability, security characteristics, licensing terms, and operational behaviour. It doesn't mean the model is perfect. It means the organisation understands its failure modes, has benchmarked its performance for relevant use cases, and has configured the appropriate Judge and guardrail pairing.

**What "trusted" means:**

| Criterion | Why It Matters |
|-----------|---------------|
| Capability benchmarked for relevant tasks | You know what it's good at and where it struggles |
| Security characteristics documented | You know how it responds to adversarial inputs |
| Licensing and data handling terms reviewed | Legal has confirmed the terms are acceptable |
| Judge pairing defined | You know which Judge model evaluates this model's output |
| Guardrail baseline configured | You have a starting-point guardrail configuration |
| Cost profile understood | You can forecast operating costs |
| Fallback model identified | You know what to switch to if this model is unavailable |
| [Model card](../extensions/templates/model-card-template.md) completed | All of the above is documented |

**The trusted model catalogue is intentionally small:**

| Role | What It Does | Example Catalogue |
|------|-------------|-------------------|
| Primary generation | Produces the main AI output | 2–3 models (e.g., Claude, GPT-4, Gemini) |
| Judge evaluation | Evaluates the primary model's output | 1–2 models, [different provider](../extensions/technical/judge-model-selection.md) from primary |
| Embedding | Generates vector embeddings for RAG | 1–2 models |
| Classification / NLP | Non-generative tasks (sentiment, entities, routing) | 1–2 models or services |
| Fallback | Degraded-mode operation when primary is unavailable | 1 per primary model |

Each model in the catalogue has a defined [model card](../extensions/templates/model-card-template.md), a Judge pairing, a guardrail baseline, and a cost profile. When a new system needs a model, it picks from the catalogue. The guardrails, Judge configuration, and operational playbooks already exist.

**Model lifecycle management:**

Models change. Providers deprecate versions, release new ones, and change terms. The catalogue must be actively maintained:

| Activity | Frequency | Owner |
|----------|-----------|-------|
| Review model availability and deprecation notices | Monthly | Platform team |
| Benchmark new model versions against existing | On release | AI engineering |
| Update guardrail baselines for new versions | On adoption | Security |
| Recalibrate Judge when primary model changes | On adoption | Operations |
| Review licensing and data handling terms | Annually + on change | Legal |
| Retire deprecated models with migration path | On deprecation | Platform team |

---

### Layer 3: Proven Patterns

A proven pattern is a reference architecture for a common AI use case type. It defines how the components fit together: model, data sources, guardrails, Judge, HITL, logging, and PACE resilience. The pattern is security-reviewed, operationally tested, and documented.

**Why patterns matter:** Without them, every team designs its own architecture. Team A builds RAG with direct vector store queries. Team B builds RAG with a retrieval API layer. Team C builds RAG with a custom caching layer. Each one needs its own security review, its own guardrail configuration, its own logging integration, and its own operational runbook. With a standard RAG pattern, the security review happens once, the guardrail configuration is templated, the logging is pre-integrated, and the operations team knows exactly what they're looking at.

**The core pattern catalogue:**

| Pattern | When To Use | Key Components | Framework Controls |
|---------|-------------|----------------|-------------------|
| **Retrieval-Augmented Generation (RAG)** | AI needs to answer from organisational knowledge | Vector store + retrieval layer + LLM + [RAG security controls](../extensions/technical/rag-security.md) | Ingestion validation, chunk-level access control, source attribution, grounding evaluation |
| **Conversational Agent** | Multi-turn dialogue with users | Session management + LLM + guardrails + memory | Conversation boundary controls, PII redaction, topic guardrails, session-level monitoring |
| **Document Processing** | Extracting structured information from unstructured documents | Document parser + LLM + validation layer | Input validation, output schema enforcement, confidence scoring, human review for low-confidence |
| **Classification / Routing** | Categorising inputs or routing to appropriate handlers | LLM or traditional NLP + rules engine | Classification accuracy monitoring, fallback routing, bias detection |
| **Content Generation** | Producing text, summaries, or communications | LLM + output guardrails + human review | Tone/brand guardrails, factual accuracy evaluation, regulatory language checking |
| **Agentic Workflow** | Multi-step tasks requiring tool use and autonomous action | Orchestrator + tools + [sandbox](../infrastructure/agentic/sandbox-patterns.md) + [delegation chain](../infrastructure/agentic/delegation-chains.md) | Action validation, sandbox enforcement, budget limits, human approval gates, [MASO controls](../maso/) |

Each pattern includes:
- **Architecture diagram** — how the components connect
- **Guardrail template** — starting-point input/output rules for this pattern type
- **Judge configuration** — what the Judge evaluates and at what sampling rate
- **HITL design** — what humans review and when
- **Logging integration** — what telemetry the pattern produces
- **PACE specification** — how the pattern degrades gracefully
- **Known risks** — what typically goes wrong with this pattern type
- **Cost model** — expected operating cost by tier and volume

**Composing patterns:**

Real systems often compose multiple patterns. A customer service system might combine:
- **RAG pattern** for knowledge base retrieval
- **Conversational Agent pattern** for multi-turn dialogue
- **Classification pattern** for intent routing

Composition is expected and supported. The principle is that each component follows its standard pattern. The composition layer — how patterns connect — is where novel architecture happens, and that's where the security review focuses.

---

### Layer 4: Standardised Data Access

AI systems need data. How they access it should be predictable, auditable, and consistent across every system.

**The problem with ad-hoc data access:** If each AI system builds its own data integration — direct database queries here, custom API calls there, file system reads elsewhere — you get inconsistent access control enforcement, inconsistent audit trails, and no single view of what data each AI system can reach.

**Standardised data access means:**

| Principle | Implementation | Why |
|-----------|---------------|-----|
| **Access through approved connectors** | Data access layer with vetted connectors for each source type | Consistent authentication, authorisation, and audit logging |
| **Identity propagation** | AI system's identity (not a shared service account) flows to the data source | Audit trail shows which system accessed which data |
| **Schema-aware retrieval** | Connectors understand the data model, not just raw access | PII detection and redaction happen at the connector level |
| **Access control at the data layer** | Permissions enforced by the data source, not by the AI application | Defence in depth — application can't access what it shouldn't |
| **Consistent caching policy** | Standard caching rules by data classification | Sensitive data isn't cached where it shouldn't be |
| **Change detection** | Standard mechanism for detecting when source data changes | RAG knowledge bases stay current; stale data flagged |

**For RAG specifically:**

RAG is the most common AI data access pattern, and the one most likely to introduce security gaps. The [RAG security controls](../extensions/technical/rag-security.md) define ingestion, embedding, retrieval, and augmentation controls. Standardisation means every RAG implementation uses the same ingestion pipeline, the same chunk-level access control model, and the same source attribution format. When a vulnerability is found in one RAG system's ingestion pipeline, the fix applies to all of them because they all use the same pipeline.

**Approved data source catalogue:**

| Source Type | Approved Connector | Access Pattern | Sensitivity Handling |
|-------------|-------------------|----------------|---------------------|
| Internal knowledge base | Document ingestion pipeline (standard) | RAG — chunked, embedded, access-controlled | PII scanning at ingestion |
| Customer data (CRM, accounts) | API gateway with identity propagation | Real-time query, scoped to current customer | PII redacted from AI context where possible |
| Transaction data | Read-only API with row-level security | Query with filters, aggregated where possible | Financial data classification applied |
| External data (web, third-party) | Vetted ingestion with content validation | Batch ingestion with adversarial content scanning | Treated as untrusted — additional guardrails |
| Internal documents (policy, procedures) | Standard document ingestion | RAG — chunked, embedded | Classification inherited from source |

---

### Layer 5: Consistent Operations

This is where standardisation delivers its highest return. When every AI system runs on an approved platform, uses a trusted model, follows a proven pattern, and accesses data through standard connectors, the operational picture becomes manageable.

**What consistency gives operations teams:**

| Area | Without Standardisation | With Standardisation |
|------|------------------------|---------------------|
| **Logging** | Twelve formats, six schemas, three storage locations | One [logging schema](../infrastructure/controls/logging-and-observability.md), one aggregation pipeline, one retention policy |
| **Monitoring** | Custom dashboards per system, unique alert rules | Standard dashboards per pattern type, shared alert catalogue |
| **Incident response** | Unique playbook per system | Playbook per pattern type, shared escalation paths |
| **Judge evaluation** | Custom Judge prompts everywhere, inconsistent calibration | Judge library by pattern type, shared calibration process |
| **Guardrail management** | Unique guardrail sets per system, no shared learning | Guardrail templates by pattern type, shared block list, coordinated updates |
| **HITL operations** | Separate queues per system, different reviewer training | Unified queue with pattern-based routing, standardised reviewer training |
| **Capacity planning** | Guess per system | Predictable cost model per pattern × tier × volume |
| **Skills required** | Specialist knowledge per system | Deep expertise per pattern type, portable across systems |

**The operational multiplier:**

Consider an organisation running fifteen AI systems. Without standardisation, the operations team needs to understand fifteen architectures, maintain fifteen monitoring configurations, and respond to incidents across fifteen different technology stacks. The team either becomes very large or very stretched.

With standardisation — say, two platforms, three patterns — the team needs to understand two platforms and three patterns deeply. A RAG system on Bedrock looks like every other RAG system on Bedrock. The monitoring is the same. The failure modes are the same. The runbook is the same. The team develops deep expertise in a few patterns rather than shallow familiarity with many.

This is the resource argument: standardisation lets limited operations and security teams cover more AI systems effectively. It's not about restricting teams. It's about making the finite resources — security reviewers, Judge calibrators, HITL operators, incident responders — go further.

---

## How the Layers Compose

```
BUSINESS PROBLEM
    │
    ▼
USE CASE FILTER ──────────── What kind of solution?
    │
    ▼
PLATFORM AND PATTERNS ────── How does it run?
    │
    ├── Which approved platform?
    ├── Which trusted models?
    ├── Which proven pattern?
    ├── Which data connectors?
    │
    ▼
USE CASE DEFINITION ──────── What exactly does the AI do?
    │
    ▼
RISK CLASSIFICATION ──────── What tier?
    │
    ▼
CONTROL DESIGN ───────────── Standard controls + pattern-specific tuning
    │
    ▼
BUILD ────────────────────── Assemble from approved components
    │
    ▼
OPERATIONS ───────────────── Familiar logs, known patterns, standard playbooks
```

The key insight: control design becomes **pattern-specific tuning** rather than design from scratch. A new RAG system at tier HIGH doesn't need a bespoke control specification. It needs the standard RAG-at-HIGH template with tuning for the specific use case — specific topic guardrails, specific Judge evaluation criteria, specific HITL reviewer qualifications. The architecture, logging, monitoring, and PACE configuration are inherited from the pattern.

---

## The Catalogue Governance Model

The approved catalogue — platforms, models, patterns, connectors — needs governance of its own.

### Who Owns What

| Catalogue | Owner | Approval Authority |
|-----------|-------|-------------------|
| Approved platforms | Platform/infrastructure team | Architecture review board + Security |
| Trusted models | AI engineering team | Security + Legal + AI governance |
| Proven patterns | AI architecture team | Security + AI governance |
| Approved connectors | Data engineering team | Security + Data governance |

### How Items Enter the Catalogue

| Step | Activity | Gate |
|------|----------|------|
| 1 | **Request** — team identifies a need not met by current catalogue | Justified business need |
| 2 | **Evaluate** — technical assessment against framework requirements | Meets all control requirements |
| 3 | **Security review** — threat model, attack surface, control mapping | Acceptable residual risk |
| 4 | **Operational readiness** — logging configured, monitoring built, playbook written, team trained | Operations team sign-off |
| 5 | **Approve and document** — added to catalogue with full documentation | Governance committee approval |
| 6 | **Maintain** — ongoing review per lifecycle schedule | Remains current and supported |

### How Items Leave the Catalogue

| Trigger | Process |
|---------|---------|
| Provider deprecation | Migration path published, deadline set, systems migrated |
| Security vulnerability (unpatched) | Immediate restriction; systems moved to alternative |
| Licensing terms change | Legal review; if unacceptable, retirement with migration path |
| Better alternative available | Gradual transition; old item marked deprecated with sunset date |
| Operations team can no longer support | Retirement with migration path |

---

## Exceptions and Escape Hatches

Standardisation without escape hatches becomes rigidity. Some legitimate scenarios require deviation:

| Scenario | Process | Constraint |
|----------|---------|-----------|
| **Research / POC** | Use any platform/model, but in isolated sandbox with no production data | Time-bounded (max 90 days), no path to production without catalogue entry |
| **Vendor product** | Product may use non-standard platform/model | Must meet framework control requirements via vendor assessment; gap analysis for controls not provided natively |
| **Regulatory requirement** | Regulation may mandate specific platform, data residency, or model characteristic | Document the requirement; add to catalogue if reusable, treat as exception if not |
| **Performance requirement** | Use case may need a model or architecture not in the catalogue | Benchmark against catalogue options first; if genuinely not met, follow "How Items Enter" process |

The principle: **exceptions are allowed, but each exception carries its own governance cost.** An exception means a custom security review, custom operational playbook, custom monitoring, and custom incident response. That cost is explicit and visible. If the business value justifies it, proceed. If not, use the catalogue.

---

## Measuring Standardisation

How to know if standardisation is working:

| Metric | Target | Why |
|--------|--------|-----|
| **% of AI systems on approved platforms** | >90% | Shows catalogue coverage |
| **% of AI systems using trusted models** | >90% | Shows model governance adoption |
| **% of AI systems following a proven pattern** | >80% | Some systems will be genuinely novel |
| **Time from use case approval to production** | Trending down | Standardisation should accelerate delivery |
| **Security review time per new system** | Trending down | Pattern reuse reduces review scope |
| **Incident response time** | Trending down | Familiar systems are easier to diagnose |
| **Number of active exceptions** | Trending down or stable | Exceptions should be temporary |
| **Operations team coverage ratio** | Systems per ops team member increasing | Standardisation should improve leverage |

---

## Relationship to Other Articles

- **[Use Case Filter](use-case-filter.md)** determines what kind of solution — this article constrains how it's built
- **[Use Case Definition](use-case-definition.md)** defines the specific system — this article provides the component catalogue it draws from
- **[From Idea to Production](idea-to-production.md)** Stage 3 (Tool Selection) and Stage 5 (Control Design) both benefit from standardisation — the decisions are faster when the options are curated
- **[The Thread](the-thread.md)** Phase 4 (Build) and Phase 5 (Monitor) are where standardisation pays off — consistent builds, consistent operations
- **[Platform Integration Guide](../extensions/regulatory/platform-integration-guide.md)** implements the multi-platform governance this article calls for
- **[RAG Security Controls](../extensions/technical/rag-security.md)** defines the controls for the RAG pattern
- **[Judge Model Selection](../extensions/technical/judge-model-selection.md)** defines the principles for the Judge entries in the model catalogue
- **[Logging & Observability](../infrastructure/controls/logging-and-observability.md)** defines the logging standard that consistent operations relies on

---

*AI Runtime Behaviour Security, 2026 (Jonathan Gill).*
