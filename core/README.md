# AI Runtime Behaviour Security — Core

Implementing behavioral controls for AI systems in production.

> *This is the implementation companion to the [Foundation overview](../foundations/). The Foundation explains the architecture and principles. This section contains the risk classification criteria, control definitions, checklists, and specialised controls you need to implement them.*

---

## Reading Order

Start with the essentials, then branch into specialised topics based on your deployment:

**Essential (read in order):**
1. [Risk Tiers](risk-tiers.md) — classify your system
2. [Risk Assessment](risk-assessment.md) — quantify control effectiveness and residual risk per tier
3. [Controls](controls.md) — implement the three-layer pattern
4. [Agentic](agentic.md) — add controls if your agent has tool access
5. [IAM Governance](iam-governance.md) — identity, lifecycle, delegation
6. [Judge Assurance](judge-assurance.md) — measure and calibrate the Judge
7. [Checklist](checklist.md) — track implementation progress

**Specialised (read based on your deployment type):**

| If you're deploying... | Read |
|---|---|
| Multimodal models (image, audio, video) | [Multimodal Controls](multimodal-controls.md) |
| Reasoning models (chain-of-thought) | [Reasoning Model Controls](reasoning-model-controls.md) |
| Streaming responses | [Streaming Controls](streaming-controls.md) |
| Persistent memory or long context | [Memory and Context](memory-and-context.md) |
| Multi-agent systems | [Multi-Agent Controls](multi-agent-controls.md) then [MASO](../maso/) |
| Open-weight / self-hosted models | [Open-Weight Models](../insights/open-weight-models-shift-the-burden.md) |

**PACE resilience (read after controls):**
- [Control Layer Resilience](pace-controls-section.md) — PACE for each control layer
- [PACE for Agentic AI](pace-agentic-section.md) — PACE for agentic deployments
- [PACE Checklist](pace-checklist-section.md) — verify your fail postures

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

**→ For detailed solution comparison, see [Current Solutions](../extensions/technical/current-solutions.md)**

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
| [Risk Assessment](risk-assessment.md) | Quantitative control effectiveness, residual risk analysis, worked examples per tier |
| [Controls](controls.md) | Guardrails, Judge, HITL implementation |
| [Agentic](agentic.md) | Additional controls for agents |
| [IAM Governance](iam-governance.md) | Identity governance, agent lifecycle, delegation, threats |
| [Checklist](checklist.md) | Implementation tracking |
| [Emerging Controls](emerging-controls.md) | Multimodal, reasoning, streaming overview |

### Specialized Controls

| Document | Purpose |
|----------|---------|
| [Judge Assurance](judge-assurance.md) | Judge accuracy measurement and calibration |
| [Multi-Agent Controls](multi-agent-controls.md) | Controls for multi-agent systems |
| [Multimodal Controls](multimodal-controls.md) | Controls for image, audio, and video AI |
| [Memory and Context](memory-and-context.md) | Long context and persistent memory controls |
| [Reasoning Model Controls](reasoning-model-controls.md) | Controls for chain-of-thought reasoning models |
| [Streaming Controls](streaming-controls.md) | Controls for real-time streaming outputs |

### Analysis & Insights

| Document | Purpose |
|----------|---------|
| [Oversight Readiness Problem](oversight-readiness-problem.md) | Why human-in-the-loop fails and how to fix it |
| [When the Judge Can Be Fooled](when-the-judge-can-be-fooled.md) | Judge adversarial robustness |
| [Open Weight Models Shift the Burden](open-weight-models-shift-the-burden.md) | Self-hosted model control implications |
| [Future Considerations](future-considerations.md) | Future framework scope |

### PACE Sections

| Document | Purpose |
|----------|---------|
| [PACE Controls Section](pace-controls-section.md) | PACE framework — controls |
| [PACE Agentic Section](pace-agentic-section.md) | PACE framework — agentic controls |
| [PACE Checklist Section](pace-checklist-section.md) | PACE framework — implementation checklist |

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

*AI Runtime Behaviour Security, 2026 (Jonathan Gill).*
