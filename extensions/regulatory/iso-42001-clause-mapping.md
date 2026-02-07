# ISO 42001 Clause Mapping

## Technical Controls to ISO Requirements

This document provides detailed mapping of the framework's technical controls and platform components to ISO/IEC 42001:2023 requirements.

---

## Quick Reference Matrix

| ISO Clause | Requirement | Guardrails | Judge | HITL | Platform |
|------------|-------------|------------|-------|------|----------|
| 6.1.2 | AI risk assessment | Input to risk treatment | Ongoing risk detection | Risk review | Inventory |
| 6.1.3 | AI risk treatment | Treatment control | Treatment monitoring | Treatment verification | Implementation |
| 8.2 | AI risk assessment | | Assessment input | Assessment validation | Documentation |
| 8.3 | AI risk treatment | Primary control | Secondary control | Tertiary control | Control execution |
| 8.4 | AI system lifecycle | Design/deploy control | Operations control | Operations control | Full lifecycle |
| 9.1 | Monitoring | Effectiveness metrics | Accuracy metrics | SLA metrics | Dashboards |
| 9.2 | Internal audit | Audit evidence | Audit evidence | Audit evidence | Audit logs |
| 10.2 | Corrective action | Pattern updates | Criteria updates | Process updates | Tool updates |

---

## Clause 6: Planning

### 6.1.2 AI Risk Assessment

**Requirement:** The organisation shall define and apply an AI risk assessment process.

| Requirement Element | How Framework Addresses |
|---------------------|------------------------|
| Identify AI risks | Risk classification matrix; threat taxonomy (OWASP) |
| Analyse AI risks | Scoring methodology (impact × likelihood) |
| Evaluate AI risks | Tier thresholds (CRITICAL, HIGH, MEDIUM, LOW) |
| Consider AI-specific risks | Hallucination, bias, prompt injection, data leakage |

**Platform Support:**

| Platform | Capability |
|----------|------------|
| Bedrock | Guardrail threat detection informs risk assessment |
| Databricks | MLflow evaluation data informs risk assessment |
| Foundry | Sensitive Data Scanner identifies data risks |

**Evidence Generated:**
- Risk classification records
- Risk assessment documents
- Judge findings (ongoing risk indicators)

---

### 6.1.3 AI Risk Treatment

**Requirement:** The organisation shall define and apply an AI risk treatment process.

| Treatment | Guardrails | Judge | HITL |
|-----------|------------|-------|------|
| **Avoid** | Block prohibited patterns | Detect prohibited behaviour | Human stops system |
| **Mitigate** | Filter harmful content | Monitor residual risk | Review and remediate |
| **Transfer** | N/A | N/A | Escalate to specialist |
| **Accept** | Document accepted risk | Monitor accepted risk | Document acceptance |

**Control Selection by Tier:**

| Tier | Treatment Strategy | Controls Applied |
|------|-------------------|------------------|
| CRITICAL | Maximum mitigation | Full guardrails + 100% Judge + 100% HITL |
| HIGH | Strong mitigation | Full guardrails + 20-50% Judge + HITL escalation |
| MEDIUM | Moderate mitigation | Standard guardrails + 5-10% Judge + periodic HITL |
| LOW | Accept with monitoring | Basic guardrails + optional Judge + spot checks |

---

## Clause 8: Operation

### 8.2 AI Risk Assessment (Operational)

**Requirement:** The organisation shall implement the AI risk assessment process.

**Framework Implementation:**

