# Control Selection Guide

How to select the right controls for your AI system based on risk tier and use case characteristics.

---

## Overview

Not every AI system needs every control. This guide helps you select appropriate controls based on:

1. **Risk tier** — CRITICAL, HIGH, MEDIUM, LOW
2. **Use case type** — Customer-facing, internal, agentic, batch
3. **Data sensitivity** — PII, financial, regulated, public
4. **Decision impact** — Consequential, advisory, informational

---

## Step 1: Determine Risk Tier

### Risk Tier Decision Tree

```
START
  │
  ├─ Does this system make or directly influence decisions that 
  │  significantly affect individuals' finances, employment, 
  │  health, or legal rights?
  │    │
  │    YES → CRITICAL
  │    │
  │    NO ↓
  │
  ├─ Does this system handle sensitive PII, financial data,
  │  or regulated information (credit, health, etc.)?
  │    │
  │    YES → Is it customer-facing?
  │           │
  │           YES → HIGH
  │           NO  → Does it process at scale (>1000 records/day)?
  │                   │
  │                   YES → HIGH
  │                   NO  → MEDIUM
  │    │
  │    NO ↓
  │
  ├─ Is this system customer-facing or externally visible?
  │    │
  │    YES → HIGH
  │    │
  │    NO ↓
  │
  ├─ Is this an internal tool with limited scope and users?
  │    │
  │    YES → MEDIUM (or LOW if truly experimental)
  │    │
  │    NO ↓
  │
  └─ LOW (sandbox, POC, limited experiment)
```

### Risk Tier Examples

| System | Tier | Rationale |
|--------|------|-----------|
| Credit decision support | CRITICAL | Directly affects lending decisions |
| Fraud detection AI | CRITICAL | False negatives allow fraud; false positives block customers |
| Trading signal generator | CRITICAL | Financial impact, regulatory scrutiny |
| Customer service chatbot | HIGH | Customer-facing, handles account queries |
| Document extraction (PII) | HIGH | Processes sensitive data at scale |
| Internal HR assistant | HIGH | Employment-related, PII |
| Meeting summariser | MEDIUM | Internal, limited sensitivity |
| Code assistant (internal) | MEDIUM | Internal productivity, no customer data |
| Marketing copy generator | MEDIUM | External-facing content, but reviewed |
| Sandbox experiments | LOW | No production data, no customer impact |
| Internal POC | LOW | Limited scope, controlled access |

---

## Step 2: Identify Use Case Characteristics

### Use Case Type Matrix

| Characteristic | Control Implications |
|----------------|---------------------|
| **Customer-facing** | Output guardrails critical; Judge sampling higher; HITL escalation paths |
| **Internal-only** | Can tolerate more latency; focus on data protection |
| **Agentic** | Full AG.1-AG.4 controls; circuit breakers mandatory |
| **Batch processing** | Can use heavier Judge evaluation; lower latency requirements |
| **Real-time** | Guardrail latency budget critical; async Judge only |
| **Decision support** | HITL mandatory for final decision; AI advisory only |
| **Fully automated** | Higher scrutiny; regulatory constraints (GDPR Art 22) |

### Data Sensitivity Matrix

| Data Type | Additional Controls |
|-----------|-------------------|
| **PII** | AI.5.3 Privacy, output PII filtering, data minimisation |
| **Financial** | AI.11 enhanced logging, AI.9 HITL for decisions |
| **Health** | HIPAA/regulatory compliance, AI.5.1 classification |
| **Credit** | SR 11-7 / SS1/23 model risk, AI.6.2 validation |
| **Authentication** | Never in AI context; AI.7.1 input filtering |
| **Regulated** | AI.13 vendor due diligence, AI.3.2 documentation |

---

## Step 3: Select Controls by Tier

### Control Selection Matrix

