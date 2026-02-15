# Framework Map

Navigate the framework by role or goal. Pick a reading path, follow it, branch when something connects.

---

## Two Architectures, One Framework

This framework has two halves. The **Foundation** covers single-model AI deployments. **MASO** extends it to multi-agent orchestration. Both share the same three-layer pattern (Guardrails → Judge → Human Oversight) and PACE resilience methodology — MASO adds the controls needed when agents communicate, delegate, and act across trust boundaries.

[![Single-Agent Architecture](/images/single-agent-architecture.svg)](/images/single-agent-architecture.svg)

**Foundation** — Three-layer runtime security for single-model AI. Risk classification, 80 infrastructure controls, PACE resilience, fast lane for low-risk deployments. **→ [Start here](foundations/)**

[![MASO Tube Map](/images/maso-tube-map.svg)](/images/maso-tube-map.svg)

**MASO** — Six control domains, 93 controls, three implementation tiers, dual OWASP coverage. For systems where multiple agents collaborate autonomously. **→ [Start here](maso/)**

---

## Reading Paths

### "I need to explain this to leadership"

Start with the business case, not the controls.

1. [The First Control: Choosing the Right Tool](insights/the-first-control.md) — frames the design-thinking question
2. [Why Your AI Guardrails Aren't Enough](insights/why-guardrails-arent-enough.md) — the case for the Judge layer
3. [Humans Remain Accountable](insights/humans-remain-accountable.md) — accountability model
4. [When Agents Talk to Agents](insights/when-agents-talk-to-agents.md) — the multi-agent problem statement

Then the two operational gap articles most boards haven't heard about: [The Supply Chain Problem](insights/the-supply-chain-problem.md) and [RAG Is Your Biggest Attack Surface](insights/rag-is-your-biggest-attack-surface.md).

For multi-agent specifically, the [MASO worked examples](maso/examples/worked-examples.md) (financial services, healthcare, critical infrastructure) translate technical controls into business scenarios.

### "I'm deploying a single-model AI system"

Follow the foundation path:

1. [Quick Start](QUICK_START.md) — zero to working controls in 30 minutes
2. [Risk Tiers](core/risk-tiers.md) — classify your system
3. [Controls](core/controls.md) — implement the three-layer pattern
4. [PACE Resilience](PACE-RESILIENCE.md) — define fail postures and fallback paths
5. [Checklist](core/checklist.md) — track progress

If your system qualifies for the [Fast Lane](FAST-LANE.md) (internal, read-only, no regulated data, human-reviewed), start there instead — minimal controls, self-certification, deploy in days.

If your system is agentic (single agent with tool access), add [Agentic Controls](core/agentic.md) after step 3.

### "I'm building a multi-agent system"

Start with the foundation path above — the single-agent controls are the baseline. Then layer on MASO:

1. [MASO Overview](maso/) — architecture, control domains, OWASP mapping
2. [Tier 1 — Supervised](maso/implementation/tier-1-supervised.md) — start here (human approves all writes)
3. [Integration Guide](maso/integration/integration-guide.md) — LangGraph, AutoGen, CrewAI, AWS Bedrock patterns
4. [Red Team Playbook](maso/red-team/red-team-playbook.md) — 13 adversarial test scenarios

Graduate to [Tier 2](maso/implementation/tier-2-managed.md) and [Tier 3](maso/implementation/tier-3-autonomous.md) as your controls mature. The tier guides include graduation criteria — don't skip tiers.

### "I'm an architect designing the pipeline"

Start with the challenges that affect your architecture:

| If you're deploying... | Read this first |
|---|---|
| Multimodal models | [Multimodal Breaks Guardrails](insights/multimodal-breaks-guardrails.md) |
| Reasoning models | [When AI Thinks Before It Answers](insights/when-ai-thinks.md) |
| Single agents with tools | [Agentic Controls](core/agentic.md) |
| Multi-agent orchestration | [MASO Overview](maso/) → [Integration Guide](maso/integration/integration-guide.md) |
| Streaming responses | [Can't Validate What Hasn't Finished](insights/you-cant-validate-unfinished.md) |
| RAG pipelines | [RAG Is Your Biggest Attack Surface](insights/rag-is-your-biggest-attack-surface.md) |

Then the data-layer controls: [RAG Security](extensions/technical/rag-security.md), [Supply Chain](extensions/technical/supply-chain.md), [Memory & Context](core/memory-and-context.md).

For infrastructure enforcement: [Infrastructure Controls](infrastructure/) — 80 controls across 11 domains with AWS, Azure, and Databricks patterns.

### "I run a SOC and need to operationalise AI monitoring"

1. [Behavioral Anomaly Detection](insights/behavioral-anomaly-detection.md) — what you're looking for and why traditional detection doesn't apply
2. [SOC Integration](extensions/technical/soc-integration.md) — alert taxonomy, SIEM rules, triage
3. [Anomaly Detection Ops](extensions/technical/anomaly-detection-ops.md) — baselining and detection engineering
4. [Cost & Latency](extensions/technical/cost-and-latency.md) — budget the evaluation layer

For multi-agent monitoring, the [MASO Observability domain](maso/controls/observability.md) covers decision chain audit, anomaly scoring, drift detection, and independent kill switch architecture. The [Incident Tracker](maso/threat-intelligence/incident-tracker.md) maps 10 real-world AI security incidents to specific controls.

### "I need regulatory alignment"

| Standard | Single-Agent Mapping | Multi-Agent Mapping |
|---|---|---|
| OWASP LLM Top 10 (2025) | [OWASP mapping](infrastructure/mappings/owasp-llm-top10.md) | [MASO OWASP coverage](maso/) |
| OWASP Agentic Top 10 (2026) | — | [MASO OWASP coverage](maso/) |
| ISO 42001 | [ISO 42001 mapping](infrastructure/mappings/iso42001-annex-a.md) | [MASO regulatory alignment](maso/) |
| NIST AI RMF | [NIST mapping](infrastructure/mappings/nist-ai-rmf.md) | [MASO regulatory alignment](maso/) |
| EU AI Act | [EU AI Act mapping](extensions/regulatory/) | [MASO regulatory alignment](maso/) |
| NIST SP 800-218A | [SP 800-218A mapping](infrastructure/mappings/nist-sp800-218a.md) | — |
| DORA | — | [MASO regulatory alignment](maso/) |

### "I need to test and red team AI controls"

**Single-agent:** [Threat Model Template](extensions/templates/threat-model-template.md) → [Testing Guidance](extensions/templates/testing-guidance.md). Design adversarial tests targeting guardrails, Judge, and human oversight. Results feed back into control tuning — testing is continuous, not a pre-deployment gate.

**Multi-agent:** [Red Team Playbook](maso/red-team/red-team-playbook.md) — 13 structured scenarios across three tiers, from basic inter-agent prompt injection (RT-01) to PACE transition under active attack (RT-12). Includes success criteria, detection latency targets, and escalation guidance.

---

## Document Index

### Entry Points

| Document | What It Is |
|---|---|
| [Root README](README.md) | Framework overview — the narrative arc from problem to solution |
| [Foundation](foundations/) | Single-model AI security — full reference |
| [MASO Framework](maso/) | Multi-agent security operations — full reference |
| [Quick Start](QUICK_START.md) | Zero to working controls in 30 minutes |
| [Cheat Sheet](CHEATSHEET.md) | Entire framework on one page |
| [Decision Poster](DECISION-POSTER.md) | Visual one-page reference — print it |
| [Fast Lane](FAST-LANE.md) | Pre-approved path for low-risk deployments |

### Core Documents

| Document | Purpose |
|---|---|
| [Risk Tiers](core/risk-tiers.md) | Classify your system |
| [Controls](core/controls.md) | Three-layer implementation |
| [Agentic](core/agentic.md) | Single-agent tool and autonomy controls |
| [PACE Resilience](PACE-RESILIENCE.md) | Fail postures and fallback paths |
| [Checklist](core/checklist.md) | Implementation tracker |
| [Emerging Controls](core/emerging-controls.md) | Multimodal, reasoning, streaming *(theoretical)* |
| [Implementation Guide](IMPLEMENTATION_GUIDE.md) | Tools, cloud provider docs, what to build |

### MASO Documents

| Document | Purpose |
|---|---|
| [MASO Overview](maso/) | Architecture, PACE integration, OWASP mapping |
| [Prompt, Goal & Epistemic Integrity](maso/controls/prompt-goal-and-epistemic-integrity.md) | 20 controls for instruction integrity and information quality |
| [Identity & Access](maso/controls/identity-and-access.md) | NHI, zero-trust, scoped permissions |
| [Data Protection](maso/controls/data-protection.md) | Cross-agent data fencing, DLP, RAG integrity |
| [Execution Control](maso/controls/execution-control.md) | Sandboxing, blast radius, Judge gate |
| [Observability](maso/controls/observability.md) | Audit, anomaly scoring, kill switch |
| [Supply Chain](maso/controls/supply-chain.md) | AIBOM, tool manifests, MCP vetting |
| [Risk Register](maso/controls/risk-register.md) | 30 emergent risks beyond OWASP |
| [Tier 1 — Supervised](maso/implementation/tier-1-supervised.md) | Human approves all writes |
| [Tier 2 — Managed](maso/implementation/tier-2-managed.md) | NHI, signed bus, Judge, continuous monitoring |
| [Tier 3 — Autonomous](maso/implementation/tier-3-autonomous.md) | Self-healing PACE, adversarial testing, kill switch |
| [Incident Tracker](maso/threat-intelligence/incident-tracker.md) | 10 real-world incidents mapped to controls |
| [Emerging Threats](maso/threat-intelligence/emerging-threats.md) | 8 forward-looking threat patterns |
| [Red Team Playbook](maso/red-team/red-team-playbook.md) | 13 adversarial test scenarios |
| [Integration Guide](maso/integration/integration-guide.md) | LangGraph, AutoGen, CrewAI, Bedrock patterns |
| [Worked Examples](maso/examples/worked-examples.md) | Finance, healthcare, critical infrastructure |

### Insights

| Article | Key Argument |
|---|---|
| [The First Control](insights/the-first-control.md) | Design thinking before technology selection |
| [Why Guardrails Aren't Enough](insights/why-guardrails-arent-enough.md) | You need detection for unknown-bad |
| [The Judge Detects. It Doesn't Decide.](insights/judge-detects-not-decides.md) | Async evaluation for nuanced decisions |
| [Infrastructure Beats Instructions](insights/infrastructure-beats-instructions.md) | You can't secure AI with prompts |
| [Risk Tier Is Use Case](insights/risk-tier-is-use-case.md) | Classification reflects deployment context |
| [Humans Remain Accountable](insights/humans-remain-accountable.md) | Humans own outcomes |
| [The Verification Gap](insights/the-verification-gap.md) | Can't confirm ground truth |
| [Behavioral Anomaly Detection](insights/behavioral-anomaly-detection.md) | Drift detection signals |
| [Multimodal Breaks Guardrails](insights/multimodal-breaks-guardrails.md) | New attack surfaces |
| [When AI Thinks](insights/when-ai-thinks.md) | Reasoning-aware controls |
| [When Agents Talk to Agents](insights/when-agents-talk-to-agents.md) | Multi-agent accountability gaps |
| [The Memory Problem](insights/the-memory-problem.md) | Persistent memory risks |
| [Can't Validate Unfinished](insights/you-cant-validate-unfinished.md) | Streaming validation |
| [Open-Weight Models](insights/open-weight-models-shift-the-burden.md) | Self-hosted control burden |
| [When the Judge Can Be Fooled](core/when-the-judge-can-be-fooled.md) | Judge threat model |

### Extensions & Infrastructure

| Resource | Purpose |
|---|---|
| [Infrastructure Controls](infrastructure/) | 80 controls, 11 domains, platform patterns |
| [Regulatory Mapping](extensions/regulatory/) | ISO 42001, EU AI Act |
| [Technical Extensions](extensions/technical/) | Bypass prevention, metrics, SOC integration |
| [Templates](extensions/templates/) | Threat models, testing guidance, playbooks |
| [Worked Examples](extensions/examples/) | Per-tier implementation walkthroughs |

---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
