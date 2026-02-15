# Enterprise AI Security Framework — Single-Agent Controls

**Runtime behavioural security for single-model AI deployments. Guardrails, LLM-as-Judge, and human oversight — scaled to the risk.**

> *Part of the [Enterprise AI Security Framework](../)*
> Version 1.0 · February 2026 · Jonathan Gill

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## Architecture

![Single-Agent Security Architecture](/images/single-agent-architecture.svg)

Three layers, one principle: **you can't fully test a non-deterministic system before deployment, so you continuously verify behaviour in production.**

**Layer 1 — Guardrails** block known-bad inputs and outputs at machine speed (~10ms). Deterministic pattern matching: content filters, PII detection, topic restrictions, rate limits. Every request passes through. No exceptions.

**Layer 2 — LLM-as-Judge** catches unknown-bad through independent model evaluation (~500ms–5s). A separate LLM evaluates task agent outputs against policy, factual grounding, tone, and safety criteria. Catches what guardrails can't pattern-match.

**Layer 3 — Human Oversight** provides the accountability backstop. Scope scales with risk: low-risk systems get spot checks, high-risk systems get human approval before commit. Humans decide edge cases. Humans own outcomes.

**Circuit Breaker** stops all AI traffic and activates a non-AI fallback when any layer fails. Not a degradation — a full stop with a predetermined safe state.

This pattern already exists in production at major platforms: NVIDIA NeMo, AWS Bedrock, Azure AI, LangChain, Guardrails AI, and others. This framework provides the vendor-neutral implementation: risk classification, controls, fail postures, and tested fallback paths.

---

## Get Started

| If you want to... | Go here |
| --- | --- |
| Get the whole framework on one page | [Cheat Sheet](../CHEATSHEET.md) / [Decision Poster](../DECISION-POSTER.md) |
| Deploy low-risk AI fast | [Fast Lane](../FAST-LANE.md) |
| Understand the concepts in 30 minutes | [Quick Start](../QUICK_START.md) |
| Implement controls with working code | [Implementation Guide](../IMPLEMENTATION_GUIDE.md) |
| Classify a system by risk | [Risk Tiers](../core/risk-tiers.md) |
| Deploy an agentic AI system | [Agentic Controls](../core/agentic.md) |
| Understand what happens when controls fail | [PACE Resilience](../PACE-RESILIENCE.md) |
| Enforce controls at the infrastructure layer | [Infrastructure Controls](../infrastructure/) |
| Track your implementation | [Checklist](../core/checklist.md) |
| **Secure a multi-agent system** | **[MASO Framework](../maso/)** |

---

## Before You Build Controls

> **[The First Control: Choosing the Right Tool](../insights/the-first-control.md)**
>
> The most effective way to reduce AI risk is to not use AI where it doesn't belong. Before guardrails, judges, or human oversight — ask whether AI is the right tool for this problem.

If your deployment is internal, read-only, handles no regulated data, and has a human reviewing output — start with the [Fast Lane](../FAST-LANE.md). You may not need the rest.

---

## Risk-Scaled Controls

Controls scale to risk so low-risk AI moves fast and high-risk AI stays safe.

| Risk Tier | Controls Required | PACE Posture | Use Case Examples |
| --- | --- | --- | --- |
| **Low** | Fast Lane: minimal guardrails, self-certification | P only (fail-open with logging) | Internal chatbots, document summarisation, code assistance |
| **Medium** | Guardrails + Judge, periodic human review | P + A configured | Customer-facing content, recommendation engines, search |
| **High** | All three layers, human-in-the-loop for writes | P + A + C configured and tested | Financial advice, medical support, regulatory decisions |
| **Critical** | Full architecture, mandatory human approval | Full PACE cycle with tested E→P recovery | Autonomous actions on regulated data, safety-critical systems |

Classify your system: **[Risk Tiers](../core/risk-tiers.md)**

---

## PACE Resilience

Every control has a defined failure mode. The [PACE methodology](../PACE-RESILIENCE.md) ensures that when a control layer degrades — and it will — the system fails safely rather than silently.

**Primary:** All layers operational. Normal production.

**Alternate:** One layer degraded. Backup activated. Scope tightened. Example: Judge layer is down → guardrails remain active, all outputs queued for human review.

**Contingency:** Multiple layers degraded. AI operates in supervised-only mode. Human approves every action. Reduced capacity, high assurance.

**Emergency:** Confirmed compromise or cascading failure. Circuit breaker fires. AI traffic stopped. Non-AI fallback activated. Incident response engaged.

Even at the lowest risk tier, there's a fallback plan. At the highest, there's a structured degradation path from full autonomy to full stop.

---

## Core Documents

