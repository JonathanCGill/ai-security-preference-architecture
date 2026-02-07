# AI Governance Operating Model — ISO 42001 Alignment

This document defines how a centralised AI governance function operates in alignment with ISO 42001:2023 (AI Management System) while leveraging the control framework (guardrails, Judge, HITL) and enterprise AI platforms.

---

## Executive Summary

ISO 42001 establishes requirements for an **AI Management System (AIMS)**—a structured approach to governing AI across the organisation. This document maps:

1. **ISO 42001 clauses** → Framework controls
2. **Centralised governance function** → Roles, responsibilities, processes
3. **Platform capabilities** → Technical implementation (Bedrock, Databricks, Foundry)
4. **Operating rhythm** → Governance cadence and decision rights

**Core principle:** Centralised policy, federated execution, unified assurance.

---

## ISO 42001 Structure Overview

ISO 42001 follows the Annex SL high-level structure common to all ISO management systems:

![ISO 42001 PDCA Cycle](../../images/iso-42001-pdca.svg)

| Clause | Title | Focus |
|--------|-------|-------|
| 4 | Context of the organisation | Understand internal/external factors affecting AI |
| 5 | Leadership | Management commitment and policy |
| 6 | Planning | Risk assessment and objectives |
| 7 | Support | Resources, competence, awareness, communication |
| 8 | Operation | AI system lifecycle controls |
| 9 | Performance evaluation | Monitoring, measurement, audit, review |
| 10 | Improvement | Nonconformity, corrective action, continual improvement |

**Annex A** provides AI-specific controls (similar to ISO 27001 Annex A for information security).

---

## Governance Operating Model

![AI Governance Operating Model](../../images/ai-governance-operating-model.svg)

### Three Lines Model for AI

| Line | Function | AI Governance Role |
|------|----------|-------------------|
| **1st Line** | Business / Engineering | Build and operate AI systems; execute controls |
| **2nd Line** | AI Governance / Risk | Set policy; provide oversight; operate assurance |
| **3rd Line** | Internal Audit | Independent assurance; validate effectiveness |

### Centralised vs Federated

| Aspect | Centralised (AI Governance Function) | Federated (Business Units) |
|--------|--------------------------------------|---------------------------|
| Policy | ✅ Sets AI policy and standards | Implements policy |
| Risk framework | ✅ Defines risk tiers and methodology | Performs risk assessments |
| Control standards | ✅ Defines control requirements | Implements controls |
| Tooling/platforms | ✅ Selects and governs platforms | Uses platforms |
| Judge configuration | ✅ Defines evaluation criteria | May customise for use case |
| HITL processes | ✅ Defines requirements | Staffs and operates |
| Monitoring | ✅ Aggregates and reports | Monitors own systems |
| Approval (CRITICAL) | ✅ Approves CRITICAL deployments | Requests approval |
| Approval (HIGH) | Reviews | ✅ Approves with risk sign-off |
| Approval (MEDIUM/LOW) | Spot checks | ✅ Self-service with standards |

---

## ISO 42001 Clause Mapping

### Clause 4: Context of the Organisation

**4.1 Understanding the organisation and its context**

| Requirement | Framework Implementation |
|-------------|--------------------------|
| Identify external factors | Regulatory landscape (EU AI Act, sector regulations), competitive environment, technology trends |
| Identify internal factors | AI maturity, risk appetite, existing capabilities, culture |

**4.2 Understanding the needs and expectations of interested parties**

| Interested Party | Needs/Expectations | How Addressed |
|------------------|-------------------|---------------|
| Regulators | Compliance, transparency, human oversight | Risk classification, HITL, audit trail |
| Customers | Fair treatment, privacy, accuracy | Guardrails, Judge quality monitoring, bias testing |
| Employees | Clear guidance, training, tools | Policy, standards, platform governance |
| Board/Executives | Risk management, value delivery | Governance reporting, metrics |
| Data subjects | Rights respected, explanations | HITL for decisions, documentation |

**4.3 Determining the scope of the AIMS**

Scope statement should define:
- Which AI systems are in scope (all? production only? above certain risk tier?)
- Which lifecycle stages (development, deployment, operation, retirement)
- Organisational boundaries
- Exclusions and justifications

