# Enterprise AI Security Framework

**Secure AI from experiment to production. A fast lane for low-risk deployments. Guardrails, LLM-as-Judge, and human oversight for everything else — scaled to the risk.**

A practical, open-source framework for implementing behavioral security controls across generative and agentic AI systems — from internal tools to regulated decisions. Controls scale to risk so low-risk AI moves fast and high-risk AI stays safe.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

[![Enterprise AI Security Framework](images/ai-security-tube-map.svg)](images/ai-security-tube-map.svg)

---

## Why This Exists

Traditional software assurance relies on design-time testing. You write code, test it, prove it works, ship it.

**AI breaks this model.**

AI systems are non-deterministic — the same input can produce different outputs. They exhibit emergent behavior that can't be predicted through conventional test suites. And adversarial inputs will find edge cases no QA team imagined.

You can't fully test an AI system before deployment. The question is: **how do you know it's working correctly in production?**

The industry is converging on an answer: **runtime behavioral monitoring.** Instead of proving correctness at design time, you continuously verify behavior in production through three complementary layers.

| Layer | What It Does | When It Acts |
| --- | --- | --- |
| **Guardrails** | Prevent known-bad inputs and outputs | Real-time (~10ms) |
| **LLM-as-Judge** | Detect unknown-bad via independent LLM evaluation | Async (~500ms–5s) |
| **Human Oversight** | Decide edge cases, maintain accountability | As needed |
| **Circuit Breaker** | Stop AI traffic, activate non-AI fallback when controls fail | Immediate |

**Guardrails prevent. Judge detects. Humans decide. Circuit breakers contain.**

This pattern already exists in production at major platforms (NVIDIA NeMo, AWS Bedrock, Azure AI, LangChain, Guardrails AI, and others). What's been missing is a clear, vendor-neutral explanation of *why* it's necessary and *how* to implement it proportionate to risk.

That's what this framework provides. The [Infrastructure Controls](infrastructure/) section provides 80 technical controls that make the pattern enforceable at the infrastructure layer.

**But controls that slow adoption aren't controls — they're obstacles.** This framework scales controls to risk. Low-risk internal AI tools get a [Fast Lane](FAST-LANE.md): minimal controls, self-certification, deploy in days. High-risk regulated systems get the full architecture with defined fail postures and tested fallback paths. The goal is to make security the enabler, not the bottleneck.

Every control in this framework has a defined failure mode. The [PACE resilience methodology](PACE-RESILIENCE.md) (Primary, Alternate, Contingency, Emergency) ensures that when a control layer degrades — and it will — the system fails safely rather than silently. Even at the lowest risk tier, there's a fallback plan. At the highest, there's a structured degradation path from full autonomy to full stop.

---

## Get Started

| If you want to... | Go here |
| --- | --- |
| Get the whole framework on one page | [Cheat Sheet](CHEATSHEET.md) / [Decision Poster](decision-poster.html) |
| Deploy low-risk AI fast | [Fast Lane](FAST-LANE.md) |
| Understand the concepts in 30 minutes | [Quick Start](QUICK_START.md) |
| Implement controls with working code | [Implementation Guide](IMPLEMENTATION_GUIDE.md) |
| Classify a system by risk | [Risk Tiers](core/risk-tiers.md) |
| Deploy an agentic AI system | [Agentic Controls](core/agentic.md) |
| Understand what happens when controls fail | [PACE Resilience](PACE-RESILIENCE.md) |
| Enforce controls at the infrastructure layer | [Infrastructure Controls](infrastructure/) |
| Track your implementation | [Checklist](core/checklist.md) |

---

## Before You Build Controls

> **[The First Control: Choosing the Right Tool](insights/the-first-control.md)**
>
> The most effective way to reduce AI risk is to not use AI where it doesn't belong. Before guardrails, judges, or human oversight — ask whether AI is the right tool for this problem. Design thinking should precede technology selection.

Everything in this framework assumes you've already answered "yes" to that question. If your deployment is internal, read-only, handles no regulated data, and has a human reviewing output — start with the [Fast Lane](FAST-LANE.md). You may not need the rest.