| Document | Purpose |
| --- | --- |
| [Cheat Sheet](../CHEATSHEET.md) | Entire framework on one page — classify, control, fail posture, test |
| [Decision Poster](../DECISION-POSTER.md) | Visual one-page reference |
| [Fast Lane](../FAST-LANE.md) | Pre-approved minimal controls for low-risk AI |
| [Risk Tiers](../core/risk-tiers.md) | Classify your system, determine control and resilience requirements |
| [Controls](../core/controls.md) | Guardrails, Judge, and Human Oversight implementation with per-layer fail postures |
| [Agentic](../core/agentic.md) | Controls for single autonomous AI agents including graceful degradation path |
| [PACE Resilience](../PACE-RESILIENCE.md) | What happens when controls fail |
| [Checklist](../core/checklist.md) | Track implementation and PACE verification progress |
| [Emerging Controls](../core/emerging-controls.md) | Multimodal, reasoning, and streaming considerations *(theoretical)* |

---

## Infrastructure Controls

This framework defines *what* to enforce. The [infrastructure](../infrastructure/) section defines *how* — 80 technical controls across 11 domains, with standards mappings and platform-specific patterns.

**Domains:** Identity & Access Management (8), Logging & Observability (10), Network & Segmentation (8), Data Protection (8), Secrets & Credentials (8), Supply Chain (8), Incident Response (8), Tool Access (6), Session & Scope (5), Delegation Chains (5), Sandbox Patterns (6).

**Standards mappings:** Every control maps to the three-layer model, ISO 42001 Annex A, NIST AI RMF, and OWASP LLM/Agentic Top 10.

**Platform patterns:** AWS Bedrock, Azure AI, and Databricks reference architectures.

---

## When You Need Multi-Agent

When AI agents collaborate, delegate tasks, and take autonomous actions across trust boundaries, the single-agent controls on this page are necessary but not sufficient. The **[MASO Framework](../maso/)** extends this architecture into multi-agent orchestration.

| What MASO adds | Why single-agent controls aren't enough |
| --- | --- |
| **Inter-agent message bus security** | Agents communicating directly create uncontrolled trust boundaries |
| **Non-Human Identity per agent** | Shared credentials between agents create lateral movement risk |
| **Epistemic integrity controls** | Hallucinations compound across agent chains; confidence inflates without evidence |
| **Transitive authority prevention** | Delegation creates implicit privilege escalation |
| **Kill switch architecture** | Multi-agent cascading failures require system-wide emergency stop |
| **Dual OWASP coverage** | Agentic Top 10 (2026) risks only exist when agents act autonomously |

| Document | Purpose |
| --- | --- |
| [MASO Overview](../maso/) | Architecture, PACE integration, OWASP dual mapping, 6 control domains |
| [Tier 1 — Supervised](../maso/implementation/tier-1-supervised.md) | Low autonomy: human approves all writes |
| [Tier 2 — Managed](../maso/implementation/tier-2-managed.md) | Medium autonomy: NHI, signed bus, LLM-as-Judge, continuous monitoring |
| [Tier 3 — Autonomous](../maso/implementation/tier-3-autonomous.md) | High autonomy: self-healing PACE, adversarial testing, isolated kill switch |
| [Red Team Playbook](../maso/red-team/red-team-playbook.md) | 13 adversarial test scenarios for multi-agent systems |
| [Integration Guide](../maso/integration/integration-guide.md) | LangGraph, AutoGen, CrewAI, AWS Bedrock implementation patterns |
| [Worked Examples](../maso/examples/worked-examples.md) | Financial services, healthcare, critical infrastructure |

---

## Extensions

| Folder | Contents |
| --- | --- |
| [Regulatory](../extensions/regulatory/) | ISO 42001 and EU AI Act mapping |
| [Technical](../extensions/technical/) | Bypass prevention, metrics |
| [Industry Solutions](../extensions/technical/current-solutions.md) | Guardrails, evaluators, and safety model reference |
| [Templates](../extensions/templates/) | Risk assessment templates, implementation plans |
| [Worked Examples](../extensions/examples/) | Per-tier implementation walkthroughs |

---

## Insights

**Foundational Arguments**

