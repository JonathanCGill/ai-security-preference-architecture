# AI Governance Framework — Core

Operational controls for AI systems in production.

---

## Scope

**In:** Custom LLM apps, AI decision support, document processing, agentic systems  
**Out:** Vendor AI products, model training, data preparation

---

## The Core Idea

| Layer | Function | Timing |
|-------|----------|--------|
| **Guardrails** | Block known-bad inputs/outputs | Real-time |
| **LLM-as-Judge** | Detect issues, surface findings | Async |
| **Human Oversight** | Decide, act, remain accountable | As needed |

**Guardrails prevent. The Judge detects. Humans decide.**

---

## Quick Start

### 1. Classify Your System

| Tier | Profile | Examples |
|------|---------|----------|
| **CRITICAL** | Direct decisions, customer/financial/safety impact | Credit decisions, fraud blocking |
| **HIGH** | Significant influence, sensitive data | Customer service with account access |
| **MEDIUM** | Moderate impact, human review expected | Internal Q&A, document drafting |
| **LOW** | Minimal impact, non-sensitive | Public FAQ, suggestions |

### 2. Apply Controls

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

### Architecture Overview

![Architecture Overview](../images/architecture-overview.svg)

---

## Extensions

| Folder | Contents |
|--------|----------|
| [regulatory/](../extensions/regulatory/) | ISO 42001, EU AI Act, banking |
| [technical/](../extensions/technical/) | Bypass prevention, infrastructure, metrics |
| [templates/](../extensions/templates/) | Playbooks, assessments |
| [examples/](../extensions/examples/) | Worked examples |

---

## Key Principles

1. **Match controls to risk** — Don't over-engineer LOW tier
2. **Guardrails are necessary but not sufficient** — They miss novel attacks
3. **The Judge is assurance, not control** — It detects; humans decide
4. **Infrastructure beats instructions** — Enforce technically, not via prompts
5. **Assume bypasses happen** — Design for detection, not just prevention
6. **Humans remain accountable** — AI assists; humans own outcomes
