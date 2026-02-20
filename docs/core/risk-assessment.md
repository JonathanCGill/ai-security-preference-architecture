# Quantitative Risk Assessment for AI Controls

**How the three-layer pattern reduces residual risk — with worked examples across all four tiers.**

> *Part of the [AI Runtime Behaviour Security](../)*
> Version 1.0 · February 2026 · Jonathan Gill

---

## NIST AI RMF Alignment

This document implements activities from all four functions of the [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework). If your organisation already uses NIST AI RMF, this risk assessment plugs directly into your existing process.

| NIST AI RMF Function | Subcategories | What This Document Provides |
|---|---|---|
| **GOVERN** | 1.3, 1.4, 1.5 | Risk management process structure; risk tolerance expressed as quantitative residual risk thresholds; recalibration schedule |
| **MAP** | 2.1, 3.1, 3.2 | AI system risk categorisation (four tiers); threat identification per scenario; lifecycle risk across transaction volumes |
| **MEASURE** | 1.1, 1.2, 2.1, 2.2, 2.3, 2.6 | Quantitative risk metrics; per-layer effectiveness measurement; residual risk calculation; recalibration methodology |
| **MANAGE** | 1.1, 1.3, 2.1, 2.2, 2.4 | Control selection proportionate to risk tier; compensating controls; PACE fail postures per tier; incident-driven recalculation |

The methodology below follows the NIST RMF lifecycle: **identify** threats (MAP), **measure** control effectiveness (MEASURE), calculate **residual risk** for governance decisions (GOVERN), and define **response actions** when risk exceeds appetite (MANAGE).

> For detailed infrastructure control mappings to all 51 NIST AI RMF subcategories, see [NIST AI RMF Mapping](../infrastructure/mappings/nist-ai-rmf.md).

---

## Why Quantify Control Effectiveness

Most AI security guidance says "add guardrails" or "implement human oversight" without answering the question that risk committees actually ask: **how much does each layer reduce the probability of harm, and what residual risk remains?**

This document provides a quantitative model for answering that question. It uses illustrative effectiveness rates — not empirically validated benchmarks — to demonstrate the methodology. Your actual rates will depend on your implementation quality, threat landscape, and operational maturity. The point is the approach, not the specific numbers.

> **Important:** The effectiveness percentages in this document are illustrative. They exist to demonstrate how layered controls compound to reduce residual risk. Your organisation should measure actual effectiveness through red teaming, Judge accuracy calibration (see [Judge Assurance](judge-assurance.md)), and incident data. Replace the illustrative rates with your measured rates as they become available.

---

## The Layered Control Model

Each layer in the three-layer pattern operates independently. When one layer misses a threat, the next layer has an independent opportunity to catch it. This is the same principle behind defence in depth in traditional security — except here we can model the compounding effect mathematically.

### Illustrative Effectiveness Rates

| Layer | Effectiveness | What This Means | Scope |
|---|---|---|---|
| **Guardrails** | ~90% | Catches 90% of issues that reach it — known patterns, policy violations, format errors | Every transaction, real-time |
| **LLM-as-Judge** | ~95% | Catches 95% of issues in the transactions it evaluates — semantic violations, subtle policy breaches, quality failures | Sampled or full coverage depending on tier |
| **Human Oversight** | ~98% | Catches 98% of issues surfaced to reviewers — edge cases, nuanced judgement calls, novel threats | Flagged transactions + sampling |

### How Layers Compound

When all three layers are active and operating independently on a transaction:

```
P(issue reaches customer) = P(miss guardrail) × P(miss judge) × P(miss human)
                          = (1 - 0.90) × (1 - 0.95) × (1 - 0.98)
                          = 0.10 × 0.05 × 0.02
                          = 0.0001
                          = 0.01%
```

**One in ten thousand.** Compare this to any single layer alone:

| Configuration | Residual Risk | Improvement Factor |
|---|---|---|
| No controls | 100% | — |
| Guardrails only | 10% | 10× |
| Guardrails + Judge | 0.5% | 200× |
| Guardrails + Judge + Human | 0.01% | 10,000× |

