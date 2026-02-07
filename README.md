# AI Governance Framework

Operational controls for enterprise AI systems.

![AI Security Reference Architecture](images/ai-security-tube-map.svg)

---

## Start Here

**[→ Core Framework](core/README.md)** — Everything you need to implement AI governance.

| Document | Purpose |
|----------|---------|
| [README](core/README.md) | Overview, quick start, key principles |
| [Risk Tiers](core/risk-tiers.md) | Classification and control selection |
| [Controls](core/controls.md) | Guardrails, Judge, Human Oversight |
| [Agentic](core/agentic.md) | Additional controls for agents |
| [Checklist](core/checklist.md) | Implementation tracking |

**Read these 5 documents. That's the framework.**

---

## Extensions

Reference material for deep dives and specific needs.

| Folder | Contents |
|--------|----------|
| [extensions/regulatory/](extensions/regulatory/) | ISO 42001, EU AI Act, banking guidance |
| [extensions/technical/](extensions/technical/) | Bypass prevention, infrastructure, metrics |
| [extensions/templates/](extensions/templates/) | Incident playbooks, assessments |
| [extensions/examples/](extensions/examples/) | Worked examples by use case |
| [images/](images/) | Architecture diagrams (SVG) |

---

## Scope

**In scope:** Custom LLM apps, AI decision support, document processing, agentic systems

**Out of scope:** 
- Vendor AI products (Copilot, Duet, etc.)
- Model training and development
- Data preparation and labelling
- Pre-deployment validation

This framework is **operationally focused** — deployment through incident response.

---

## The Core Idea

| Layer | Function | Timing |
|-------|----------|--------|
| **Guardrails** | Block known-bad inputs/outputs | Real-time |
| **LLM-as-Judge** | Detect issues, surface findings | Async |
| **Human Oversight** | Decide, act, remain accountable | As needed |

**Guardrails prevent. The Judge detects. Humans decide.**

---

## Status

This is a **discussion draft**, not a finished standard.

- Some pieces are proven, some are proposals
- Feedback welcome
- Will evolve as we learn

---

## Quick Links

| Need | Go To |
|------|-------|
| Classify a system | [Risk Tiers](core/risk-tiers.md) |
| Implement controls | [Controls](core/controls.md) |
| Deploy an agent | [Agentic](core/agentic.md) |
| Track implementation | [Checklist](core/checklist.md) |
| ISO 42001 alignment | [extensions/regulatory/](extensions/regulatory/) |
| Infrastructure controls | [extensions/technical/](extensions/technical/) |
| Incident playbooks | [extensions/templates/](extensions/templates/) |
| See examples | [extensions/examples/](extensions/examples/) |
