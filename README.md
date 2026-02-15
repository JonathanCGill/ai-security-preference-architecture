# Enterprise AI Security Framework

**Runtime behavioural security for AI systems — from single-model deployments to autonomous multi-agent orchestration.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## The Problem

Traditional software assurance relies on design-time testing. You write code, test it, prove it works, ship it.

AI breaks this model. AI systems are non-deterministic — the same input can produce different outputs. They exhibit emergent behaviour that can't be predicted through conventional test suites. And adversarial inputs will find edge cases no QA team imagined.

You can't fully test an AI system before deployment. The question is: **how do you know it's working correctly in production?**

---

## The Foundation

![Single-Agent Security Architecture](/images/single-agent-architecture.svg)

The industry is converging on an answer: **runtime behavioural monitoring.** Instead of proving correctness at design time, you continuously verify behaviour in production through three complementary layers.

| Layer | What It Does | When It Acts |
| --- | --- | --- |
| **Guardrails** | Prevent known-bad inputs and outputs | Real-time (~10ms) |
| **LLM-as-Judge** | Detect unknown-bad via independent LLM evaluation | Async (~500ms–5s) |
| **Human Oversight** | Decide edge cases, maintain accountability | As needed |
| **Circuit Breaker** | Stop AI traffic, activate non-AI fallback when controls fail | Immediate |

**Guardrails prevent. Judge detects. Humans decide. Circuit breakers contain.**

But security controls alone aren't enough — you also need to know what happens when they fail. The framework pairs every control layer with a **[PACE resilience architecture](PACE-RESILIENCE.md)** (Primary, Alternate, Contingency, Emergency). When a layer degrades, the system doesn't fail silently — it transitions to a predetermined safe state. Full stack operational → backup activated → supervised-only mode → full stop. Every tier has a plan. Every plan has been defined before the incident, not during it.

This pattern already exists in production at major platforms — NVIDIA NeMo, AWS Bedrock, Azure AI, LangChain, Guardrails AI, and others. The **[Foundation Framework](foundations/)** provides the complete implementation: risk classification, 80 infrastructure controls, PACE resilience methodology, regulatory mappings, and a fast lane for low-risk deployments.

For single-model AI systems, this is the answer. **→ [Start here](foundations/)**

---

## The Next Problem

Single-model controls assume one AI, one context window, one trust boundary. But the industry has already moved past this.

Multi-agent systems — where multiple LLMs from different providers collaborate, delegate tasks, and take autonomous actions — are the emerging architecture for complex AI workflows. Planning agents decompose problems. Specialist agents execute. Evaluation agents verify. Orchestrators coordinate.

This changes the threat model fundamentally:

- **Prompt injection propagates.** A poisoned document processed by one agent becomes instructions for another.
- **Delegation creates transitive authority.** If Agent A can delegate to Agent B, and Agent B has write access, then Agent A effectively has write access.
- **Consensus is not evidence.** Three agents agreeing doesn't mean three independent opinions — not when they share the same model, training data, and retrieval corpus.
- **Hallucinations compound.** Agent A hallucinates a claim. Agent B cites it as fact. By Agent C, it's been elaborated and presented with high confidence.
- **Failures look like success.** The most dangerous multi-agent failure modes produce outputs that are well-formatted, confident, and unanimously agreed — and wrong.

Single-model controls don't address any of this. You need a framework designed for multi-agent dynamics.

---

## MASO: Multi-Agent Security Operations

![MASO Tube Map](/images/maso-tube-map.svg)

The **[MASO Framework](maso/)** extends the foundation into multi-agent orchestration. Six control domains. 93 controls. 99 tests. Three implementation tiers. Full PACE resilience integration. Coverage of all 20 OWASP risks across both the LLM Top 10 (2025) and Agentic Top 10 (2026), plus 30 emergent risks that have no OWASP equivalent.

### Control Domains

| Domain | What It Secures |
| --- | --- |
| **[Prompt, Goal & Epistemic Integrity](maso/controls/prompt-goal-and-epistemic-integrity.md)** | Agent instructions, objectives, and information quality across chains — injection, goal hijack, groupthink, hallucination amplification, uncertainty stripping |
| **[Identity & Access](maso/controls/identity-and-access.md)** | Non-Human Identity per agent, zero-trust credentials, scoped permissions, no transitive authority |
| **[Data Protection](maso/controls/data-protection.md)** | Cross-agent data fencing, DLP on the message bus, RAG integrity, memory isolation |
| **[Execution Control](maso/controls/execution-control.md)** | Sandboxed execution, blast radius caps, circuit breakers, LLM-as-Judge gate, interaction timeouts |
| **[Observability](maso/controls/observability.md)** | Decision chain audit, anomaly scoring, drift detection, independent observability agent with kill switch |
| **[Supply Chain](maso/controls/supply-chain.md)** | AIBOM per agent, signed tool manifests, MCP server vetting, A2A trust chain validation |

### Implementation Tiers

| Tier | Autonomy | Key Controls |
| --- | --- | --- |
| **[Tier 1 — Supervised](maso/implementation/tier-1-supervised.md)** | Human approves all writes | Guardrails, tool scoping, audit logging, manual review |
| **[Tier 2 — Managed](maso/implementation/tier-2-managed.md)** | Auto-approve low-risk, escalate high-risk | NHI, signed bus, LLM-as-Judge, continuous anomaly scoring, PACE A/C configured |
| **[Tier 3 — Autonomous](maso/implementation/tier-3-autonomous.md)** | Minimal human intervention | Self-healing PACE, adversarial testing, independent observability agent, kill switch |

### What Makes MASO Different