| Control | CRITICAL | HIGH | MEDIUM | LOW |
|---------|----------|------|--------|-----|
| **AI.1 Governance** | | | | |
| AI.1.1 Policy framework | ✅ Required | ✅ Required | ✅ Required | ⚠️ Recommended |
| AI.1.2 Governance structure | ✅ Required | ✅ Required | ⚠️ Recommended | ○ Optional |
| AI.1.3 Accountability | ✅ Required | ✅ Required | ✅ Required | ⚠️ Recommended |
| **AI.2 Risk Management** | | | | |
| AI.2.1 Risk classification | ✅ Required | ✅ Required | ✅ Required | ✅ Required |
| AI.2.2 Risk assessment | Full assessment | Full assessment | Streamlined | Self-assessment |
| AI.2.3 Ongoing monitoring | Continuous | Continuous | Periodic | Spot checks |
| **AI.3 Inventory & Documentation** | | | | |
| AI.3.1 System inventory | ✅ Required | ✅ Required | ✅ Required | ✅ Required |
| AI.3.2 System documentation | Comprehensive | Comprehensive | Standard | Basic |
| AI.3.3 Data lineage | Full | Full | Key flows | Basic |
| AI.3.4 Explainability | Full audit trail | Key factors | General approach | Basic |
| **AI.4 Development Security** | | | | |
| AI.4.1 Secure development | ✅ Required | ✅ Required | ✅ Required | ⚠️ Recommended |
| AI.4.2 Testing | Statistical (≥50 runs) | Statistical (≥20 runs) | Statistical (≥10 runs) | Basic (≥5 runs) |
| AI.4.3 Pre-deployment review | Independent + committee | Security team | Streamlined | Self-assessment |
| **AI.5 Data Governance** | | | | |
| AI.5.1 Data classification | ✅ Required | ✅ Required | ✅ Required | ⚠️ Recommended |
| AI.5.2 Data quality | Continuous validation | Continuous validation | Periodic validation | Basic checks |
| AI.5.3 Privacy protection | Full PIA, minimisation | PIA, minimisation | Standard handling | Basic |
| AI.5.4 RAG content integrity | Full validation | Full validation | Validation | Basic monitoring |
| **AI.6 Model Security** | | | | |
| AI.6.1 Model protection | ✅ Required | ✅ Required | ✅ Required | ⚠️ Recommended |
| AI.6.2 Model validation | Independent (SR 11-7) | Internal validation | Functional testing | Basic testing |
| AI.6.3 Model monitoring | Continuous + trends | Continuous | Periodic | Basic |
| AI.6.4 Capability assessment | On every change | On every change | On major changes | Initial only |
| AI.6.5 Baseline comparison | Daily | Weekly | Fortnightly | Monthly |
| **AI.7 Guardrails** | | | | |
| AI.7.1 Input guardrails | ✅ Required | ✅ Required | ✅ Required | ✅ Required |
| AI.7.2 Output guardrails | ✅ Required | ✅ Required | ✅ Required | ⚠️ Recommended |
| AI.7.3 Guardrail maintenance | Monthly adversarial test | Quarterly | Biannually | Annually |
| AI.7.4 Context isolation | Dedicated instances | Strict isolation | Session isolation | Standard |
| **AI.8 LLM-as-Judge** | | | | |
| AI.8.1 Judge evaluation | ✅ Required | ✅ Required | ✅ Required | ⚠️ Recommended |
| AI.8.2 Sampling strategy | 100% | 20-50% | 5-10% | 1-5% |
| AI.8.3 Finding management | 1-hour SLA (critical) | 24-hour SLA | 1-week SLA | Monthly batch |
| AI.8.4 Judge governance | ✅ Required | ✅ Required | ⚠️ Recommended | ○ Optional |
| AI.8.5 Confidence calibration | ✅ Required | ✅ Required | ⚠️ Recommended | ○ Optional |
| **AI.9 Human Oversight** | | | | |
| AI.9.1 HITL | All decisions | All escalations + sampling | Periodic + escalation | Spot checks |
| AI.9.2 Escalation procedures | ✅ Required | ✅ Required | ✅ Required | ⚠️ Recommended |
| AI.9.3 Human override | ✅ Required | ✅ Required | ✅ Required | ⚠️ Recommended |
| AI.9.4 Accountability | ✅ Required | ✅ Required | ✅ Required | ✅ Required |
| AI.9.5 HITL effectiveness | Weekly canaries | Monthly canaries | Quarterly canaries | Biannual |
| **AI.10 Agentic Controls** | | | | |
| AI.10.1-10.6 | Full AG.1-AG.4 | Full AG.1-AG.4 | AG.2 + AG.3 | Basic AG.2 |
| **AI.11 Logging & Monitoring** | | | | |
| AI.11.1 Logging | Full, tamper-evident, 7yr | Full, 3yr | Metadata + sampled, 1yr | Basic, 90 days |
| AI.11.2 Real-time monitoring | ✅ Required | ✅ Required | ✅ Required | ⚠️ Recommended |
| AI.11.3 Alerting | Immediate | Within 1 hour | Daily | Weekly |
| **AI.12 Incident Response** | | | | |
| AI.12.1 AI-specific playbooks | ✅ Required | ✅ Required | ⚠️ Recommended | ○ Optional |
| AI.12.2 Investigation capability | ✅ Required | ✅ Required | ✅ Required | ⚠️ Recommended |
| AI.12.3 Remediation | ✅ Required | ✅ Required | ✅ Required | ⚠️ Recommended |
| AI.12.4 Notification | Regulatory + customers | Regulatory | Internal | Basic |
| **AI.13 Supplier Management** | | | | |
| AI.13.1 Vendor assessment | Full + training data | Full | Standard | Basic |
| AI.13.2 Vendor agreements | Full AI terms | Full AI terms | Standard | Basic |
| AI.13.3 Model provenance | Full documentation | Full documentation | Standard | Basic |
| AI.13.4 Training data risk | Full assessment | Full assessment | Streamlined | Basic |
| **AI.14 Security Awareness** | | | | |
| AI.14.1 Training | All audiences + HITL bias | All audiences | Key personnel | Basic |
| **AI.15 Continuity** | | | | |
| AI.15.1 Continuity planning | ✅ Required | ✅ Required | ⚠️ Recommended | ○ Optional |
| AI.15.2 System resilience | Multi-region, failover | Failover | Graceful degradation | Basic |
| **AI.16 Intellectual Property** | | | | |
| AI.16.1 Model IP protection | ✅ Required | ✅ Required | ⚠️ Recommended | ○ Optional |
| AI.16.2 Third-party IP | ✅ Required | ✅ Required | ✅ Required | ⚠️ Recommended |