This is why the framework insists on layered controls. Each layer alone is insufficient. Together, they achieve orders-of-magnitude risk reduction.

### Critical Caveat: Independence Assumption

This model assumes layers fail independently — a threat that bypasses guardrails is not inherently more likely to bypass the Judge. This holds when:

- The Judge uses a **different model** than the task agent
- Human reviewers have **domain expertise** beyond what the AI provides
- Each layer uses **different detection methods** (pattern matching vs. semantic evaluation vs. human judgement)

If your Judge uses the same model as your task agent, or your human reviewers rubber-stamp AI outputs, the independence assumption breaks and your actual residual risk is higher than this model predicts.

---

## Worked Example: Customer Product Chatbot (HIGH Tier)

### System Description

A customer-facing AI chatbot that helps customers browse products, compare options, and complete purchases. The system has:

- **Product catalog access** (read) — prices, specifications, availability
- **Shopping cart management** (write) — add/remove items, apply promotions
- **Payment processing** (write via API) — charge customer payment methods
- **Order management** (write) — create and confirm orders
- **Customer account access** (read) — order history, saved addresses, payment methods

### Risk Classification

| Dimension | Assessment | Rationale |
|---|---|---|
| Decision authority | **High** — takes actions (processes payments, creates orders) |
| Reversibility | **Medium** — payments can be refunded but create operational cost |
| Data sensitivity | **High** — PII, payment data, purchase history |
| Audience | **Critical** — external customers |
| Scale | **High** — thousands of transactions per day |
| Regulatory | **Medium** — consumer protection, PCI-DSS adjacency |

**Assigned tier: HIGH** (payment processing pushes toward CRITICAL, but payment gateway provides independent validation — see compensating controls)

### Threat Scenarios

Five failure modes that matter for this system, with per-layer analysis across 1,000 transactions.

---

### Scenario 1: Prompt Injection — Price Manipulation

**Threat:** Attacker crafts input that causes the chatbot to apply unauthorized discounts, override pricing, or bypass payment validation.

**Inherent likelihood:** ~20 attempts per 1,000 transactions (2%). Most e-commerce chatbots see regular probing from both malicious actors and curious users.

| Layer | Detection Method | Effectiveness | Catches | Misses |
|---|---|---|---|---|
| **Guardrails** | Pattern matching for injection signatures, encoding detection, input normalisation | 90% | 18.0 | 2.0 |
| **Judge** | Semantic evaluation — "did the chatbot apply pricing outside catalog parameters?" | 95% | 1.9 | 0.1 |
| **Human** | Review flagged transactions, price anomaly alerts | 98% | 0.098 | 0.002 |

**Residual:** 0.002 successful price manipulations per 1,000 transactions = **1 in 500,000 transactions**

| Metric | Value |
|---|---|
| Inherent risk (no controls) | 20 per 1,000 |
| Residual risk (all layers) | 0.002 per 1,000 |
| Risk reduction factor | 10,000× |
| Annualised (1M transactions/year) | ~2 incidents |

---

### Scenario 2: Hallucinated Product Information

**Threat:** Chatbot fabricates product specifications, availability, pricing, or warranty terms that don't match the catalog. Customer makes a purchase decision based on wrong information.

**Inherent likelihood:** ~50 per 1,000 transactions (5%). Hallucination rates vary by model and RAG implementation quality, but product attribute errors are common when the model generates rather than retrieves.

| Layer | Detection Method | Effectiveness | Catches | Misses |
|---|---|---|---|---|
| **Guardrails** | Output validation against product catalog API — price match, spec match, availability check | 90% | 45.0 | 5.0 |
| **Judge** | Semantic grounding evaluation — "are all stated product attributes supported by the catalog source?" | 95% | 4.75 | 0.25 |
| **Human** | Review flagged hallucination alerts, spot-check random transactions | 98% | 0.245 | 0.005 |