| Article | Key Argument |
| --- | --- |
| [The First Control: Choosing the Right Tool](../insights/the-first-control.md) | Design thinking before technology selection |
| [Why Your AI Guardrails Aren't Enough](../insights/why-guardrails-arent-enough.md) | Guardrails block known-bad; you need detection for unknown-bad |
| [The Judge Detects. It Doesn't Decide.](../insights/judge-detects-not-decides.md) | Async evaluation beats real-time blocking for nuanced decisions |
| [Infrastructure Beats Instructions](../insights/infrastructure-beats-instructions.md) | You can't secure AI systems with prompts alone |
| [Risk Tier Is Use Case, Not Technology](../insights/risk-tier-is-use-case.md) | Classification reflects deployment context, not model capability |
| [Humans Remain Accountable](../insights/humans-remain-accountable.md) | AI assists decisions; humans own outcomes |

**Emerging Challenges**

| Article | Key Argument |
| --- | --- |
| [The Verification Gap](../insights/the-verification-gap.md) | Current safety approaches can't confirm ground truth |
| [Behavioral Anomaly Detection](../insights/behavioral-anomaly-detection.md) | Aggregating signals to detect drift from expected behaviour |
| [Multimodal AI Breaks Your Text-Based Guardrails](../insights/multimodal-breaks-guardrails.md) | Images, audio, and video create new attack surfaces |
| [When AI Thinks Before It Answers](../insights/when-ai-thinks.md) | Reasoning models need reasoning-aware controls |
| [When Agents Talk to Agents](../insights/when-agents-talk-to-agents.md) | Multi-agent accountability gaps → see [MASO](../maso/) |
| [The Memory Problem](../insights/the-memory-problem.md) | Long context and persistent memory introduce novel risks |
| [You Can't Validate What Hasn't Finished](../insights/you-cant-validate-unfinished.md) | Real-time streaming challenges existing validation |
| [Open-Weight Models Shift the Burden](../insights/open-weight-models-shift-the-burden.md) | Self-hosted models inherit the provider's control responsibilities |
| [When the Judge Can Be Fooled](../core/when-the-judge-can-be-fooled.md) | The Judge layer needs its own threat model |

---

## Platforms Implementing This Pattern

This isn't a theoretical proposal. These platforms already implement variants of the three-layer pattern:

| Platform | Approach |
| --- | --- |
| [NVIDIA NeMo Guardrails](https://github.com/NVIDIA/NeMo-Guardrails) | Five rail types: input, dialog, retrieval, execution, output |
| [LangChain](https://docs.langchain.com/) | Middleware chains with human-in-the-loop |
| [Guardrails AI](https://www.guardrailsai.com/) | Open-source validator framework |
| [Galileo](https://www.rungalileo.io/) | Eval-to-guardrail lifecycle |
| [DeepEval](https://github.com/confident-ai/deepeval) | LLM-as-judge evaluation framework |
| AWS Bedrock Guardrails | Managed input/output filtering |
| Azure AI Content Safety | Content filtering and moderation |

---

## Standards Alignment

| Standard | Relevance | Mapping |
| --- | --- | --- |
| [OWASP LLM Top 10](https://owasp.org/www-project-top-10-for-large-language-model-applications/) | Security vulnerabilities in LLM applications | [OWASP mapping](../infrastructure/mappings/owasp-llm-top10.md) |
| [OWASP Agentic Top 10](https://genai.owasp.org/) | Risks specific to autonomous AI agents | [MASO mapping](../maso/) |
| [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework) | AI risk management framework | [NIST mapping](../infrastructure/mappings/nist-ai-rmf.md) |
| [ISO 42001](https://www.iso.org/standard/81230.html) | AI management system standard | [ISO 42001 mapping](../infrastructure/mappings/iso42001-annex-a.md) |
| [NIST SP 800-218A](https://csrc.nist.gov/pubs/sp/800/218/a/final) | Secure development for generative AI | [SP 800-218A mapping](../infrastructure/mappings/nist-sp800-218a.md) |
| [MITRE ATLAS](https://atlas.mitre.org/) | Adversarial threat landscape for AI | [MASO threat intelligence](../maso/threat-intelligence/incident-tracker.md) |
| [DORA](https://www.digital-operational-resilience-act.com/) | Digital operational resilience | [MASO regulatory alignment](../maso/) |

---

## Scope

**In scope:** Custom LLM applications, AI decision support, document processing, single-agent systems — from deployment through incident response.

**Out of scope:** Vendor AI products (use vendor controls), model training (see MLOps security guidance), and pre-deployment testing. This framework is about what happens in production.

**Pre-deployment complement:** For secure development practices covering data sourcing, training, fine-tuning, and model release, see [NIST SP 800-218A](https://csrc.nist.gov/pubs/sp/800/218/a/final). This framework begins where SP 800-218A ends.

**For multi-agent systems:** See [MASO](../maso/).

---

## Contributing

Feedback, corrections, and extensions welcome. See [CONTRIBUTING.md](../CONTRIBUTING.md).

---

*© 2026 Jonathan Gill. [MIT License](../LICENSE). Citation: [CITATION.md](../CITATION.md).*