---

## Step 4: Apply Use Case Modifiers

### Agentic AI Modifier

If your system is **agentic** (takes actions, not just generates content), add:

| Control | All Agentic Systems |
|---------|---------------------|
| AG.1.1 Plan disclosure | ✅ Required |
| AG.1.2 Plan guardrails | ✅ Required |
| AG.1.3 Plan approval | Based on tier |
| AG.2.1 Action guardrails | ✅ Required |
| AG.2.2 Circuit breakers | ✅ Required |
| AG.2.3 Scope enforcement | ✅ Required |
| AG.2.4 Tool controls | ✅ Required |
| AG.2.5 Tool protocol security | ✅ Required |
| AG.3.1 Trajectory logging | ✅ Required |
| AG.3.2 Trajectory evaluation | Based on tier |
| AG.3.3 Agentic HITL | Based on tier |
| AG.4.1 Agent inventory | ✅ Required |
| AG.4.2 Orchestration controls | If multi-agent |
| AG.4.3 Trace correlation | ✅ Required |

### Customer-Facing Modifier

If your system is **customer-facing**, add:

| Enhancement | Rationale |
|-------------|-----------|
| Increase Judge sampling by 1 tier | Higher reputational risk |
| Output guardrails mandatory | Customer protection |
| Escalation SLAs tightened | Customer impact |
| HITL queue prioritisation | Customer experience |
| Incident notification to customers | Regulatory requirement |

### Regulated Data Modifier

If your system handles **regulated data** (credit, health, financial):

| Enhancement | Rationale |
|-------------|-----------|
| Full AI.6.2 validation (SR 11-7 style) | Regulatory requirement |
| AI.3.4 Explainability at CRITICAL level | Right to explanation |
| AI.9.1 HITL mandatory for decisions | GDPR Art 22, fair lending |
| AI.11.1 Logging at CRITICAL level | Audit trail |
| AI.12.4 Regulatory notification | Reporting requirements |

### Batch Processing Modifier

If your system operates in **batch mode** (not real-time):

| Adjustment | Rationale |
|------------|-----------|
| Guardrail latency budget relaxed | No real-time requirement |
| Judge can evaluate 100% even at lower tiers | Processing time available |
| HITL can review before results released | Batch allows pre-release review |
| Rollback easier | Can reprocess batch |

---

## Step 5: Document Control Selection

For each AI system, document:

| Item | Content |
|------|---------|
| **System name** | Unique identifier |
| **Risk tier** | CRITICAL / HIGH / MEDIUM / LOW |
| **Tier rationale** | Why this tier was selected |
| **Use case type** | Customer-facing, internal, agentic, batch |
| **Data sensitivity** | PII, financial, regulated, public |
| **Decision impact** | Consequential, advisory, informational |
| **Modifiers applied** | Which modifiers and why |
| **Controls selected** | Full list with evidence requirements |
| **Controls deferred** | Any controls not implemented and rationale |
| **Review schedule** | When to reassess tier and controls |

