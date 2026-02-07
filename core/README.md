# AI Security Blueprint — Core

Implementing behavioral controls for AI systems in production.

---

## The Fundamental Shift

Traditional software can be tested before deployment. AI cannot — not fully.

| Traditional Software | AI Systems |
|---------------------|------------|
| Deterministic outputs | Non-deterministic |
| Testable at design time | Emergent behavior |
| Known failure modes | Adversarial discovery |

**The shift:** From design-time assurance to runtime behavioral monitoring.

---

## The Pattern

The industry is converging on three layers:

| Layer | Function | Timing |
|-------|----------|--------|
| **Guardrails** | Block known-bad inputs/outputs | Real-time |
| **Judge** | Detect unknown-bad via LLM evaluation | Async |
| **Human Oversight** | Decide, act, remain accountable | As needed |

**Guardrails prevent. Judge detects. Humans decide.**

### Where This Pattern Exists

This isn't theoretical. Production implementations include:

| Platform | Implementation |
|----------|----------------|
| [NVIDIA NeMo Guardrails](https://github.com/NVIDIA/NeMo-Guardrails) | Input, dialog, retrieval, execution, output rails |
| [LangChain](https://docs.langchain.com/) | Middleware + human-in-the-loop |
| [Guardrails AI](https://www.guardrailsai.com/) | Open-source validator framework |
| [Galileo](https://www.rungalileo.io/) | Eval-to-guardrail lifecycle |
| [DeepEval](https://github.com/confident-ai/deepeval) | LLM-as-judge evaluation |
| AWS Bedrock Guardrails | Managed filtering |
| Azure AI Content Safety | Content moderation |

What's been missing: clear guidance on *why* this pattern is necessary and *how* to implement it proportionate to risk.

---

## Scope

**In:** Custom LLM apps, AI decision support, document processing, agentic systems  
**Out:** Vendor AI products, model training, data preparation

---

## Quick Start

### 1. Classify Your System

| Tier | Profile | Examples |
|------|---------|----------|
| **CRITICAL** | Direct decisions, customer/financial/safety impact | Credit decisions, fraud blocking |
| **HIGH** | Significant influence, sensitive data | Customer service with account access |
| **MEDIUM** | Moderate impact, human review expected | Internal Q&A, document drafting |
| **LOW** | Minimal impact, non-sensitive | Public FAQ, suggestions |

### 2. Select Controls

| Control | LOW | MEDIUM | HIGH | CRITICAL |
|---------|-----|--------|------|----------|
| Input guardrails | Basic | Standard | Enhanced | Maximum |
| Output guardrails | Basic | Standard | Enhanced | Maximum |
| Judge evaluation | — | Sampling | All | All + real-time |
| Human review | Exceptions | Sampling | Risk-based | All significant |

### 3. Implement in Order

1. **Logging** — Can't evaluate what you don't capture
2. **Basic guardrails** — Block obvious attacks  
3. **Judge in shadow mode** — Evaluate without action
4. **HITL queues** — Somewhere for findings to go
5. **Operationalise** — Act on findings, tune continuously

---

## Core Documents

| Document | Purpose |
|----------|---------|
| [Risk Tiers](risk-tiers.md) | Classification criteria, control mapping |
| [Controls](controls.md) | Guardrails, Judge, HITL implementation |
| [Agentic](agentic.md) | Additional controls for agents |
| [Checklist](checklist.md) | Implementation tracking |
| [Emerging Controls](emerging-controls.md) | Multimodal, reasoning, streaming *(theoretical)* |

### Architecture Overview

![Architecture Overview](../images/architecture-overview.svg)

---

## Extensions

| Folder | Contents |
|--------|----------|
| [regulatory/](../extensions/regulatory/) | ISO 42001, EU AI Act mapping |
| [technical/](../extensions/technical/) | Bypass prevention, infrastructure, metrics |
| [templates/](../extensions/templates/) | Playbooks, threat models |
| [examples/](../extensions/examples/) | Worked examples |

---

## Key Principles

1. **Match controls to risk** — Don't over-engineer LOW tier systems
2. **Guardrails are necessary but not sufficient** — They miss novel attacks and nuance
3. **Judge is assurance, not control** — It detects; humans decide what to do
4. **Infrastructure beats instructions** — Enforce technically, not via prompts
5. **Assume bypasses happen** — Design for detection, not just prevention
6. **Humans remain accountable** — AI assists; humans own outcomes

---

## The Key Insight

> **Design reviews prove intent. Behavioral monitoring proves reality.**

You can review prompts and guardrail configs. But you won't know what your AI actually does until it does it. That's why runtime monitoring isn't optional — it's primary assurance.