```
┌─────────────────────────────────────────────────────────────┐
│              OPERATIONAL RISK ASSESSMENT                     │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  PRE-DEPLOYMENT              IN-OPERATION                   │
│  ┌─────────────────┐        ┌─────────────────┐            │
│  │ Risk            │        │ Judge           │            │
│  │ Classification  │        │ Monitoring      │            │
│  │                 │        │                 │            │
│  │ • Impact        │        │ • Quality       │            │
│  │ • Data          │        │ • Policy        │            │
│  │ • Autonomy      │        │ • Anomalies     │            │
│  │ • Regulatory    │        │ • Bias          │            │
│  └────────┬────────┘        └────────┬────────┘            │
│           │                          │                      │
│           ▼                          ▼                      │
│  ┌─────────────────┐        ┌─────────────────┐            │
│  │ Control         │        │ HITL Review     │            │
│  │ Selection       │        │ of Findings     │            │
│  └─────────────────┘        └─────────────────┘            │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

**Platform Support:**

| Platform | Pre-Deployment | In-Operation |
|----------|----------------|--------------|
| Bedrock | Risk assessment templates | Guardrail metrics, inference logs |
| Databricks | Model validation, bias testing | MLflow Judges, Lakehouse Monitoring |
| Foundry | Sensitive Data Scanner, AIP Evals | Continuous evaluation, audit logs |

---

### 8.3 AI Risk Treatment (Operational)

**Requirement:** The organisation shall implement the AI risk treatment plan.

**Control Layer Implementation:**

| Layer | ISO 8.3 Function | Implementation |
|-------|------------------|----------------|
| Guardrails | Primary treatment | Block/filter harmful content in real-time |
| Judge | Treatment monitoring | Detect residual risk, verify treatment effectiveness |
| HITL | Treatment verification | Human review confirms treatment adequacy |

**Treatment Verification Cycle:**

1. **Guardrails** block known-bad patterns (immediate)
2. **Judge** evaluates whether treatment is effective (async)
3. **HITL** reviews findings and confirms adequacy (review cycle)
4. **Feedback** updates guardrail patterns if gaps found (improvement)

**Evidence Generated:**
- Guardrail block logs (treatment execution)
- Judge evaluations (treatment monitoring)
- HITL decisions (treatment verification)
- Pattern updates (treatment improvement)

---

### 8.4 AI System Lifecycle

**Requirement:** The organisation shall establish processes for the AI system life cycle.

| Lifecycle Phase | Guardrails | Judge | HITL | Platform |
|-----------------|------------|-------|------|----------|
| **Requirements** | Define guardrail needs | Define evaluation criteria | Define HITL requirements | Document in inventory |
| **Design** | Select guardrail patterns | Design sampling strategy | Design review workflow | Configure platform |
| **Development** | Implement guardrails | Implement Judge prompts | Build review interface | Integrate controls |
| **Verification** | Test guardrails | Test Judge accuracy | Test HITL workflow | Validate integration |
| **Deployment** | Activate guardrails | Enable Judge sampling | Staff HITL queues | Deploy to production |
| **Operation** | Monitor effectiveness | Review findings | Process reviews | Monitor dashboards |
| **Monitoring** | Track false positives | Track accuracy | Track SLA compliance | Aggregate metrics |
| **Retirement** | Disable guardrails | Stop sampling | Archive records | Decommission |

**Platform Lifecycle Support:**

| Platform | Lifecycle Capabilities |
|----------|----------------------|
| Bedrock | Guardrail versioning; inference logging; CloudFormation |
| Databricks | MLflow model registry; Deployment Jobs; Unity Catalog |
| Foundry | Version control; release management; audit trails |

---

## Clause 9: Performance Evaluation

### 9.1 Monitoring, Measurement, Analysis, and Evaluation

**Requirement:** The organisation shall determine what needs to be monitored and measured.

**Monitoring Framework:**

| What | Metric | Source | Frequency |
|------|--------|--------|-----------|
| **Guardrail effectiveness** | Block rate, false positive rate | Guardrail logs | Real-time |
| **Judge accuracy** | Agreement with HITL | Calibration data | Weekly |
| **HITL performance** | SLA compliance, throughput | Queue metrics | Daily |
| **Risk treatment** | Residual risk indicators | Judge findings | Monthly |
| **Control coverage** | % systems with required controls | Inventory | Monthly |

**Platform Monitoring Capabilities:**

| Platform | Monitoring Capability | Dashboard |
|----------|----------------------|-----------|
| Bedrock | CloudWatch metrics; Guardrail metrics | CloudWatch dashboards |
| Databricks | Inference Tables; Lakehouse Monitoring | Unity Catalog dashboards |
| Foundry | Audit logs; inference history | Contour dashboards |

**Aggregated Governance Metrics:**

| Metric Category | Metrics | ISO Alignment |
|-----------------|---------|---------------|
| **Control Effectiveness** | Block rate, detection rate, false positives | 9.1 |
| **Risk Coverage** | % classified, % controlled, residual risk | 6.1, 8.3 |
| **Human Oversight** | HITL coverage, SLA compliance, override rate | 8.4 |
| **Improvement** | Pattern updates, criteria updates, findings resolved | 10.2 |

---

### 9.2 Internal Audit

**Requirement:** The organisation shall conduct internal audits at planned intervals.

**Audit Evidence from Technical Controls:**

| Control | Audit Evidence Generated |
|---------|-------------------------|
| Guardrails | Configuration records; block logs; pattern library; false positive analysis |
| Judge | Evaluation criteria; sampling configuration; calibration records; finding logs |
| HITL | Queue configuration; review records; decision documentation; SLA metrics |
| Logging | Log completeness; retention compliance; tamper-evidence verification |

**Audit Programme by Control:**

| Audit Area | Frequency | Evidence Required |
|------------|-----------|-------------------|
| Guardrail configuration | Annual | Pattern library, configuration, test results |
| Guardrail effectiveness | Quarterly | Metrics, false positive analysis |
| Judge accuracy | Quarterly | Calibration data, HITL feedback |
| HITL compliance | Quarterly | SLA metrics, decision samples |
| Control coverage | Annual | Inventory, control mapping |

**Platform Audit Support:**

| Platform | Audit Capabilities |
|----------|-------------------|
| Bedrock | Inference logs; guardrail trace data; CloudTrail |
| Databricks | Unity Catalog lineage; audit logs; inference tables |
| Foundry | Audit logs; workflow lineage; decision trails |

---

### 9.3 Management Review

**Requirement:** Top management shall review the organisation's AIMS at planned intervals.

**Management Review Inputs from Technical Controls:**

| Input | Source |
|-------|--------|
| Guardrail performance trends | Monthly metrics reports |
| Judge finding trends | Weekly summaries, monthly analysis |
| HITL escalation patterns | Escalation logs, trend analysis |
| Incident data | Incident reports, root cause analysis |
| Control effectiveness | Quarterly effectiveness review |

**Management Review Agenda Items:**

| Agenda Item | Technical Control Input |
|-------------|------------------------|
| Status of previous actions | Control remediation status |
| Changes affecting AIMS | New guardrail patterns, Judge criteria changes |
| Performance metrics | Guardrail, Judge, HITL metrics |
| Audit results | Control audit findings |
| Opportunities for improvement | HITL feedback, pattern gaps identified |

---

## Clause 10: Improvement

### 10.2 Nonconformity and Corrective Action

**Requirement:** When a nonconformity occurs, the organisation shall react, evaluate, implement corrective action, review effectiveness, and make changes to the AIMS.

**Nonconformity Sources:**

| Source | Examples |
|--------|----------|
| Guardrail failure | Harmful content not blocked; excessive false positives |
| Judge failure | Inaccurate evaluation; missed issues |
| HITL failure | SLA breach; inadequate review; wrong decision |
| Incident | Security breach; customer harm; regulatory finding |

**Corrective Action Workflow:**

```
┌─────────────────────────────────────────────────────────────┐
│              CORRECTIVE ACTION WORKFLOW                      │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  DETECT              ANALYSE              CORRECT            │
│  ┌─────────┐        ┌─────────┐         ┌─────────┐        │
│  │ Judge   │───────▶│ Root    │────────▶│ Update  │        │
│  │ Finding │        │ Cause   │         │Guardrail│        │
│  └─────────┘        └─────────┘         └─────────┘        │
│                                                │             │
│  ┌─────────┐        ┌─────────┐         ┌─────┴─────┐      │
│  │ HITL    │───────▶│ Pattern │────────▶│ Update    │      │
│  │ Review  │        │ Analysis│         │ Judge     │      │
│  └─────────┘        └─────────┘         └───────────┘      │
│                                                │             │
│  ┌─────────┐        ┌─────────┐         ┌─────┴─────┐      │
│  │ Audit   │───────▶│ Gap     │────────▶│ Update    │      │
│  │ Finding │        │ Analysis│         │ Process   │      │
│  └─────────┘        └─────────┘         └───────────┘      │
│                                                              │
│                          VERIFY                              │
│                     ┌─────────────┐                         │
│                     │ Effectiveness│                        │
│                     │ Review       │                        │
│                     └─────────────┘                         │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