**4.4 AI Management System**

The AIMS comprises:
- This governance operating model
- The control framework (AI.1–AI.12)
- Platform implementations (guardrails, Judge, HITL)
- Supporting processes and documentation

---

### Clause 5: Leadership

**5.1 Leadership and commitment**

| Requirement | Implementation |
|-------------|----------------|
| Top management demonstrates commitment | Board/ExCo AI governance charter |
| Policy aligned with strategic direction | AI strategy linked to business strategy |
| Integration with business processes | AI controls embedded in SDLC, procurement, vendor management |
| Resources available | Funded AI governance function, platform investment |
| Achieving intended outcomes | Metrics and reporting to leadership |

**5.2 AI Policy**

The AI Policy should address:

```
AI POLICY — KEY ELEMENTS

1. Purpose and scope
2. Alignment with organisational values
3. Commitment to responsible AI principles
4. Risk-based approach to AI governance
5. Roles and responsibilities
6. Compliance with applicable laws and regulations
7. Commitment to human oversight
8. Continuous improvement commitment
9. Review and update cycle
```

**5.3 Organisational roles, responsibilities, and authorities**

| Role | Responsibilities | Authority |
|------|------------------|-----------|
| **Board / ExCo** | Approve AI strategy and policy; oversee AI risk | Final accountability |
| **AI Governance Committee** | Approve CRITICAL deployments; review AI risk posture; escalation body | Approval for CRITICAL tier |
| **Chief AI Officer / Head of AI** | Lead AI strategy and governance function | Policy decisions |
| **AI Risk Lead** | Manage AI risk framework; oversee assessments | Risk methodology |
| **AI Ethics Lead** | Advise on ethical considerations; review sensitive use cases | Ethics review |
| **Business Unit AI Leads** | Implement AI governance in business units; own AI systems | LOCAL execution |
| **AI Platform Team** | Operate AI platforms; implement technical controls | Platform standards |
| **HITL Reviewers** | Review Judge findings; make decisions on flagged items | Review decisions |

---

### Clause 6: Planning

**6.1 Actions to address risks and opportunities**

| Framework Component | ISO 42001 Alignment |
|--------------------|---------------------|
| AI.2.1 Risk Classification | Systematic identification of AI risks |
| AI.2.2 Risk Assessment | Evaluation of likelihood and impact |
| Risk tier controls | Risk treatment through proportionate controls |
| Judge monitoring | Ongoing risk identification |

**Risk Assessment Integration:**