**Residual:** 0.005 undetected hallucinations per 1,000 transactions = **1 in 200,000 transactions**

| Metric | Value |
|---|---|
| Inherent risk (no controls) | 50 per 1,000 |
| Residual risk (all layers) | 0.005 per 1,000 |
| Risk reduction factor | 10,000× |
| Annualised (1M transactions/year) | ~5 incidents |

---

### Scenario 3: PII Leakage

**Threat:** Chatbot includes another customer's personal data, order history, or payment details in a response. Could occur through context window contamination, shared session state, or RAG retrieval pulling the wrong customer's data.

**Inherent likelihood:** ~10 per 1,000 transactions (1%). Lower base rate than hallucination, but higher impact per incident.

| Layer | Detection Method | Effectiveness | Catches | Misses |
|---|---|---|---|---|
| **Guardrails** | Output PII scanner — regex + ML for names, addresses, card numbers, account IDs not belonging to the current session | 90% | 9.0 | 1.0 |
| **Judge** | Cross-reference evaluation — "does the response contain any data elements not attributable to the requesting customer?" | 95% | 0.95 | 0.05 |
| **Human** | Review PII alerts, periodic audit of cross-customer data access patterns | 98% | 0.049 | 0.001 |

**Residual:** 0.001 PII leakage incidents per 1,000 transactions = **1 in 1,000,000 transactions**

| Metric | Value |
|---|---|
| Inherent risk (no controls) | 10 per 1,000 |
| Residual risk (all layers) | 0.001 per 1,000 |
| Risk reduction factor | 10,000× |
| Annualised (1M transactions/year) | ~1 incident |

---

### Scenario 4: Unauthorized or Incorrect Payment

**Threat:** Chatbot processes a payment for the wrong amount, charges the wrong payment method, or initiates a transaction the customer didn't authorize.

**Inherent likelihood:** ~5 per 1,000 transactions (0.5%). Lower base rate because payment flows are typically more structured, but highest financial impact per incident.

| Layer | Detection Method | Effectiveness | Catches | Misses |
|---|---|---|---|---|
| **Guardrails** | Amount validation against cart total, payment method confirmation check, duplicate transaction detection | 90% | 4.5 | 0.5 |
| **Judge** | Transaction integrity evaluation — "does the payment amount, method, and authorization match the conversation flow?" | 95% | 0.475 | 0.025 |
| **Human** | Payment anomaly queue, high-value transaction review | 98% | 0.0245 | 0.0005 |

**Residual:** 0.0005 unauthorized payments per 1,000 transactions = **1 in 2,000,000 transactions**

| Metric | Value |
|---|---|
| Inherent risk (no controls) | 5 per 1,000 |
| Residual risk (all layers) | 0.0005 per 1,000 |
| Risk reduction factor | 10,000× |
| Annualised (1M transactions/year) | ~0.5 incidents |

---

### Scenario 5: Inappropriate or Harmful Response

**Threat:** Chatbot generates offensive content, makes inappropriate recommendations, provides dangerous advice (e.g., regarding product use), or behaves in a way that damages brand reputation.

**Inherent likelihood:** ~15 per 1,000 transactions (1.5%). Includes both adversarial prompting and edge cases where the model's training produces unexpected outputs in a commercial context.

| Layer | Detection Method | Effectiveness | Catches | Misses |
|---|---|---|---|---|
| **Guardrails** | Content policy filter, toxicity classifier, brand guideline checker | 90% | 13.5 | 1.5 |
| **Judge** | Tone and policy evaluation — "is this response appropriate for a customer-facing commercial interaction?" | 95% | 1.425 | 0.075 |
| **Human** | Escalation queue review, customer complaint correlation | 98% | 0.0735 | 0.0015 |

**Residual:** 0.0015 inappropriate responses per 1,000 transactions = **1 in ~667,000 transactions**

| Metric | Value |
|---|---|
| Inherent risk (no controls) | 15 per 1,000 |
| Residual risk (all layers) | 0.0015 per 1,000 |
| Risk reduction factor | 10,000× |
| Annualised (1M transactions/year) | ~1.5 incidents |