**Corrective Action by Control:**

| Control | Nonconformity | Corrective Action |
|---------|---------------|-------------------|
| Guardrails | Pattern gap | Add new pattern to library |
| Guardrails | False positives | Tune thresholds, refine patterns |
| Judge | Inaccurate evaluation | Update prompts, recalibrate |
| Judge | Missed issues | Adjust sampling, add criteria |
| HITL | SLA breach | Add capacity, improve tooling |
| HITL | Wrong decision | Additional training, clearer guidance |

---

## Annex A Mapping (Informative)

ISO 42001 Annex A provides reference control objectives. Here's how the framework maps:

### A.2 Policies for AI

| Control | Framework Implementation |
|---------|-------------------------|
| A.2.1 AI policy | AI policy in governance framework |
| A.2.2 Policy review | Quarterly policy review in operating rhythm |

### A.5 Resources for AI Systems

| Control | Framework Implementation |
|---------|-------------------------|
| A.5.3 Computing resources | Platform capacity (Bedrock, Databricks, Foundry) |
| A.5.4 Data for AI | Data governance controls; PII guardrails |

### A.6 AI System Lifecycle

| Control | Framework Implementation |
|---------|-------------------------|
| A.6.1 Requirements | Risk classification; control selection |
| A.6.2 Design | Control design per tier |
| A.6.3 Verification | Pre-deployment testing; Judge calibration |
| A.6.4 Deployment | Control activation; HITL staffing |
| A.6.5 Operation | Guardrails, Judge, HITL operating |
| A.6.6 Monitoring | Metrics framework; dashboards |