---

## Framework Structure

### Core

The essential documents for understanding and implementing the pattern.

| Document | Purpose |
| --- | --- |
| [Cheat Sheet](CHEATSHEET.md) | The entire framework on one page — classify, control, fail posture, test |
| [Decision Poster](decision-poster.html) | Visual one-page reference — open in browser, print it, stick it on a wall |
| [Fast Lane](FAST-LANE.md) | Pre-approved minimal controls for low-risk AI — deploy without a bespoke security assessment |
| [Risk Tiers](core/risk-tiers.md) | Classify your AI system, determine control and resilience requirements |
| [Controls](core/controls.md) | Guardrails, Judge, and Human Oversight implementation with per-layer fail postures |
| [Agentic](core/agentic.md) | Controls for autonomous AI agents including graceful degradation path |
| [PACE Resilience](PACE-RESILIENCE.md) | The resilience methodology — what happens when controls fail, from fail-open to full stop |
| [Checklist](core/checklist.md) | Track your implementation and PACE verification progress |
| [Emerging Controls](core/emerging-controls.md) | Multimodal, reasoning, and streaming considerations *(theoretical)* |

### Extensions

Reference material for specific needs: regulatory mapping, technical depth, templates, and worked examples.

| Folder | Contents |
| --- | --- |
| [Regulatory](extensions/regulatory/) | ISO 42001 and EU AI Act mapping |
| [Technical](extensions/technical/) | Bypass prevention, metrics — see also [Infrastructure Controls](infrastructure/) |
| [Industry Solutions](extensions/technical/current-solutions.md) | Guardrails, evaluators, and safety model reference |
| [Templates](extensions/templates/) | Incident playbooks, threat models, testing guidance |
| [Examples](extensions/examples/) | Worked examples by use case |

### Insights

Articles explaining the reasoning behind the pattern — suitable for standalone reading, sharing, or adaptation.

**Why This Pattern?**