---

### Combined Risk Summary — Product Chatbot

| Threat | Inherent (per 1K) | Residual (per 1K) | Annualised (1M txn) | Severity |
|---|---|---|---|---|
| Prompt injection — price manipulation | 20 | 0.002 | ~2 | High |
| Hallucinated product info | 50 | 0.005 | ~5 | Medium |
| PII leakage | 10 | 0.001 | ~1 | Critical |
| Unauthorized payment | 5 | 0.0005 | ~0.5 | Critical |
| Inappropriate response | 15 | 0.0015 | ~1.5 | Medium |
| **Total** | **100** | **0.0100** | **~10** | |

**Interpretation:** Without controls, roughly 10% of transactions would have some form of issue. With all three layers active, the residual rate drops to approximately 0.001% — about 10 incidents per year at 1M transactions. Of those, the critical-severity incidents (PII, unauthorized payment) are expected at fewer than 2 per year.

---

## Compensating Controls

The three-layer AI pattern does not operate in isolation. Existing infrastructure provides independent controls that further reduce residual risk. These are not substitutes for AI-specific controls — they are additional layers in the overall defence.

### For the Product Chatbot

| Compensating Control | What It Catches | Independence From AI Layers |
|---|---|---|
| **Payment gateway validation** | Amount limits, card verification, 3D Secure, duplicate detection | Operates at payment infrastructure level — catches errors regardless of what the chatbot sends |
| **API input validation** | Malformed requests, out-of-range values, schema violations | Application layer — rejects structurally invalid API calls before they reach backend systems |
| **Fraud detection system** | Anomalous transaction patterns, velocity checks, device fingerprinting | Operates on transaction data, not chatbot outputs — independent signal |
| **Rate limiting (API gateway)** | Bulk exploitation, automated attacks, enumeration | Network/infrastructure level — limits blast radius regardless of individual transaction success |
| **Order confirmation workflow** | Customer verifies order details before final payment | Human-in-the-loop at the customer level — the customer themselves is a control |
| **Inventory management system** | Prevents fulfillment of out-of-stock items, catches quantity errors | Backend system of record — chatbot hallucination about availability is caught at fulfillment |
| **Refund/chargeback process** | Enables recovery from payment errors | Not a preventive control, but reduces financial impact of residual failures |

### Adjusted Residual Risk with Compensating Controls

Taking the two highest-severity scenarios and applying compensating controls:

**Unauthorized payment (0.0005 per 1,000 AI-layer residual):**

| Additional Layer | Catches | Remaining |
|---|---|---|
| Payment gateway validation (amount, card, 3DS) | ~95% of remaining | 0.000025 |
| Fraud detection system | ~80% of remaining | 0.000005 |
| Customer order confirmation | ~90% of remaining | 0.0000005 |

**Effective residual with compensating controls:** ~1 in 2 billion transactions

**PII leakage (0.001 per 1,000 AI-layer residual):**

| Additional Layer | Catches | Remaining |
|---|---|---|
| API response filtering (DLP at gateway) | ~85% of remaining | 0.00015 |
| Session isolation (infrastructure) | ~70% of remaining | 0.000045 |

**Effective residual with compensating controls:** ~1 in 22 million transactions

> **The point:** Compensating controls don't excuse weak AI-specific controls. But when a risk committee asks "what's the realistic probability of a customer being charged incorrectly?" the answer includes the full control stack, not just the AI layers. Present both the AI-layer residual and the compensated residual.

---

## Risk Tier Scenarios

The product chatbot is a HIGH-tier system. Here's how the same methodology applies across all four tiers, showing how control depth scales with risk.

### LOW Tier: Public FAQ Chatbot

**System:** Answers general product questions from public documentation. No customer data access, no transaction capability, no personalization.

**Control configuration:** Guardrails only. No Judge (or optional 1-5% sampling). No human-in-the-loop (exception-only).

