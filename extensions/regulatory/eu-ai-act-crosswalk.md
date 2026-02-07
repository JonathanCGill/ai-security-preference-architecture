# EU AI Act Crosswalk

This document maps the AI Security Blueprint controls to EU AI Act requirements.

---

## Overview

The EU AI Act establishes requirements for AI systems based on risk classification. This crosswalk focuses on **high-risk AI systems** (Annex III), which face the most stringent requirements.

**Key principle:** The framework's control model—guardrails prevent, Judge detects, humans decide—aligns well with the EU AI Act's emphasis on human oversight and risk management.

---

## EU AI Act Risk Categories

| Category | Description | Framework Alignment |
|----------|-------------|---------------------|
| Unacceptable Risk | Prohibited AI practices | Out of scope (don't build these) |
| High Risk | Annex III systems (credit, employment, etc.) | CRITICAL tier |
| Limited Risk | Transparency obligations | HIGH/MEDIUM tier |
| Minimal Risk | No specific requirements | LOW tier |

---

## High-Risk AI System Requirements

### Article 9: Risk Management System

**Requirement:** Establish, implement, document, and maintain a risk management system.

| EU AI Act Requirement | Framework Control | Implementation |
|-----------------------|-------------------|----------------|
| Identify and analyse known/foreseeable risks | AI.2.1 Risk Classification | Risk assessment before deployment |
| Estimate and evaluate risks | AI.2.2 Risk Assessment | Document risk factors and scoring |
| Evaluate risks from intended use | AI.2.2 Risk Assessment | Use case analysis in classification |
| Adopt risk management measures | AI.7, AI.8, AI.9 | Guardrails, Judge, HITL |
| Test risk management measures | AI.4.2 Testing | Pre-deployment and ongoing testing |
| Ongoing risk management | AI.2.3 Ongoing Risk Monitoring | Judge monitoring, drift detection |

**How the framework satisfies this:**

1. **Risk identification:** Risk classification matrix covers key factors
2. **Risk mitigation:** Three-layer control model (guardrails, Judge, HITL)
3. **Testing:** Golden set testing, adversarial testing, bias testing
4. **Ongoing management:** Async Judge monitoring, HITL feedback loops

---

### Article 10: Data and Data Governance

**Requirement:** High-risk AI systems using training data shall be developed on the basis of training, validation, and testing datasets that meet quality criteria.

| EU AI Act Requirement | Framework Control | Implementation |
|-----------------------|-------------------|----------------|
| Relevant, representative, free of errors | AI.5.2 Data Quality | Training data validation |
| Appropriate statistical properties | AI.5.2 Data Quality | Data quality metrics |
| Account for specific settings | AI.5.2 Data Quality | Context-appropriate data |
| Examine for biases | AI.6.2 Model Validation | Bias testing |

**How the framework satisfies this:**

1. **Data governance:** AI.5 Data Governance control family
2. **Quality assurance:** Judge can evaluate for quality issues
3. **Bias detection:** Bias monitoring as part of Judge evaluation (CRITICAL tier)

---

### Article 11: Technical Documentation

**Requirement:** Technical documentation shall be drawn up before placing on the market and kept up to date.

| EU AI Act Requirement | Framework Control | Implementation |
|-----------------------|-------------------|----------------|
| General description | AI.3.2 System Documentation | System documentation package |
| Detailed description of elements | AI.3.2 System Documentation | Architecture, data flows |
| Monitoring, functioning, control | AI.11 Logging & Monitoring | Monitoring documentation |
| Risk management system description | AI.2 Risk Management | Risk assessment records |
| Description of changes | AI.3.2 System Documentation | Change documentation |

**How the framework satisfies this:**

1. **Documentation requirements scale by tier:** CRITICAL requires full SR 11-7-style documentation
2. **Inventory and documentation:** AI.3 control family
3. **Change management:** Part of AI.4 Development Security

---

### Article 12: Record-Keeping

**Requirement:** High-risk AI systems shall technically allow for automatic recording of events (logs).

| EU AI Act Requirement | Framework Control | Implementation |
|-----------------------|-------------------|----------------|
| Recording period of operation | AI.11.1 Comprehensive Logging | Full interaction logging |
| Reference database against which checked | AI.11.1 Comprehensive Logging | Context and source logging |
| Traceability of functioning | AI.11.1 Comprehensive Logging | Audit trail |

**How the framework satisfies this:**

1. **Comprehensive logging:** CRITICAL tier requires full content logging with 7-year retention
2. **Traceability:** Correlation IDs, timestamps, full context
3. **Judge evaluation logs:** Additional assurance documentation

---

### Article 13: Transparency and Provision of Information to Deployers

**Requirement:** High-risk AI systems shall be designed to ensure their operation is sufficiently transparent.

| EU AI Act Requirement | Framework Control | Implementation |
|-----------------------|-------------------|----------------|
| Understand output and use appropriately | AI.9.1 Human-in-the-Loop | HITL ensures understanding |
| Characteristics, capabilities, limitations | AI.3.2 System Documentation | Documentation package |
| Intended purpose | AI.3.1 AI System Inventory | Inventory records |
| Level of accuracy, robustness, cybersecurity | AI.6.2 Model Validation | Validation reports |

**How the framework satisfies this:**

1. **Transparency to users:** System prompts, UI design, documentation
2. **Transparency to oversight:** Judge reasoning, HITL context

---

### Article 14: Human Oversight

**Requirement:** High-risk AI systems shall be designed to allow for effective human oversight during use.

| EU AI Act Requirement | Framework Control | Implementation |
|-----------------------|-------------------|----------------|
| Properly understand capabilities and limitations | AI.9.1 HITL | Training, documentation |
| Remain aware of automation bias | AI.9.1 HITL | Reviewer independence |
| Correctly interpret output | AI.9.1 HITL | Context in review interface |
| Decide not to use in any situation | AI.9.3 Human Override | Override capability |
| Intervene or interrupt | AI.9.3 Human Override | Stop capability |

**How the framework satisfies this:**

This is where the framework's control model is specifically designed to comply:

1. **Guardrails prevent** — Real-time protection, humans can override/disable
2. **Judge detects** — Surfaces issues for human review, does NOT make decisions
3. **Humans decide** — HITL reviews findings, makes all consequential decisions

**Critical alignment:**
- The Judge is explicitly NOT a decision-maker
- Humans remain accountable for all outcomes
- Override capability is mandatory
- CRITICAL tier requires human decision on all consequential actions

**This avoids GDPR Article 22 concerns** about solely automated decision-making by ensuring meaningful human involvement.

---

### Article 15: Accuracy, Robustness, and Cybersecurity

**Requirement:** High-risk AI systems shall be designed to achieve appropriate levels of accuracy, robustness, and cybersecurity.

| EU AI Act Requirement | Framework Control | Implementation |
|-----------------------|-------------------|----------------|
| Appropriate levels of accuracy | AI.6.2 Model Validation | Accuracy metrics, validation |
| Resilient against errors, faults, inconsistencies | AI.6.3 Model Monitoring | Drift detection, anomaly monitoring |
| Resilient against attempts to alter use/performance | AI.7 Guardrails | Input validation, injection prevention |
| Appropriate cybersecurity measures | AI.4, AI.6.1 | Secure development, model protection |

**How the framework satisfies this:**

1. **Accuracy:** Validation, Judge quality monitoring
2. **Robustness:** Guardrails protect against malformed input
3. **Security:** Full control framework including injection prevention

---

## Control Mapping Summary

| EU AI Act Article | Primary Framework Controls |
|-------------------|---------------------------|
| Art. 9 Risk Management | AI.2 Risk Management |
| Art. 10 Data Governance | AI.5 Data Governance |
| Art. 11 Documentation | AI.3 Inventory & Documentation |
| Art. 12 Record-Keeping | AI.11 Logging & Monitoring |
| Art. 13 Transparency | AI.3, AI.9 |
| Art. 14 Human Oversight | AI.9 Human Oversight |
| Art. 15 Accuracy/Security | AI.6, AI.7 |

---

## Key Alignment Points

### Human Oversight (Article 14)

The framework's three-layer model directly supports Article 14:

```
┌────────────────────────────────────────────────────────────┐
│                  EU AI Act Article 14                       │
│                   Human Oversight                           │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  Requirement          Framework Implementation              │
│  ───────────          ────────────────────────              │
│                                                             │
│  Understand AI    →   Training, documentation, HITL         │
│                       context in review interface            │
│                                                             │
│  Interpret output →   Judge surfaces reasoning,             │
│                       HITL reviews with full context         │
│                                                             │
│  Override AI      →   Override capability mandatory,         │
│                       humans decide all CRITICAL actions     │
│                                                             │
│  Intervene/stop   →   Guardrails can be disabled,           │
│                       system can be halted                   │
│                                                             │
│  KEY: Judge does NOT decide. Humans decide.                 │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

### Risk Management (Article 9)

The framework provides comprehensive risk management:

```
┌────────────────────────────────────────────────────────────┐
│                  EU AI Act Article 9                        │
│               Risk Management System                        │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  Lifecycle Stage     Framework Controls                     │
│  ───────────────     ─────────────────                      │
│                                                             │
│  Design          →   Risk classification, control selection │
│                                                             │
│  Development     →   Secure development, testing            │
│                                                             │
│  Deployment      →   Guardrails (inline protection)         │
│                                                             │
│  Operation       →   Judge (ongoing monitoring)             │
│                      HITL (human oversight)                 │
│                      Logging (audit trail)                  │
│                                                             │
│  Improvement     →   Feedback loops (HITL → guardrails)     │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

---

## Evidence Package for Regulators

When demonstrating EU AI Act compliance, provide:

### Article 9 Evidence

| Evidence | Source |
|----------|--------|
| Risk assessment methodology | Documented methodology |
| Risk classification for system | Classification record |
| Control selection rationale | Mapping to risk tier |
| Test results | Validation reports, golden set results |
| Ongoing monitoring reports | Judge summaries, drift reports |

### Article 14 Evidence

| Evidence | Source |
|----------|--------|
| HITL process documentation | Operating procedures |
| HITL coverage by tier | Configuration records |
| Override capability | System documentation, logs |
| HITL decision records | Review logs with decisions |
| Training records | Staff training documentation |

### Article 12 Evidence

| Evidence | Source |
|----------|--------|
| Logging configuration | Technical documentation |
| Sample logs | Log exports |
| Retention compliance | Retention policy, verification |
| Tamper-evidence | Log integrity verification |

---

## GDPR Article 22 Alignment

**GDPR Article 22** gives data subjects the right not to be subject to solely automated decisions with legal or significant effects.

**How the framework satisfies this:**

| GDPR Requirement | Framework Implementation |
|------------------|--------------------------|
| Not solely automated | HITL for all CRITICAL decisions |
| Meaningful human involvement | Humans decide, not rubber-stamp |
| Right to human intervention | Override capability |
| Right to explanation | Judge reasoning + human decision trail |

**The Judge's role is critical here:**
- Judge does NOT make decisions
- Judge surfaces findings for humans
- Humans make the decision
- Therefore, decision is not "solely automated"

---

## Implementation Checklist

### For High-Risk AI Systems (CRITICAL Tier)

- [ ] Risk management system documented (Art. 9)
- [ ] Data governance documented (Art. 10)
- [ ] Technical documentation complete (Art. 11)
- [ ] Logging implemented with appropriate retention (Art. 12)
- [ ] Transparency requirements met (Art. 13)
- [ ] Human oversight implemented (Art. 14)
  - [ ] 100% HITL for consequential decisions
  - [ ] Override capability functional
  - [ ] Humans trained and accountable
- [ ] Accuracy and security validated (Art. 15)
- [ ] Guardrails deployed and tested
- [ ] Judge deployed with 100% sampling
- [ ] HITL processes operational
- [ ] Feedback loops active

---

## Limitations

This crosswalk provides guidance, not legal advice. Key limitations:

1. **Interpretation may vary:** Regulatory interpretation is still evolving
2. **Technical standards pending:** EU is developing harmonised standards
3. **Context matters:** Specific implementation depends on your use case
4. **Legal advice required:** Consult legal counsel for your situation

---

*This document is part of the AI Security Blueprint — Discussion Draft*