```
┌─────────────────────────────────────────────────────────────┐
│                  AI RISK ASSESSMENT PROCESS                  │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  1. IDENTIFY                                                 │
│     • Use case analysis                                      │
│     • Data sensitivity                                       │
│     • Decision impact                                        │
│     • Regulatory scope                                       │
│                                                              │
│  2. CLASSIFY                                                 │
│     • Apply risk tier methodology                            │
│     • Document classification rationale                      │
│     • Obtain appropriate approval                            │
│                                                              │
│  3. TREAT                                                    │
│     • Select controls per tier                               │
│     • Implement guardrails, Judge, HITL                      │
│     • Document residual risk                                 │
│                                                              │
│  4. MONITOR                                                  │
│     • Judge ongoing evaluation                               │
│     • HITL review findings                                   │
│     • Periodic reassessment                                  │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

**6.2 AI objectives and planning to achieve them**

| Objective Category | Example Objectives | Metrics |
|-------------------|-------------------|---------|
| Safety | Zero critical safety incidents | Incident count |
| Fairness | No significant bias in CRITICAL systems | Bias metrics |
| Quality | >95% Judge quality scores | Quality scores |
| Compliance | 100% regulatory compliance | Audit findings |
| Oversight | 100% HITL coverage for CRITICAL | HITL coverage |
| Transparency | Documentation complete for all HIGH+ | Documentation audit |

---

### Clause 7: Support

**7.1 Resources**

| Resource Category | Requirements |
|-------------------|--------------|
| AI Governance Function | Staffed with AI risk, ethics, technical expertise |
| Platform investment | Guardrails, Judge, HITL tooling |
| HITL capacity | Reviewers scaled to volume and SLAs |
| Training | AI governance training for all AI practitioners |
| External expertise | Access to legal, regulatory, ethics advisors |

**Governance Function Sizing:**

| Organisation Size | AI Portfolio | Recommended FTE |
|-------------------|--------------|-----------------|
| Small (<5 AI systems) | Low complexity | 1-2 FTE (may be part-time) |
| Medium (5-20 AI systems) | Mixed tiers | 3-5 FTE |
| Large (20-100 AI systems) | Multiple CRITICAL | 8-15 FTE |
| Enterprise (100+ AI systems) | Complex, regulated | 15-30+ FTE |

**7.2 Competence**

| Role | Required Competencies |
|------|----------------------|
| AI Governance Lead | AI/ML fundamentals, risk management, regulatory knowledge |
| AI Risk Analyst | Risk assessment, AI systems, data analysis |
| AI Ethics Specialist | Ethics frameworks, bias assessment, stakeholder engagement |
| Platform Engineer | AI platforms, guardrails implementation, MLOps |
| HITL Reviewer | Domain expertise, judgement, documentation |

**7.3 Awareness**

All personnel involved with AI should be aware of:
- The AI policy
- Their contribution to AIMS effectiveness
- Implications of not conforming
- How to report concerns or incidents

**7.4 Communication**

| What | Who | When | How |
|------|-----|------|-----|
| AI policy | All staff | Onboarding, annually | Intranet, training |
| Governance requirements | AI practitioners | Project initiation | Standards documents |
| Risk decisions | Stakeholders | As needed | Governance forum |
| Performance metrics | Leadership | Quarterly | Dashboard, report |
| Incidents | Affected parties | As required | Incident process |

**7.5 Documented information**

| Document Type | Retention | Owner |
|---------------|-----------|-------|
| AI Policy | Current + 3 years | AI Governance |
| Risk assessments | Life of system + 7 years | Business owner |
| Control documentation | Life of system + 7 years | Platform team |
| Judge evaluations | Per tier (90 days – 7 years) | Platform team |
| HITL decisions | Per tier (1 – 7 years) | HITL team |
| Audit reports | 7 years | Internal Audit |

---

### Clause 8: Operation

**8.1 Operational planning and control**

This is where the framework controls (AI.1–AI.12) are implemented.

**AI System Lifecycle Governance:**

| Stage | Governance Activities | Controls |
|-------|----------------------|----------|
| **Ideation** | Use case review, initial risk screening | AI.2.1 |
| **Design** | Risk assessment, control selection | AI.2.2, AI.3 |
| **Development** | Secure development, testing | AI.4, AI.5, AI.6 |
| **Pre-deployment** | Security review, approval | AI.4.3 |
| **Deployment** | Guardrails, logging enabled | AI.7, AI.11 |
| **Operation** | Judge monitoring, HITL review | AI.8, AI.9 |
| **Change** | Change assessment, re-approval if needed | AI.2, AI.4 |
| **Retirement** | Decommissioning, data retention | AI.3 |

**8.2 AI risk assessment**

See AI.2 Risk Management controls.

**8.3 AI risk treatment**

| Risk Tier | Treatment Approach |
|-----------|-------------------|
| CRITICAL | Full controls, 100% Judge, 100% HITL decisions |
| HIGH | Full controls, 20-50% Judge sampling, HITL escalation |
| MEDIUM | Standard controls, 5-10% Judge sampling, periodic HITL |
| LOW | Basic controls, optional Judge, spot checks |

**8.4 AI system impact assessment**

For HIGH and CRITICAL systems, conduct:
- Fundamental Rights Impact Assessment (FRIA) — required for EU AI Act high-risk
- Data Protection Impact Assessment (DPIA) — if personal data involved
- Bias and Fairness Assessment — especially for consequential decisions

---

### Clause 9: Performance Evaluation

**9.1 Monitoring, measurement, analysis, and evaluation**

**Governance Metrics Framework:**

| Category | Metric | Target | Source |
|----------|--------|--------|--------|
| **Coverage** | % AI systems in inventory | 100% | Inventory |
| **Coverage** | % HIGH+ with guardrails | 100% | Platform |
| **Coverage** | % CRITICAL with 100% Judge | 100% | Platform |
| **Quality** | Judge quality score (avg) | >90% | Judge logs |
| **Quality** | HITL false positive rate | <20% | HITL records |
| **Timeliness** | HITL SLA compliance | >95% | HITL records |
| **Incidents** | AI safety incidents | 0 critical | Incident log |
| **Compliance** | Regulatory findings | 0 major | Audit reports |
| **Maturity** | Maturity assessment score | Per target | Assessment |

**Governance Dashboard:**

![AI Governance Dashboard](../../images/governance-dashboard.svg)

**9.2 Internal audit**

| Audit Type | Frequency | Scope | Auditor |
|------------|-----------|-------|---------|
| AIMS audit | Annual | Full management system | Internal Audit |
| Control effectiveness | Annual | Technical controls sample | Internal Audit / 2nd Line |
| CRITICAL system review | Annual per system | Deep dive on CRITICAL systems | Internal Audit |
| HITL quality | Quarterly | Sample of HITL decisions | AI Governance |
| Judge calibration | Quarterly | Judge accuracy vs human | AI Governance |

**9.3 Management review**

**AI Governance Committee — Quarterly Review Agenda:**

1. Performance metrics review
2. Incident summary
3. Risk posture update
4. Regulatory developments
5. CRITICAL system status
6. Audit findings and actions
7. Resource and capability needs
8. Emerging risks and opportunities
9. Improvement actions

---

### Clause 10: Improvement

**10.1 Continual improvement**

Improvement sources:
- Judge findings → guardrail updates
- HITL feedback → Judge calibration
- Incidents → process improvements
- Audits → control enhancements
- Regulatory changes → policy updates
- Technology advances → capability improvements

**10.2 Nonconformity and corrective action**

| Nonconformity Type | Example | Response |
|--------------------|---------|----------|
| Policy violation | AI deployed without approval | Stop, assess, remediate, prevent recurrence |
| Control failure | Guardrails bypassed | Investigate, fix, strengthen controls |
| HITL failure | SLAs consistently missed | Root cause, capacity adjustment |
| Incident | Customer harm from AI output | Incident response, customer remediation, process fix |

---

## Platform Integration

### How Platforms Support ISO 42001

| ISO 42001 Requirement | Bedrock | Databricks | Foundry |
|-----------------------|---------|------------|---------|
| Risk treatment (8.3) | Guardrails | AI Gateway + Judge | AIP governance |
| Monitoring (9.1) | CloudWatch + custom | Unity Catalog + Lakehouse Monitoring | Inference history |
| Audit trail (7.5) | Inference logging | Inference Tables | Audit logs |
| Human oversight (8.4) | Build yourself | Review App + Deployment Jobs | Ontology workflows |
| Documentation (7.5) | Build yourself | MLflow tracking | Notepad + Model docs |
| Change control (8.1) | Build yourself | Model Registry + Deployment Jobs | Versioning + releases |

### Recommended Architecture

![ISO 42001 Platform Architecture](../../images/iso-42001-platform-architecture.svg)

---

## Governance Operating Rhythm

### Daily

| Activity | Owner | Inputs |
|----------|-------|--------|
| HITL queue processing | HITL Reviewers | Judge findings |
| Immediate escalation handling | On-call | Critical findings |
| Monitoring review | Platform Team | Dashboards |

### Weekly

| Activity | Owner | Inputs |
|----------|-------|--------|
| HITL metrics review | HITL Lead | Queue metrics |
| Guardrail tuning | Platform Team | False positive data |
| Incident triage | AI Risk Lead | Open incidents |

### Monthly

| Activity | Owner | Inputs |
|----------|-------|--------|
| Portfolio review | AI Governance Lead | Inventory changes |
| Metrics reporting | AI Governance Lead | All metrics |
| Judge calibration review | Platform Team | Calibration data |

### Quarterly

| Activity | Owner | Inputs |
|----------|-------|--------|
| AI Governance Committee | Committee Chair | Performance report |
| Maturity assessment | AI Governance Lead | Assessment criteria |
| Policy review | AI Governance Lead | Regulatory changes |
| HITL quality audit | AI Governance | Sample reviews |

### Annually

| Activity | Owner | Inputs |
|----------|-------|--------|
| AIMS internal audit | Internal Audit | Full scope |
| Management review | Senior Leadership | Annual report |
| Policy update | AI Governance Lead | All inputs |
| External audit (if certified) | External Auditor | Full scope |
| Training refresh | AI Governance | All AI practitioners |

---

## Certification Considerations

### Preparing for ISO 42001 Certification

| Phase | Activities | Duration |
|-------|------------|----------|
| **Gap assessment** | Compare current state to ISO 42001 | 4-6 weeks |
| **Remediation** | Close gaps, implement missing controls | 3-6 months |
| **Documentation** | Complete AIMS documentation | 2-3 months |
| **Internal audit** | Verify readiness | 2-4 weeks |
| **Management review** | Confirm readiness | 1-2 weeks |
| **Stage 1 audit** | Documentation review | 1-2 days |
| **Stage 2 audit** | Implementation audit | 3-5 days |
| **Certification** | Certificate issued | — |

### Common Certification Gaps

| Gap | Remediation |
|-----|-------------|
| Incomplete AI inventory | Systematic discovery and registration |
| Missing risk assessments | Retrospective assessments for existing systems |
| No HITL for CRITICAL | Implement HITL processes |
| Inadequate documentation | Document existing practices |
| No Judge/quality assurance | Implement async evaluation |
| Governance not formalised | Establish committee, define roles |

---

## Annex A Control Mapping

ISO 42001 Annex A provides AI-specific controls. Here's the complete mapping:

### A.2 AI Governance

| ISO 42001 Control | Framework Mapping | Notes |
|-------------------|-------------------|-------|
| A.2.2 AI policy | AI.1.1 AI Policy Framework | ✅ Covered |
| A.2.3 Roles and responsibilities | AI.1.2 Governance Structure, AI.1.3 Accountability | ✅ Covered |
| A.2.4 Resources | AI.14 Security Awareness (training) | ✅ Covered |

### A.3 AI System Lifecycle

| ISO 42001 Control | Framework Mapping | Notes |
|-------------------|-------------------|-------|
| A.3.2 AI system inventory | AI.3.1 AI System Inventory | ✅ Covered |
| A.3.3 AI system requirements | AI.4 Development Security | ✅ Covered |
| A.3.4 Third-party components | AI.13 Supplier Management, AI.6 Model Security | ✅ Covered |

### A.4 Risk Management

| ISO 42001 Control | Framework Mapping | Notes |
|-------------------|-------------------|-------|
| A.4.2 AI risk assessment | AI.2.2 Risk Assessment | ✅ Covered |
| A.4.3 AI risk treatment | AI.2.3 + Risk tier controls | ✅ Covered |
| A.4.4 Residual risk acceptance | Governance approval workflows | ✅ Covered |

### A.5 Data Management

| ISO 42001 Control | Framework Mapping | Notes |
|-------------------|-------------------|-------|
| A.5.2 Data acquisition | AI.5 Data Governance | ✅ Covered |
| A.5.3 Data quality | AI.5.2 Data Quality | ✅ Covered |
| A.5.4 Data provenance | AI.13.3 Model Provenance (includes data) | ✅ Covered |
| A.5.5 Data preparation | AI.5 Data Governance | ✅ Covered |

### A.6 AI Development

| ISO 42001 Control | Framework Mapping | Notes |
|-------------------|-------------------|-------|
| A.6.2.2 Design | AI.4.1 Secure Development | ✅ Covered |
| A.6.2.3 Data processing | AI.5 Data Governance | ✅ Covered |
| A.6.2.4 Model building | AI.4 Development Security | ✅ Covered |
| A.6.2.5 Validation | AI.6.2 Model Validation | ✅ Covered |
| A.6.2.6 Verification | AI.4.2 Testing | ✅ Covered |
| A.6.2.7 Deployment | AI.4.3 Deployment Security | ✅ Covered |

### A.7 AI Operation

| ISO 42001 Control | Framework Mapping | Notes |
|-------------------|-------------------|-------|
| A.7.2 Operation and monitoring | AI.8 Judge, AI.11 Logging & Monitoring | ✅ Covered |
| A.7.3 Human oversight | AI.9 Human Oversight (HITL) | ✅ Covered |
| A.7.4 Explainability | AI.3.2 Documentation, Judge reasoning capture | ✅ Covered |
| A.7.5 Bias management | AI.8 Judge (bias evaluation), AI.9 HITL | ✅ Covered |

### A.8 AI System Performance

| ISO 42001 Control | Framework Mapping | Notes |
|-------------------|-------------------|-------|
| A.8.2 Performance monitoring | AI.8 Judge, AI.11 Monitoring | ✅ Covered |
| A.8.3 Performance evaluation | Judge metrics, calibration | ✅ Covered |

### A.9 AI System Support

| ISO 42001 Control | Framework Mapping | Notes |
|-------------------|-------------------|-------|
| A.9.2 Change management | AI.4.4 Change Management (added) | ✅ Covered |
| A.9.3 Incident management | AI.12 Incident Response | ✅ Covered |
| A.9.4 Problem management | AI.12, Feedback loops | ✅ Covered |

### A.10 Improvement

| ISO 42001 Control | Framework Mapping | Notes |
|-------------------|-------------------|-------|
| A.10.2 Corrective action | Clause 10 processes, feedback loops | ✅ Covered |
| A.10.3 Continual improvement | PDCA cycle, Judge→Guardrail feedback | ✅ Covered |

### Agentic AI Mapping (Framework Extension)

ISO 42001 does not explicitly address agentic AI. The framework extends coverage:

| Agentic Concern | ISO 42001 Nearest | Framework Control |
|-----------------|-------------------|-------------------|
| Plan approval | A.7.3 Human oversight | AG.1.3 Plan Approval |
| Action constraints | A.4.3 Risk treatment | AG.2.1-2.4 Execution Controls |
| Circuit breakers | A.8.2 Monitoring | AG.2.2 Circuit Breakers |
| Trajectory evaluation | A.8.3 Performance | AG.3.2 Trajectory Evaluation |
| Multi-agent governance | A.3.2 Inventory | AG.4 Multi-Agent Controls |

---

## Cross-Reference: Framework to ISO 42001

| Framework Control | ISO 42001 Clause | ISO 42001 Annex A |
|-------------------|------------------|-------------------|
| AI.1 Governance | 5 Leadership | A.2 |
| AI.2 Risk Management | 6 Planning | A.4 |
| AI.3 Inventory & Documentation | 7.5 Documented info | A.3.2 |
| AI.4 Development Security | 8.1 Operational planning | A.6 |
| AI.5 Data Governance | 8.1 | A.5 |
| AI.6 Model Security | 8.1 | A.6.2 |
| AI.7 Guardrails | 8.1 | A.4.3, A.7.2 |
| AI.8 Judge | 9.1 Monitoring | A.8 |
| AI.9 Human Oversight | 8.1 | A.7.3 |
| AI.10 Agentic Controls | — (extension) | — |
| AI.11 Logging & Monitoring | 9.1 | A.7.2, A.8.2 |
| AI.12 Incident Response | 10.2 Nonconformity | A.9.3 |
| AI.13 Supplier Management | 8.1 External provision | A.3.4 |
| AI.14 Security Awareness | 7.2, 7.3 Competence | A.2.4 |
| AI.15 Business Continuity | 8.1 | — |
| AI.16 Intellectual Property | 7.5 | — |

---

## Summary

### Key Success Factors

1. **Executive sponsorship** — AI governance needs visible leadership support
2. **Clear accountability** — Named owners for AI systems and governance
3. **Proportionate controls** — Match controls to risk tier
4. **Platform enablement** — Technical controls make governance practical
5. **Feedback loops** — Continuous improvement from Judge findings and HITL
6. **Metrics-driven** — Measure what matters, report to leadership

### The Operating Model in One Page

![AI Governance Operating Model Summary](../../images/iso-42001-operating-model-summary.svg)

---

*This document is part of the AI Security Blueprint — Discussion Draft*
