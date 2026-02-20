# Product Owners

**Product Managers, Business Owners, Delivery Leads — what controls cost, what they prevent, and how to ship AI without getting blocked by security.**

> *Part of [Stakeholder Views](README.md) · [AI Runtime Behaviour Security](../)*

---

## The Problem You Have

You want to ship AI features. Your security and risk teams want "controls" and "governance" that feel like they'll slow you down by months. Meanwhile, your competitors are shipping AI without all this overhead.

Here's what's actually happening:

1. **You're liable for what your AI does.** When your chatbot hallucinates a product spec and a customer acts on it, that's your product's problem. When it leaks PII, that's a data breach. When it processes an unauthorized payment, that's financial loss. "The AI did it" is not an answer.

2. **Controls aren't equally expensive.** A LOW-tier system (internal FAQ bot) needs minimal controls and can deploy in days. A CRITICAL-tier system (credit decisioning) needs substantial controls and takes longer. The framework scales — not everything is maximum security.

3. **Most delivery delays come from ambiguity, not controls.** Teams stall when they don't know what's required. A clear classification and a proportionate control list is faster than endless security reviews.

---

## What This Framework Gives You

### A fast path for low-risk AI

If your AI system meets **all four** of these criteria, it qualifies for the [Fast Lane](../FAST-LANE.md):

- Internal users only
- Read-only (no write access to external systems)
- No regulated data (PII, financial, health, legal)
- Human reviews output before acting on it

**Fast Lane = basic guardrails + self-certification. Deploy in days, not months.**

Most internal productivity tools — meeting summarisers, document search, code assistants — qualify. Don't put them through the same process as a customer-facing payment system.

### A clear answer to "what controls do I need?"

The framework maps controls to risk tier. No negotiation needed:

| Your System | Tier | Controls Required | Typical Delivery Impact |
|---|---|---|---|
| Internal FAQ bot | LOW | Basic guardrails | Days — minimal overhead |
| Internal document assistant | MEDIUM | Guardrails + sampled Judge | 1-2 weeks — add evaluation layer |
| Customer service chatbot | HIGH | Full three layers + PACE | 3-6 weeks — includes fail posture design |
| Credit decisioning / autonomous agent | CRITICAL | Full three layers, 100% Judge, tested PACE | 6-12 weeks — includes compliance sign-off |

### The business case for controls (not just the cost)

Controls aren't overhead — they're insurance with measurable ROI:

| Without Controls | With Controls |
|---|---|
| ~10% of transactions have some issue | ~0.01% of transactions have issues |
| Incident response is reactive, expensive | Incidents are rare, contained, pre-planned |
| Each incident risks reputational damage | Residual incidents are low-severity |
| Regulatory exposure is unquantified | Risk is quantified and within appetite |

The [Risk Assessment](../core/risk-assessment.md) has the full worked example: a customer-facing chatbot at 1M transactions/year drops from ~100,000 issues to ~10 per year with three-layer controls.

### A classification you own

The [Use Case Definition](../strategy/use-case-definition.md) provides ten questions that classify your AI system and determine exactly what controls are required. Complete it in 15 minutes. The classification is self-service — you answer the questions, the tier follows, and the platform auto-applies the corresponding controls. No security review is needed for Fast Lane. For higher tiers, the controls activate from the platform; you configure the use-case-specific settings on top.

You own this classification. If you misalign your answers with reality — understate the data sensitivity, overstate the human oversight, misjudge the decision authority — the residual risk falls on you. The framework makes the secure path the easy path. Choosing to deviate from it is your prerogative, but the consequences are yours too.

---

## Your Starting Path

| # | Document | Why You Need It |
|---|---|---|
| 1 | [Cheat Sheet](../CHEATSHEET.md) | The entire framework on one page — decide your tier in 2 minutes |
| 2 | [Fast Lane](../FAST-LANE.md) | Check if your system qualifies for accelerated deployment |
| 3 | [Risk Tiers](../core/risk-tiers.md) | If not Fast Lane, classify your system using the six dimensions |
| 4 | [Use Case Definition](../strategy/use-case-definition.md) | Ten questions that feed directly into security requirements |
| 5 | [Checklist](../core/checklist.md) | Track your implementation — know what's done and what's left |

**If you're planning AI strategy:** [From Strategy to Production](../strategy/) covers business alignment, data reality, human factors, and progression planning.

---

## What You Can Do Monday Morning

1. **Classify your AI system** using the [Cheat Sheet](../CHEATSHEET.md). Can it go through the Fast Lane? If so, do that. If not, know your tier.

2. **Complete the [Use Case Definition](../strategy/use-case-definition.md).** Ten questions, 15 minutes, and you have a clear scope, a risk tier, and a control specification. This is self-service — no security review queue to wait in.

3. **Budget for the evaluation layer** based on your tier. LOW tier = negligible. MEDIUM = modest (Judge on 10% sample). HIGH/CRITICAL = meaningful but proportionate. The [Cost & Latency](../extensions/technical/cost-and-latency.md) analysis has actual numbers.

4. **Frame controls as a feature, not overhead.** "Our chatbot has a 99.99% accuracy rate with three-layer verification" is a competitive advantage, not a burden. Customers trust AI systems that can demonstrate safety.

5. **Plan your progression.** If you're starting with a MEDIUM-tier system and want to evolve to HIGH (e.g., adding autonomous actions), the [Progression](../strategy/progression.md) guide maps the path. This typically takes 6-12 months of operational maturity per tier jump.

---

## Common Objections — With Answers

**"Our competitors ship AI without all this."**
Your competitors either have controls you can't see, or they're accumulating unquantified risk. The first major AI incident in your industry will change the conversation. Being ahead of that conversation is a strategic advantage. See [Risk Stories](../insights/risk-stories.md) for what happens when controls are absent.

**"We can add security later."**
Retrofitting controls is 3-5x more expensive than building them in. The architecture decisions (where the Judge sits, how the circuit breaker works, what the fail posture is) are easier at design time. The controls themselves are incremental — start with guardrails, add Judge, add oversight.

**"Security keeps moving the goalposts."**
The framework fixes the goalposts. Your tier determines your controls. If you're classified as MEDIUM, nobody can require CRITICAL-tier controls. If requirements change, the tier classification changes first, with documented justification.

**"The AI team says we don't need a Judge layer."**
Ask them what their hallucination rate is. Ask what happens when guardrails miss a prompt injection. The Judge isn't questioning the AI team's work — it's catching the failures that every AI system produces at some rate. It's the same principle as code review: not because the developer is bad, but because a second pair of eyes catches what the first misses.

---

*AI Runtime Behaviour Security, 2026 (Jonathan Gill).*