| Threat | Inherent (per 1K) | Controls Applied | Residual (per 1K) |
|---|---|---|---|
| Hallucinated FAQ answer | 30 | Guardrails (90%): output grounding check against FAQ corpus | 3.0 |
| Inappropriate response | 10 | Guardrails (90%): content policy filter | 1.0 |
| Brand reputation harm | 5 | Guardrails (90%): tone checker | 0.5 |

**Residual: ~4.5 issues per 1,000 interactions**

**Why this is acceptable:** No financial impact, no data exposure, no irreversible actions. The FAQ bot gives wrong or awkward answers ~0.45% of the time. Users can verify against the website. The cost of these failures is low. Adding Judge and HITL would improve accuracy but the investment isn't proportionate to the risk.

**PACE posture:** Primary only (fail-open with logging). If guardrails fail, the chatbot continues to operate but all outputs are logged for batch review.

---

### MEDIUM Tier: Internal Document Assistant

**System:** Helps internal employees search and summarise company policy documents. Has access to internal knowledge base (read-only). Users are employees with domain knowledge who are expected to verify outputs.

**Control configuration:** Guardrails + Judge (5-10% sampling, batch daily). Human review on flags only.

| Threat | Inherent (per 1K) | Controls Applied | Residual (per 1K) |
|---|---|---|---|
| Hallucinated policy detail | 40 | Guardrails (90%) + Judge on 10% sample (95%) | 4.0 full coverage, ~3.8 effective* |
| PII in internal docs exposed incorrectly | 8 | Guardrails (90%) + Judge on 10% sample (95%) | 0.8 full coverage, ~0.76 effective* |
| Confidential doc outside need-to-know | 5 | Guardrails (90%): access control check + Judge | 0.5 full coverage, ~0.475 effective* |

*\* Judge at 10% sampling catches 95% of the 10% it evaluates. Effective additional catch rate: 0.10 × 0.95 = 9.5% of guardrail misses.*

**How sampling affects the math:**

```
With full Judge coverage:  P(miss) = 0.10 × 0.05 = 0.005 (0.5%)
With 10% Judge sampling:   P(miss) = 0.10 × (0.90 + 0.10 × 0.05) = 0.10 × 0.905 = 0.0905 (~9%)
```

**Residual: ~5 issues per 1,000 interactions (with sampling)**

**Why this is acceptable:** Internal users with domain expertise will catch most residual errors. The document assistant is an accelerator, not a decision-maker. Employees are expected to verify critical details against source documents. The Judge sampling catches systematic errors (drifting summaries, recurring hallucination patterns) even if it doesn't catch every individual instance.

**PACE posture:** P + A configured. If guardrails degrade, scope narrows to read-only retrieval (no summarisation). If Judge is unavailable, guardrail-only mode with increased human spot-checking.

---

### HIGH Tier: Customer Product Chatbot