**Epistemic security.** Most AI security frameworks focus on adversarial attacks — injection, exfiltration, jailbreaks. MASO also addresses the non-adversarial failures that emerge from multi-agent interaction itself: groupthink, correlated errors, synthetic corroboration, semantic drift, and uncertainty stripping. These aren't attacks. They're emergent properties of agents working together. They produce failures that look like success. We haven't found another framework that treats these as a formal control domain with test criteria and maturity indicators — though others may be working on similar ideas.

**PACE resilience for agent orchestration.** Every control has a defined failure mode. Every tier has a structured degradation path from full autonomy to full stop. The system doesn't just detect problems — it has a predetermined response at every phase: Primary → Alternate → Contingency → Emergency.

**Dual OWASP coverage.** Full mapping against both the OWASP Top 10 for LLM Applications and the OWASP Top 10 for Agentic Applications, with controls that address how individual LLM risks compound across agent chains. An additional [Emergent Risk Register](maso/controls/risk-register.md) captures 30 risks beyond the OWASP taxonomies.

**→ [Enter MASO](maso/)**

---

## Quick Navigation

| If you want to... | Go here |
| --- | --- |
| **Secure a multi-agent system** | **[MASO Framework](maso/)** |
| Understand MASO controls at a glance | [MASO Tube Map](/images/maso-tube-map.svg) |
| See real incidents mapped to controls | [Incident Tracker](maso/threat-intelligence/incident-tracker.md) |
| Run adversarial tests on your agents | [Red Team Playbook](maso/red-team/red-team-playbook.md) |
| Implement MASO in LangGraph, AutoGen, CrewAI, or Bedrock | [Integration Guide](maso/integration/integration-guide.md) |
| See MASO applied in finance, healthcare, or energy | [Worked Examples](maso/examples/worked-examples.md) |
| Deploy a single-model AI system | [Foundation Framework](foundations/) |
| Deploy low-risk AI fast | [Fast Lane](FAST-LANE.md) |
| Classify a system by risk | [Risk Tiers](core/risk-tiers.md) |
| Understand PACE resilience | [PACE Methodology](PACE-RESILIENCE.md) |
| Enforce controls at infrastructure level | [Infrastructure Controls](infrastructure/) |

---

## Standards Alignment

| Standard | Coverage |
| --- | --- |
| [OWASP LLM Top 10 (2025)](https://owasp.org/www-project-top-10-for-large-language-model-applications/) | Full mapping across foundation + MASO |
| [OWASP Agentic Top 10 (2026)](https://genai.owasp.org/) | Full mapping in MASO |
| [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework) | Govern, Map, Measure, Manage |
| [ISO 42001](https://www.iso.org/standard/81230.html) | AI management system alignment |
| [NIST SP 800-218A](https://csrc.nist.gov/pubs/sp/800/218/a/final) | Pre-deployment complement |
| [MITRE ATLAS](https://atlas.mitre.org/) | Agent-focused threat intelligence |
| [EU AI Act](https://artificialintelligenceact.eu/) | Art. 9, 14, 15 — risk management, oversight, robustness |
| [DORA](https://www.digital-operational-resilience-act.com/) | Digital operational resilience for financial services |

---

## Repository Structure

```
├── README.md                          # This document — start here
├── foundations/
│   └── README.md                      # Single-model AI security framework
├── maso/
│   ├── README.md                      # Multi-Agent Security Operations
│   ├── controls/                      # 6 domain specifications + risk register
│   ├── implementation/                # 3 tier guides (supervised, managed, autonomous)
│   ├── threat-intelligence/           # Incident tracker + emerging threats
│   ├── red-team/                      # Adversarial test playbook (13 scenarios)
│   ├── integration/                   # LangGraph, AutoGen, CrewAI, AWS Bedrock patterns
│   └── examples/                      # Financial services, healthcare, critical infrastructure
├── images/                            # All SVGs (tube map, architecture, OWASP coverage)
├── core/                              # Risk tiers, controls, checklists
├── infrastructure/                    # 80 technical controls, 11 domains
├── extensions/                        # Regulatory, templates, worked examples
└── insights/                          # Analysis articles and emerging challenges
```

---

## About the Author

**Jonathan Gill** is a cybersecurity practitioner with over 30 years in information technology and 20+ years in enterprise cybersecurity. His career spans UNIX system administration, building national-scale ISP infrastructure, enterprise security architecture at major financial institutions, and diplomatic IT service.

His current focus is AI security governance: designing control architectures that address the unique challenges of securing non-deterministic systems at enterprise scale, and translating complex technical risk into actionable guidance for engineering teams and executive leadership.

- GitHub: [@JonathanCGill](https://github.com/JonathanCGill)
- LinkedIn: [Jonathan Gill](https://www.linkedin.com/in/jonathancgill/)

---

## Disclaimer

This framework is provided as-is under the [MIT License](LICENSE). It reflects one practitioner's synthesis of industry patterns, regulatory requirements, and operational experience — not a certification, audit standard, or guarantee of security.

If you adopt any part of this framework, you are responsible for validating that it works in your environment, against your threat model, and within your regulatory obligations. No framework substitutes for professional security assessment of your specific deployment.

---

## How This Was Made

This framework was written with the help of AI — primarily Claude and ChatGPT. The architecture, control design, risk analysis, editorial decisions, and professional judgment are mine. The AI helped with drafting, structuring, research synthesis, and generating the volume of detailed content across 30+ documents that would have taken one person months to write manually.

This is a framework about securing AI systems, written with the assistance of AI systems. I think that's appropriate. The output has been reviewed, edited, and validated against real-world implementation experience. But I'm not going to pretend a human typed every word, because that would be dishonest — and honesty about AI use is kind of the point.

---

## License

[MIT](LICENSE) — Use it, adapt it, build on it.

## Citation

If you reference this framework in publications, presentations, or derivative work, see [CITATION.md](CITATION.md).