### A.7 Data for AI Systems

| Control | Framework Implementation |
|---------|-------------------------|
| A.7.1 Data acquisition | Data governance policy |
| A.7.2 Data quality | Judge evaluation; data validation |
| A.7.3 Data preparation | Guardrail input validation |

### A.8 Information for Interested Parties

| Control | Framework Implementation |
|---------|-------------------------|
| A.8.1 Information about AI | Transparency controls; HITL explanations |
| A.8.2 User information | HITL provides human explanation |

### A.9 AI System Documentation

| Control | Framework Implementation |
|---------|-------------------------|
| A.9.1 Technical documentation | System documentation per tier |
| A.9.2 AI system instructions | Guardrail configuration; Judge criteria |

### A.10 Responsible Use of AI

| Control | Framework Implementation |
|---------|-------------------------|
| A.10.1 Responsible AI | Ethics policy; bias monitoring (Judge) |
| A.10.2 Human oversight | HITL model; human accountability |
| A.10.3 AI system autonomy | Guardrail scope limits; HITL for consequential actions |

### A.11 Third-Party Relationships

| Control | Framework Implementation |
|---------|-------------------------|
| A.11.1 Supply chain | Vendor assessment; platform security |
| A.11.2 Customer relationships | Customer-facing guardrails; complaint handling |

---

## Certification Readiness Checklist

### Documentation Requirements

| Document | Framework Source | Ready? |
|----------|------------------|--------|
| AI policy | ai-governance-operating-model.md | ☐ |
| Risk classification methodology | ../../core/risk-tiers.md | ☐ |
| Risk assessment records | Per-system risk assessments | ☐ |
| Control standards | 03-control-families.md | ☐ |
| Guardrail configuration | Platform configuration records | ☐ |
| Judge criteria | Judge prompt documentation | ☐ |
| HITL procedures | ../../core/controls.md | ☐ |
| Monitoring records | Dashboard exports, metric reports | ☐ |
| Management review minutes | Committee meeting records | ☐ |
| Internal audit reports | Audit documentation | ☐ |
| Corrective action records | Remediation tracking | ☐ |

### Control Implementation Evidence

| Control | Evidence | Ready? |
|---------|----------|--------|
| Guardrails deployed | Configuration, block logs | ☐ |
| Judge operational | Evaluation logs, calibration records | ☐ |
| HITL functional | Queue metrics, decision records | ☐ |
| Logging complete | Log samples, retention verification | ☐ |
| Monitoring active | Dashboard screenshots, alert records | ☐ |

### Operational Evidence

| Activity | Evidence | Ready? |
|----------|----------|--------|
| Risk assessments conducted | Assessment records | ☐ |
| HITL reviews performed | Review records | ☐ |
| Findings actioned | Remediation records | ☐ |
| Management reviews held | Meeting minutes | ☐ |
| Internal audits conducted | Audit reports | ☐ |
| Training delivered | Training records | ☐ |

---

## Summary

The framework's technical controls directly support ISO 42001 compliance:

| ISO Theme | Primary Control | Supporting Controls |
|-----------|-----------------|---------------------|
| Risk assessment (6.1, 8.2) | Risk classification | Judge (ongoing), HITL (validation) |
| Risk treatment (6.1, 8.3) | Guardrails | Judge (monitoring), HITL (verification) |
| AI lifecycle (8.4) | All controls | Platform integration |
| Monitoring (9.1) | Logging | All metrics |
| Audit (9.2) | Logging | All evidence |
| Improvement (10.2) | Feedback loops | All controls |

**Key message:** Technical controls are not separate from governance—they ARE the operational implementation of ISO 42001 requirements.

---

*This document is part of the AI Security Blueprint — Discussion Draft*