See the [detailed worked example above](#worked-example-customer-product-chatbot-high-tier).

**Control configuration:** Guardrails + Judge (20-50% coverage, near real-time) + Human oversight (flagged transactions + sampling).

**Residual: ~0.01 issues per 1,000 transactions across all threat categories**

**PACE posture:** P + A + C configured and tested.
- **Alternate:** Judge down → guardrails remain active, all transactions flagged for human review queue, response latency accepted
- **Contingency:** Guardrails degraded → chatbot enters "assisted browse" mode (read-only, no transactions), human reviews every interaction
- **Emergency:** Multiple layers down → circuit breaker fires, chatbot replaced with static product pages + "contact us" fallback

---

### CRITICAL Tier: Credit Decisioning System

**System:** AI evaluates loan applications and produces credit decisions that are auto-executed for standard cases. Decisions affect customer finances directly. Regulatory obligations (fair lending, adverse action notices).

**Control configuration:** Full three-layer deployment. Judge at 100% coverage, real-time. Human review of all adverse decisions and all decisions above a threshold.

| Threat | Inherent (per 1K) | Controls Applied | Residual (per 1K) |
|---|---|---|---|
| Discriminatory decision | 15 | Guardrails (90%): protected-class input filtering + bias detection. Judge (95%): fairness evaluation per decision. Human (98%): all adverse decisions reviewed | 0.0015 |
| Hallucinated financial data | 20 | Guardrails (90%): data validation against bureau records. Judge (95%): source verification. Human (98%): sample review of all auto-approved | 0.002 |
| Incorrect risk score | 30 | Guardrails (90%): range and consistency checks. Judge (95%): independent risk recalculation on sample. Human (98%): all high-value reviewed | 0.003 |
| Regulatory violation | 10 | Guardrails (90%): compliance rule engine. Judge (95%): regulatory checklist evaluation. Human (98%): compliance officer review | 0.001 |
| Model drift — gradual accuracy degradation | 5 | Guardrails (90%): statistical drift detection. Judge (95%): decision distribution monitoring. Human (98%): monthly portfolio review | 0.0005 |

**Residual: ~0.008 issues per 1,000 decisions across all threat categories = 1 in 125,000 decisions**

**Compensating controls that further reduce risk:**
- **Regulatory model validation** — independent model risk management (OCC/Fed guidance)
- **Adverse action notice process** — customer can challenge decisions, creating a feedback loop
- **Portfolio-level monitoring** — statistical analysis catches systematic bias even if individual decisions pass
- **Audit trail requirements** — every decision is logged with full reasoning chain for regulatory examination

**PACE posture:** Full PACE cycle with tested E→P recovery.
- **Alternate:** Judge degraded → all decisions require human approval (no auto-execution)
- **Contingency:** Multiple layers degraded → system enters "manual underwriting" mode — AI provides data retrieval only, all decisions made by human underwriters
- **Emergency:** Circuit breaker → AI removed from decision path entirely, application queue held, existing commitments honoured through manual process

---

## Cross-Tier Summary

| Tier | Example | Layers Active | Judge Coverage | Inherent Risk (per 1K) | Residual Risk (per 1K) | Reduction Factor |
|---|---|---|---|---|---|---|
| **LOW** | Public FAQ | Guardrails | Optional 1-5% | ~45 | ~4.5 | 10× |
| **MEDIUM** | Internal docs | Guardrails + Judge (sampled) | 5-10% | ~53 | ~5.0 | 10× |
| **HIGH** | Product chatbot | All three | 20-50% | ~100 | ~0.01 | 10,000× |
| **CRITICAL** | Credit decisions | All three (full) | 100% | ~80 | ~0.008 | 10,000× |

**Key insight:** The jump from 10× to 10,000× risk reduction happens when the Judge moves from sampling to substantial coverage and Human Oversight moves from exception-only to systematic review. This is why the framework requires full three-layer deployment for HIGH and CRITICAL tiers.

---

## What These Numbers Do Not Tell You

**1. Severity is not uniform.** One PII leakage incident may matter more than fifty hallucinated FAQ answers. The residual risk numbers are per-incident counts, not impact-weighted. Weight your residual risk by impact severity when reporting to risk committees.

**2. Effectiveness rates change over time.** Adversaries adapt. Models drift. Guardrail bypass techniques evolve. The 90/95/98 rates are a snapshot. Schedule quarterly recalibration through:
- Red team exercises against guardrails
- Judge accuracy measurement against labelled datasets (see [Judge Assurance](judge-assurance.md))
- Human reviewer agreement studies

**3. Correlated failures break the model.** If a novel attack technique bypasses both guardrails AND the Judge (because both rely on similar detection approaches), the independence assumption fails and residual risk is higher than predicted. This is why the framework emphasises different models, different methods, and different perspectives across layers.

**4. The "unknown unknown" isn't modelled.** This analysis covers known threat categories. Novel failure modes — threats you haven't imagined — are not captured. The Judge layer's semantic evaluation and Human Oversight provide some coverage for novel threats, but the model cannot quantify what it cannot anticipate. This is the fundamental argument for defence in depth: you need layers precisely because you can't predict everything.

**5. Compensating controls have their own failure rates.** The payment gateway, fraud detection system, and API validation layer can all fail too. A complete risk assessment would model these as additional independent layers with their own effectiveness rates, producing a full probability tree. The simplified analysis above is sufficient for directional decision-making.

---

## Using This in Practice

### For Risk Committees

If your organisation uses NIST AI RMF, frame this assessment in those terms:

| What You're Presenting | NIST RMF Function | Language to Use |
|---|---|---|
| Threat scenarios and likelihood | **MAP** | "We've identified and categorised the AI-specific risks for this system" |
| Per-layer effectiveness data | **MEASURE** | "We've measured control effectiveness through red teaming and Judge calibration" |
| Residual risk calculation | **MEASURE** | "Residual risk after all control layers is X per Y transactions" |
| Risk appetite comparison | **GOVERN** | "This residual risk is within/outside our stated risk tolerance" |
| Compensating controls and PACE postures | **MANAGE** | "We have compensating controls and defined degradation paths when controls fail" |

Present two numbers:
1. **AI-layer residual risk** — what's left after guardrails, Judge, and human oversight
2. **Compensated residual risk** — what's left after existing infrastructure controls also apply

Frame the discussion around whether the compensated residual risk is within appetite, not whether it's zero. It will never be zero.

### For Engineering Teams

Use the per-scenario tables to:
- **Prioritise control implementation** — highest inherent likelihood × severity first
- **Justify Judge coverage levels** — show the math on sampling vs. full coverage
- **Identify where compensating controls reduce urgency** — payment gateway validation may mean you can deploy with guardrails-only initially while building out the Judge layer

### For Incident Response

When an incident occurs, update the effectiveness rates:
- If a prompt injection bypasses guardrails, your guardrail effectiveness for that attack class drops
- Recalculate residual risk with updated rates
- Determine whether the remaining layers still bring residual risk within appetite
- If not, implement additional controls or reduce system scope

### Recalibration Schedule

| Activity | Frequency | Updates |
|---|---|---|
| Red team guardrail testing | Quarterly | Guardrail effectiveness rate |
| Judge accuracy evaluation | Quarterly | Judge effectiveness rate |
| Human reviewer agreement study | Bi-annually | Human oversight effectiveness rate |
| Incident-driven recalculation | Per incident | Specific scenario rates |
| Full risk assessment refresh | Annually | All rates, all scenarios, all tiers |

---

## Template: Applying This to Your System

For each AI system, complete this assessment. NIST AI RMF function labels are included so you can slot each step into your existing risk management process.

**1. System description and tier classification** *(NIST RMF: MAP 1.1, MAP 2.1)*
- What does the system do?
- What data does it access? What actions can it take?
- What is the assigned risk tier and why?

**2. Threat scenario identification** *(NIST RMF: MAP 3.1, MAP 3.2)*
- List 3-7 realistic failure modes
- Estimate inherent likelihood per 1,000 transactions (use incident data, red team results, or informed estimates)
- Rate severity: Critical / High / Medium / Low

**3. Per-layer control analysis** *(NIST RMF: MEASURE 1.1, MEASURE 2.1, MEASURE 2.6)*
- For each scenario, describe what each layer detects and how
- Apply your measured or estimated effectiveness rates
- Calculate residual risk

**4. Compensating controls** *(NIST RMF: MANAGE 1.1, MANAGE 2.2)*
- List existing infrastructure controls that independently reduce risk
- Estimate their effectiveness against each scenario
- Calculate compensated residual risk

**5. Appetite comparison** *(NIST RMF: GOVERN 1.5, MANAGE 2.4)*
- Does the compensated residual risk fall within your risk appetite?
- If not, what additional controls or scope reductions are needed?

**6. Recalibration plan** *(NIST RMF: MEASURE 2.3, GOVERN 1.4)*
- When will you re-measure effectiveness rates?
- What triggers an unscheduled reassessment?

---

*AI Runtime Behaviour Security, 2026 (Jonathan Gill).*