| Article | Key Argument |
| --- | --- |
| [Risk Stories](insights/risk-stories.md) | Real incidents mapped to framework controls and PACE resilience — where the pattern helps, and where it has limits |
| [The First Control: Choosing the Right Tool](insights/the-first-control.md) | Design thinking before technology selection |
| [Why Your AI Guardrails Aren't Enough](insights/why-guardrails-arent-enough.md) | Guardrails block known-bad; you need detection for unknown-bad |
| [The Judge Detects. It Doesn't Decide.](insights/judge-detects-not-decides.md) | Async evaluation beats real-time blocking for nuanced decisions |
| [Infrastructure Beats Instructions](insights/infrastructure-beats-instructions.md) | You can't secure AI systems with prompts alone |
| [Risk Tier Is Use Case, Not Technology](insights/risk-tier-is-use-case.md) | Classification should reflect deployment context, not model capability |
| [Humans Remain Accountable](insights/humans-remain-accountable.md) | AI assists decisions; humans own outcomes |

**Emerging Challenges**

| Article | Key Argument |
| --- | --- |
| [The Verification Gap](insights/the-verification-gap.md) | Current safety approaches can't confirm ground truth |
| [Behavioral Anomaly Detection](insights/behavioral-anomaly-detection.md) | Aggregating signals to detect drift from expected behavior |
| [Multimodal AI Breaks Your Text-Based Guardrails](insights/multimodal-breaks-guardrails.md) | Images, audio, and video create new attack surfaces |
| [When AI Thinks Before It Answers](insights/when-ai-thinks.md) | Reasoning models need reasoning-aware controls |
| [When Agents Talk to Agents](insights/when-agents-talk-to-agents.md) | Multi-agent systems have accountability gaps |
| [The Memory Problem](insights/the-memory-problem.md) | Long context and persistent memory introduce novel risks |
| [You Can't Validate What Hasn't Finished](insights/you-cant-validate-unfinished.md) | Real-time streaming challenges existing validation approaches |
| [Open-Weight Models Shift the Burden](insights/open-weight-models-shift-the-burden.md) | Self-hosted models inherit the provider's control responsibilities |
| [When the Judge Can Be Fooled](insights/when-the-judge-can-be-fooled.md) | The Judge layer needs its own threat model |

### Infrastructure Controls

This framework defines *what* to enforce. The [infrastructure](infrastructure/) section defines *how* to enforce it at the infrastructure layer — 80 technical controls across 11 domains, with standards mappings and platform-specific patterns.

| Resource | Contents |
| --- | --- |
| [Infrastructure Controls](infrastructure/) | Technical infrastructure controls |

**Control domains:** Identity & Access Management (8), Logging & Observability (10), Network & Segmentation (8), Data Protection (8), Secrets & Credentials (8), Supply Chain (8), Incident Response (8), Tool Access (6), Session & Scope (5), Delegation Chains (5), Sandbox Patterns (6).

**Standards mappings:** Every control maps to the three-layer model, ISO 42001 Annex A, NIST AI RMF, and OWASP LLM/Agentic Top 10.

**Platform patterns:** AWS Bedrock, Azure AI, and Databricks reference architectures.

---

## Scope

**In scope:** Custom LLM applications, AI decision support, document processing, agentic systems — from deployment through incident response.

**Out of scope:** Vendor AI products (use vendor controls), model training (see MLOps security guidance), and pre-deployment testing. This framework is about what happens in production.

**Pre-deployment complement:** For secure development practices covering data sourcing, training, fine-tuning, and model release, see [NIST SP 800-218A](https://csrc.nist.gov/pubs/sp/800/218/a/final). This framework begins where SP 800-218A ends.

**Note on fine-tuning:** Organisations that fine-tune or customise models before deployment operate in both the development and deployment lifecycle phases. They are simultaneously model producers (partially) and deployers. These organisations should apply SP 800-218A practices to their fine-tuning pipeline and this framework's controls to their production deployment. Neither framework alone provides complete coverage for this hybrid role.

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

The framework maps to established standards and risk taxonomies:

| Standard | Relevance | Infrastructure Mapping |
| --- | --- | --- |
| [OWASP LLM Top 10](https://owasp.org/www-project-top-10-for-large-language-model-applications/) | Security vulnerabilities in LLM applications | [OWASP mapping](infrastructure/mappings/owasp-llm-top10.md) |
| [OWASP Top 10 for Agentic Applications](https://genai.owasp.org/) | Risks specific to autonomous AI agents | [OWASP mapping](infrastructure/mappings/owasp-llm-top10.md) |
| [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework) | AI risk management framework | [NIST mapping](infrastructure/mappings/nist-ai-rmf.md) |
| [ISO 42001](https://www.iso.org/standard/81230.html) | AI management system standard | [ISO 42001 mapping](infrastructure/mappings/iso42001-annex-a.md) |
| [NIST SP 800-218A](https://csrc.nist.gov/pubs/sp/800/218/a/final) | Secure development practices for generative AI and dual-use foundation models | [SP 800-218A mapping](infrastructure/mappings/nist-sp800-218a.md) |

---

## Contributing

Feedback, corrections, and extensions are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

This is a living framework. It will evolve as the field matures.

---

## About the Author

**Jonathan Gill** is a cybersecurity practitioner with over 30 years in information technology and 20+ years in enterprise cybersecurity. His career spans UNIX system administration, building national-scale ISP infrastructure, enterprise security architecture at major financial institutions, and diplomatic IT service.

His current focus is AI security governance: designing control architectures that address the unique challenges of securing non-deterministic systems at enterprise scale, and translating complex technical risk into actionable guidance for engineering teams and executive leadership.

This framework represents a synthesis of practical implementation experience, industry patterns, and regulatory requirements — built to be useful, not theoretical.

- GitHub: [@JonathanCGill](https://github.com/JonathanCGill)
- LinkedIn: [Jonathan Gill](https://www.linkedin.com/in/jonathancgill/)

---

## Citation

If you reference this framework in publications, presentations, or derivative work, see [CITATION.md](CITATION.md).

---

## License

[MIT](LICENSE) — Use it, adapt it, build on it.