---

## Common Patterns

### Pattern 1: Customer Service Chatbot

**Typical profile:**
- Tier: HIGH
- Type: Customer-facing, real-time
- Data: Account data (PII, financial)
- Decision: Advisory (human makes final decision)

**Control selection:**

| Control Area | Selection |
|--------------|-----------|
| Guardrails | Full input + output, <50ms budget |
| Judge | 20-50% sampling, 24hr SLA |
| HITL | Escalation path, not every interaction |
| Logging | Full content, 3-year retention |
| Agentic | Not applicable (reactive, not agentic) |

### Pattern 2: Credit Decision Support

**Typical profile:**
- Tier: CRITICAL
- Type: Internal, decision support
- Data: Credit data, PII
- Decision: Consequential (affects lending)

**Control selection:**

| Control Area | Selection |
|--------------|-----------|
| Guardrails | Full input + output + grounding verification |
| Judge | 100% sampling, 2hr SLA for critical findings |
| HITL | Human decides all — AI is advisory only |
| Logging | Full, tamper-evident, 7-year retention |
| Validation | Independent validation per SR 11-7 |
| Explainability | Full audit trail, decision rationale |

### Pattern 3: Internal Document Assistant

**Typical profile:**
- Tier: MEDIUM
- Type: Internal, real-time
- Data: Internal documents (some confidential)
- Decision: Informational

**Control selection:**

| Control Area | Selection |
|--------------|-----------|
| Guardrails | Standard input + output |
| Judge | 5-10% sampling, weekly SLA |
| HITL | Periodic review, standard escalation |
| Logging | Metadata + sampled content, 1-year retention |
| Agentic | Not applicable |

### Pattern 4: Agentic Research Assistant

**Typical profile:**
- Tier: HIGH (or CRITICAL if external actions)
- Type: Internal, agentic
- Data: Various (depends on tools)
- Decision: Advisory with autonomous actions

**Control selection:**

| Control Area | Selection |
|--------------|-----------|
| Guardrails | Full input + output + action guardrails |
| Judge | 20-50% trajectory evaluation |
| HITL | Plan approval for high-risk actions |
| Circuit breakers | Step limits, time limits, cost limits |
| Scope enforcement | Tool allowlist, data scope, outcome boundaries |
| Logging | Full trajectory, 3-year retention |

### Pattern 5: Sandbox/POC

**Typical profile:**
- Tier: LOW
- Type: Internal, experimental
- Data: Synthetic or public only
- Decision: None (experimentation)

**Control selection:**

| Control Area | Selection |
|--------------|-----------|
| Guardrails | Basic input validation |
| Judge | Spot checks only |
| HITL | Not required |
| Logging | Basic metadata, 90-day retention |
| Isolation | Separate from production |

---

## Review and Reassessment

### Triggers for Reassessment

| Trigger | Action |
|---------|--------|
| Use case scope expands | Reassess tier |
| New data types added | Reassess data sensitivity |
| Customer-facing deployment | Likely tier increase |
| Model upgrade | Capability assessment |
| Regulatory change | Control alignment review |
| Incident occurs | Post-incident control review |
| Annual review | Full reassessment |

### Review Schedule

| Tier | Full Reassessment | Control Verification |
|------|-------------------|---------------------|
| CRITICAL | Quarterly | Monthly |
| HIGH | Biannually | Quarterly |
| MEDIUM | Annually | Biannually |
| LOW | Annually | Annually |

---

## Checklist: Control Selection Process

| Step | Complete |
|------|----------|
| 1. System identified and named | ☐ |
| 2. Risk tier determined using decision tree | ☐ |
| 3. Tier rationale documented | ☐ |
| 4. Use case type identified | ☐ |
| 5. Data sensitivity assessed | ☐ |
| 6. Decision impact classified | ☐ |
| 7. Base controls selected from matrix | ☐ |
| 8. Modifiers applied (agentic, customer-facing, regulated, batch) | ☐ |
| 9. Control selection documented | ☐ |
| 10. Deferred controls justified | ☐ |
| 11. Review schedule established | ☐ |
| 12. Sign-off obtained | ☐ |

---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
