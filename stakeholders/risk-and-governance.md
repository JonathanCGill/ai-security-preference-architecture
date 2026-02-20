# Risk & Governance

**CROs, Risk Managers, GRC Teams — how to quantify AI risk, set appetite, and demonstrate control effectiveness.**

> *Part of [Stakeholder Views](README.md) · [AI Runtime Behaviour Security](../)*

---

## The Problem You Have

AI introduces a risk category your frameworks weren't built for: **non-deterministic system behaviour.**

Your existing risk models assume systems behave predictably. You test them, prove they work, assign a residual risk rating, and move on. AI doesn't work like that. The same system, with the same input, can produce different outputs at different times. An AI system that passed every test last quarter might hallucinate a medical dosage, leak customer data, or approve a transaction it shouldn't — tomorrow.

You need to answer three questions your board is asking:
1. **How much AI risk do we have?** (inventory and classification)
2. **Are our controls working?** (measurement, not assertion)
3. **What happens when they fail?** (resilience, not just prevention)

---

## What This Framework Gives You

### A classification scheme that maps to risk appetite

Four tiers with six scoring dimensions. Each dimension produces a measurable assessment, not a subjective rating:

| Dimension | What It Measures |
|---|---|
| Decision authority | Can the AI take action, or only advise? |
| Reversibility | Can outcomes be undone? At what cost? |
| Data sensitivity | PII, financial, health, legal, classified? |
| Audience | Internal employees or external customers? |
| Scale | Hundreds or millions of transactions? |
| Regulatory | Which regulatory obligations apply? |

The classification drives everything downstream — control requirements, Judge coverage levels, human oversight frequency, PACE resilience posture. **Risk appetite is expressed through tier boundaries**, not abstract statements.

### Quantified control effectiveness — with the maths

The [Risk Assessment](../core/risk-assessment.md) document provides a full quantitative methodology, aligned to **NIST AI RMF** (Govern, Map, Measure, Manage):

| NIST AI RMF Function | What the Framework Provides |
|---|---|
| **GOVERN** | Risk tolerance expressed as quantitative residual risk thresholds |
| **MAP** | Threat identification per scenario, per system, per tier |
| **MEASURE** | Per-layer effectiveness measurement with compounding calculations |
| **MANAGE** | Control selection proportionate to risk; PACE fail postures per tier |

**Example output for a HIGH-tier system** (customer-facing chatbot, 1M transactions/year):

| Threat | Inherent (per 1K) | Residual (per 1K) | Annual Incidents |
|---|---|---|---|
| Prompt injection | 20 | 0.002 | ~2 |
| Hallucinated information | 50 | 0.005 | ~5 |
| PII leakage | 10 | 0.001 | ~1 |
| Unauthorized action | 5 | 0.0005 | ~0.5 |

This is the language risk committees understand: inherent risk, control effectiveness, residual risk, annualised frequency. Not "we have guardrails."

### PACE resilience for operational risk

Every control layer has a defined degradation path:

![PACE Degradation Path](../images/pace-degradation.svg)

This maps directly to operational resilience frameworks (DORA, operational risk appetite statements, BCM). The AI system doesn't fail silently — it transitions through predetermined states, each with defined risk implications.

### Regulatory mapping you can hand to auditors

Pre-built crosswalks to the standards your GRC team already tracks:

| Standard | Mapping |
|---|---|
| NIST AI RMF 1.0 | [51 subcategories mapped](../infrastructure/mappings/nist-ai-rmf.md) |
| ISO 42001 | [Annex A alignment](../infrastructure/mappings/iso42001-annex-a.md) |
| EU AI Act | [Art. 9, 14, 15 crosswalk](../extensions/regulatory/eu-ai-act-crosswalk.md) |
| OWASP LLM Top 10 | [Full control mapping](../infrastructure/mappings/owasp-llm-top10.md) |
| NIST CSF 2.0 | [Function mapping](../infrastructure/mappings/csf-2.0.md) |

---

## Your Starting Path

| # | Document | Why You Need It |
|---|---|---|
| 1 | [Risk Tiers](../core/risk-tiers.md) | The classification scheme — six dimensions, four tiers, self-service with detection |
| 2 | [Risk Assessment](../core/risk-assessment.md) | Quantitative methodology — worked examples at every tier, NIST AI RMF aligned |
| 3 | [Controls](../core/controls.md) | What each control layer does, so you can evaluate whether they're implemented correctly |
| 4 | [PACE Resilience](../PACE-RESILIENCE.md) | Operational resilience — defined fail postures and degradation paths |
| 5 | [AI Governance Operating Model](../extensions/regulatory/ai-governance-operating-model.md) | Organisational structure for AI risk governance |

**For regulated industries:** Add [High-Risk Financial Services](../extensions/regulatory/high-risk-financial-services.md) and [EU AI Act Crosswalk](../extensions/regulatory/eu-ai-act-crosswalk.md).

---

## What You Can Do Monday Morning

1. **Inventory and classify** every AI system using the [Risk Tiers](../core/risk-tiers.md) six-dimension scoring. Most organisations don't know how many AI systems they're running or at what tier.

2. **Define risk appetite per tier.** Use the [Risk Assessment](../core/risk-assessment.md) template: "For HIGH-tier systems, we accept residual risk below X per 1,000 transactions." This converts abstract appetite into measurable thresholds.

3. **Require quantified control evidence.** Stop accepting "we have guardrails" as a risk treatment. Require the worked example format: inherent risk → per-layer effectiveness → residual risk → compensated residual.

4. **Add PACE posture to your risk register.** For each AI system, record which PACE state it's in (P/A/C/E) and what triggers transitions between states. This is your operational resilience evidence.

5. **Schedule quarterly recalibration.** Control effectiveness rates change. Require red team results, Judge accuracy measurements, and human reviewer agreement studies to update residual risk calculations. The recalibration schedule is in the [Risk Assessment](../core/risk-assessment.md) document.

---

## Common Objections — With Answers

**"We already have an AI risk framework."**
Does it quantify residual risk per control layer? Does it define what happens when controls fail? Does it map to NIST AI RMF at the subcategory level? This framework isn't competing with your existing GRC tooling — it provides the AI-specific risk methodology that plugs into it.

**"The AI team says the risk is low."**
Risk classification is the product owner's decision — they are accountable for the product's behaviour in production. The six-dimension scoring is designed to be self-service: the product owner answers the questions, the tier follows from the scores, and the controls auto-apply. Your role as a risk function is to ensure the scoring criteria are clear, the platform enforces the right controls per tier, and runtime monitoring detects misclassification. If a product owner underestimates their risk, the consequences are theirs — but the detection mechanisms should surface the misalignment quickly, not wait for a quarterly review.

**"We can't measure AI control effectiveness."**
You can. Guardrail effectiveness is measured through red team exercises. Judge accuracy is measured through labelled evaluation datasets. Human reviewer effectiveness is measured through agreement studies. The [Risk Assessment](../core/risk-assessment.md) methodology explains exactly how — and what to do with illustrative rates before you have measured ones.

**"This is too complex for our current maturity."**
Start with the [Cheat Sheet](../CHEATSHEET.md) and apply tier classification only. Even classifying your AI systems into four tiers, without implementing any new controls, gives you a risk inventory you didn't have before.

---

*AI Runtime Behaviour Security, 2026 (Jonathan Gill).*
