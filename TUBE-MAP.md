# Framework Tube Map

**The entire framework on one map. Foundation controls above the trust boundary. MASO multi-agent controls below. Cross-cutting lines show where operations, regulations, and testing connect both.**

*Click the image to view full-size. Right-click to save or print (landscape, A3 recommended).*

---

[![Enterprise AI Security Framework — Complete Tube Map](/images/ai-security-tube-map.svg)](/images/ai-security-tube-map.svg)

---

## How to Read the Map

The map uses London Underground visual conventions. **Stations** (white circles) are individual documents or concepts. **Interchanges** (larger circles with black borders) are places where multiple concerns intersect — Risk Tiers, Guardrails, Judge, Human Oversight, and the key MASO control domains are all interchanges.

The **trust boundary** (the pale blue river) separates the single-agent foundation above from multi-agent MASO controls below. Lines that cross the river — Operations, Pipeline & Data, Testing, Regulatory — show controls that span both architectures.

The **PACE resilience sidebar** on the right shows that resilience planning runs through everything — both foundation and MASO, from Primary through Emergency.

### Zones (left to right)

**Understand** — Why runtime behavioural security is necessary. The Insights line and Emerging Challenges line explain the problem.

**Implement** — The three-layer pattern (Guardrails → Judge → Human Oversight) and the six MASO control domains. This is where the controls live.

**Operate** — Checklists, regulatory compliance, operations, and cost management. What keeps the controls running.

### Key Interchanges

| Interchange | What Connects There |
|---|---|
| **Risk Tiers** | Getting Started, Runtime Controls, Testing — classification drives everything |
| **Guardrails** | Runtime Controls, Pipeline & Data, Testing feedback — the first control layer |
| **Judge** | Runtime Controls, Operations — connects to MASO Execution Control (Judge gate for agents) |
| **Judge Assurance** | Runtime Controls, Testing feedback, Emerging Challenges — the Judge needs its own assurance |
| **Human Oversight** | Runtime Controls, Operations — connects to MASO Observability |
| **When Agents Talk to Agents** | Emerging Challenges, MASO bridge — the conceptual crossing point from single-agent to multi-agent |
| **Checklist** | Runtime Controls, Regulatory — where compliance meets implementation tracking |

### The Lines

| Line | Colour | Zone | What It Covers |
|---|---|---|---|
| **Runtime Controls** | Red | Foundation | The three-layer pattern: Risk Tiers → Guardrails → Judge → Human Oversight → Circuit Breaker → Checklist |
| **Insights** | Blue | Foundation | Why the pattern exists — six foundational arguments |
| **Emerging Challenges** | Green | Foundation | Where the pattern meets its limits — seven frontier topics |
| **Getting Started** | Teal | Foundation | Quick Start → Implementation Guide → Fast Lane |
| **Testing & Assurance** | Pink | Both | Threat Modelling → Adversarial Testing → Red Team, with feedback loop back to Judge Assurance. Bridges to MASO Red Team. |
| **MASO Control Domains** | Orange | MASO | Six domains: Prompt/Goal/Epistemic → Identity & Access → Data Protection → Execution Control → Observability → Supply Chain |
| **Implementation Tiers** | Purple | MASO | Tier 1 (Supervised) → Tier 2 (Managed) → Tier 3 (Autonomous) |
| **Threat Intelligence** | Dark Gold | MASO | Incident Tracker → Emerging Threats → MASO Red Team Playbook |
| **Integration** | Brown | MASO | LangGraph → AutoGen/CrewAI → AWS Bedrock → Worked Examples |
| **Pipeline & Data** | Magenta | Cross-cutting | RAG Security → Supply Chain → Memory & Context — crosses the trust boundary |
| **Operations** | Yellow | Cross-cutting | Metrics → SOC Integration → Anomaly Detection → Cost & Latency — serves both architectures |
| **Regulatory** | Silver | Cross-cutting | Runs vertically through both zones: NIST, ISO 42001, EU AI Act, OWASP (LLM + Agentic), DORA, MITRE ATLAS |

---

## Related

| Resource | Purpose |
|---|---|
| [Framework Map](FRAMEWORK-MAP.md) | Reading paths by role — "I need to explain this to leadership", "I'm building a multi-agent system", etc. |
| [Cheat Sheet](CHEATSHEET.md) | Same content in text — scannable tables, copy-paste friendly |
| [Decision Poster](DECISION-POSTER.md) | Visual one-page reference for the foundation three-layer pattern |
| [Root README](README.md) | Full framework overview |
| [Foundation](foundations/) | Single-agent security framework |
| [MASO](maso/) | Multi-agent security operations |

---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
