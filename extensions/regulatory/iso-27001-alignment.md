# ISO 27001:2022 Alignment

This document maps the AI Security Blueprint to ISO 27001:2022 information security requirements.

---

## Executive Summary

**The AI framework is complementary to, not a replacement for, ISO 27001.**

ISO 27001 provides the Information Security Management System (ISMS). The AI framework addresses AI-specific risks within that ISMS. Most ISO 27001 controls apply to AI systems — they're information systems. The AI framework adds controls for AI-specific risks that ISO 27001 doesn't explicitly address.

| Standard | Scope | Relationship |
|----------|-------|--------------|
| ISO 27001:2022 | Information security management | Foundation |
| ISO 42001:2023 | AI management system | AI-specific extension |
| This framework | AI security controls | Practical implementation |

**Key finding:** No fundamental changes needed. Some controls need explicit AI interpretation. A few gaps exist where AI introduces novel risks.

---

## ISO 27001 Structure Overview

ISO 27001:2022 has:
- **Clauses 4-10:** Management system requirements (PDCA)
- **Annex A:** 93 controls in 4 themes

| Theme | Controls | AI Relevance |
|-------|----------|--------------|
| Organizational (A.5) | 37 | High — governance, policy, supplier management |
| People (A.6) | 8 | Medium — awareness, responsibilities |
| Physical (A.7) | 14 | Low — standard physical security applies |
| Technological (A.8) | 34 | High — access, logging, development, data protection |

---

## Annex A Control Mapping

### A.5 Organizational Controls

| Control | ISO 27001 Requirement | AI Framework Mapping | Gap? |
|---------|----------------------|---------------------|------|
| **A.5.1** Policies for information security | Establish information security policy | AI.1.1 AI Policy Framework | ✅ Aligned — AI policy extends infosec policy |
| **A.5.2** Information security roles | Define and allocate roles | AI.1.2 Governance Structure, AI.1.3 Accountability | ✅ Aligned |
| **A.5.3** Segregation of duties | Separate conflicting duties | Implicit in risk tiers and approval workflows | ✅ Aligned |
| **A.5.4** Management responsibilities | Management enforce policy | AI Governance Committee | ✅ Aligned |
| **A.5.5** Contact with authorities | Maintain regulatory contact | Regulatory alignment sections | ✅ Aligned |
| **A.5.6** Contact with special interest groups | Participate in security forums | Not explicit | ⚠️ Minor gap |
| **A.5.7** Threat intelligence | Collect and analyse threat info | OWASP/MITRE mapping, emerging trends | ✅ Aligned |
| **A.5.8** Information security in project management | Include security in projects | AI.4 Development Security | ✅ Aligned |
| **A.5.9** Inventory of information and assets | Maintain asset inventory | AI.3.1 AI System Inventory | ✅ Aligned |
| **A.5.10** Acceptable use of information | Define acceptable use | AI policy, guardrails enforce acceptable use | ✅ Aligned |
| **A.5.11** Return of assets | Return assets on termination | Standard HR process applies | ✅ Standard |
| **A.5.12** Classification of information | Classify information | AI.2 Risk Management (data sensitivity) | ✅ Aligned |
| **A.5.13** Labelling of information | Label classified information | Data governance controls | ✅ Aligned |
| **A.5.14** Information transfer | Secure information transfer | Guardrails on data in prompts/responses | ✅ Aligned |
| **A.5.15** Access control | Restrict access based on need | AI.6 Model Security, scope controls | ✅ Aligned |
| **A.5.16** Identity management | Manage identities | Standard IAM applies to AI systems | ✅ Standard |
| **A.5.17** Authentication information | Protect credentials | Standard applies; API keys for AI services | ✅ Standard |
| **A.5.18** Access rights | Provision access appropriately | Risk tier determines access requirements | ✅ Aligned |
| **A.5.19** Information security in supplier relationships | Manage supplier security | See "Vendor AI" in scope section | ⚠️ Needs AI-specific supplier requirements |
| **A.5.20** Addressing security in supplier agreements | Include security in contracts | Not explicit for AI vendors/APIs | ⚠️ Gap — add AI supplier requirements |
| **A.5.21** Managing information security in ICT supply chain | Secure supply chain | Model supply chain (foundation models) | ⚠️ Gap — add model provenance |
| **A.5.22** Monitoring, review of supplier services | Monitor suppliers | API/model monitoring | ⚠️ Needs explicit coverage |
| **A.5.23** Information security for cloud services | Secure cloud use | Platform guidance (Bedrock, Databricks, etc.) | ✅ Aligned |
| **A.5.24** Information security incident management planning | Plan incident response | AI.12 Incident Response | ✅ Aligned |
| **A.5.25** Assessment and decision on information security events | Assess events | Judge findings, HITL triage | ✅ Aligned |
| **A.5.26** Response to information security incidents | Respond to incidents | AI.12 Incident Response | ✅ Aligned |
| **A.5.27** Learning from information security incidents | Learn from incidents | Feedback loops, guardrail updates | ✅ Aligned |
| **A.5.28** Collection of evidence | Collect evidence | AI.11 Logging (tamper-evident for CRITICAL) | ✅ Aligned |
| **A.5.29** Information security during disruption | Maintain security in disruption | Not explicit | ⚠️ Gap — add AI continuity |
| **A.5.30** ICT readiness for business continuity | ICT continuity | Not explicit for AI | ⚠️ Gap — add AI continuity |
| **A.5.31** Legal, statutory, regulatory requirements | Identify requirements | Regulatory alignment sections | ✅ Aligned |
| **A.5.32** Intellectual property rights | Protect IP | Not explicit | ⚠️ Gap — add AI/model IP |
| **A.5.33** Protection of records | Protect records | AI.11 Logging retention | ✅ Aligned |
| **A.5.34** Privacy and protection of PII | Protect personal data | Guardrails (PII filtering), GDPR alignment | ✅ Aligned |
| **A.5.35** Independent review of information security | Independent review | Internal audit in ISO 42001 section | ✅ Aligned |
| **A.5.36** Compliance with policies and standards | Ensure compliance | Judge monitors compliance | ✅ Aligned |
| **A.5.37** Documented operating procedures | Document procedures | Operating model documents | ✅ Aligned |

---

### A.6 People Controls

| Control | ISO 27001 Requirement | AI Framework Mapping | Gap? |
|---------|----------------------|---------------------|------|
| **A.6.1** Screening | Screen personnel | Standard HR applies | ✅ Standard |
| **A.6.2** Terms and conditions of employment | Include security in contracts | Standard HR applies | ✅ Standard |
| **A.6.3** Information security awareness | Security training | Not explicit for AI | ⚠️ Gap — add AI security training |
| **A.6.4** Disciplinary process | Enforce policy | Standard HR applies | ✅ Standard |
| **A.6.5** Responsibilities after termination | Post-employment duties | Standard HR applies | ✅ Standard |
| **A.6.6** Confidentiality or non-disclosure agreements | Require NDAs | Standard applies | ✅ Standard |
| **A.6.7** Remote working | Secure remote work | Standard applies | ✅ Standard |
| **A.6.8** Information security event reporting | Report security events | Escalation to HITL, incident reporting | ✅ Aligned |

---

### A.7 Physical Controls

Physical controls (A.7.1 - A.7.14) apply to AI systems as they do to any information system. No AI-specific mapping needed — standard physical security controls apply to:
- Data centres hosting AI infrastructure
- Endpoints accessing AI systems
- Physical security of on-premise GPU/TPU infrastructure

**No gaps identified.**

---

### A.8 Technological Controls

| Control | ISO 27001 Requirement | AI Framework Mapping | Gap? |
|---------|----------------------|---------------------|------|
| **A.8.1** User endpoint devices | Secure endpoints | Standard applies | ✅ Standard |
| **A.8.2** Privileged access rights | Manage privileged access | CRITICAL tier requires elevated approval | ✅ Aligned |
| **A.8.3** Information access restriction | Restrict access to information | Scope enforcement, guardrails on data access | ✅ Aligned |
| **A.8.4** Access to source code | Protect source code | AI.4.2 applies to AI code | ✅ Aligned |
| **A.8.5** Secure authentication | Implement secure auth | Standard applies; API key management | ✅ Standard |
| **A.8.6** Capacity management | Manage capacity | Circuit breakers include resource limits | ✅ Aligned |
| **A.8.7** Protection against malware | Protect against malware | Guardrails detect malicious inputs | ✅ Aligned |
| **A.8.8** Management of technical vulnerabilities | Manage vulnerabilities | AI.4 Development Security | ✅ Aligned |
| **A.8.9** Configuration management | Manage configuration | Guardrail configuration, platform config | ✅ Aligned |
| **A.8.10** Information deletion | Secure deletion | Logging retention policies include deletion | ✅ Aligned |
| **A.8.11** Data masking | Mask sensitive data | Guardrails: PII redaction/filtering | ✅ Aligned |
| **A.8.12** Data leakage prevention | Prevent data leakage | Output guardrails, DLP integration | ✅ Aligned |
| **A.8.13** Information backup | Backup information | Standard applies | ✅ Standard |
| **A.8.14** Redundancy of information processing facilities | Ensure redundancy | Standard applies | ✅ Standard |
| **A.8.15** Logging | Log activities | AI.11 Comprehensive Logging | ✅ Aligned |
| **A.8.16** Monitoring activities | Monitor for anomalies | Judge monitoring, circuit breaker anomaly detection | ✅ Aligned |
| **A.8.17** Clock synchronisation | Synchronise clocks | Standard applies | ✅ Standard |
| **A.8.18** Use of privileged utility programs | Control privileged utilities | Agent tool controls (AG.2.4) | ✅ Aligned |
| **A.8.19** Installation of software | Control software installation | Model deployment controls | ✅ Aligned |
| **A.8.20** Networks security | Secure networks | Standard applies; API endpoint security | ✅ Standard |
| **A.8.21** Security of network services | Secure network services | API security for AI services | ✅ Aligned |
| **A.8.22** Segregation of networks | Segregate networks | Standard applies | ✅ Standard |
| **A.8.23** Web filtering | Filter web content | Guardrails filter content | ✅ Aligned |
| **A.8.24** Use of cryptography | Use cryptography | Standard applies; model encryption | ✅ Standard |
| **A.8.25** Secure development life cycle | Secure SDLC | AI.4 Development Security | ✅ Aligned |
| **A.8.26** Application security requirements | Define security requirements | Control selection by risk tier | ✅ Aligned |
| **A.8.27** Secure system architecture | Design secure architecture | Three-layer control model | ✅ Aligned |
| **A.8.28** Secure coding | Code securely | AI.4 Development Security | ✅ Aligned |
| **A.8.29** Security testing | Test security | AI.4.2 Testing, Judge validation | ✅ Aligned |
| **A.8.30** Outsourced development | Secure outsourced dev | Not explicit for AI | ⚠️ Gap — add outsourced AI dev |
| **A.8.31** Separation of environments | Separate dev/test/prod | AI.4.3 Deployment Security | ✅ Aligned |
| **A.8.32** Change management | Manage changes | Model versioning, guardrail changes | ✅ Aligned |
| **A.8.33** Test information | Protect test data | Standard applies | ✅ Standard |
| **A.8.34** Protection of information systems during audit testing | Protect during audits | Standard applies | ✅ Standard |

---

## Gap Analysis Summary

### Gaps Identified

| Gap | ISO 27001 Control | Required Addition |
|-----|-------------------|-------------------|
| **AI supplier management** | A.5.19, A.5.20, A.5.21, A.5.22 | Add AI-specific supplier/vendor requirements |
| **AI security training** | A.6.3 | Add AI security awareness training |
| **AI business continuity** | A.5.29, A.5.30 | Add AI system continuity requirements |
| **AI/model IP protection** | A.5.32 | Add model IP and training data protection |
| **Outsourced AI development** | A.8.30 | Add controls for outsourced AI work |

### Controls That Need AI-Specific Interpretation

| Control | Standard Interpretation | AI-Specific Interpretation |
|---------|------------------------|---------------------------|
| **A.5.7** Threat intelligence | General threat intel | OWASP LLM Top 10, MITRE ATLAS, prompt injection trends |
| **A.5.9** Asset inventory | IT asset inventory | AI system inventory including models, agents, tools |
| **A.5.21** Supply chain | Software supply chain | Model supply chain — foundation model provenance |
| **A.8.7** Malware protection | Detect malicious software | Detect malicious prompts, adversarial inputs |
| **A.8.11** Data masking | Mask data in storage/transit | PII filtering in prompts and responses |
| **A.8.12** Data leakage prevention | Prevent data exfiltration | Prevent model leaking training data, sensitive context |
| **A.8.16** Monitoring | Monitor for security events | Judge monitoring for policy violations, anomalies |

---

## Required Additions

### 1. AI Supplier Management (New Section)

**Control: AI.13 AI Supplier and Vendor Management**

#### AI.13.1 AI Vendor Assessment

**Requirement:** Assess AI vendors and foundation model providers for security.

**Assessment criteria:**

| Criterion | Questions |
|-----------|-----------|
| Model security | How is the model protected? What access controls exist? |
| Data handling | How is prompt/response data handled? Retention? |
| Training data | What data was used to train? Any known issues? |
| Security certifications | SOC 2, ISO 27001, etc.? |
| Incident response | How are security incidents handled? Notification? |
| Model updates | How are model changes communicated? |
| API security | Authentication, encryption, rate limiting? |

**Evidence:** Vendor assessment records, certifications

---

#### AI.13.2 AI Vendor Agreements

**Requirement:** Include AI-specific terms in vendor agreements.

**Required terms:**

| Term | Purpose |
|------|---------|
| Data processing | How vendor processes prompt/response data |
| Data residency | Where data is processed and stored |
| Model use restrictions | Restrictions on using your data for training |
| Security requirements | Minimum security controls vendor must maintain |
| Incident notification | Timeline and process for breach notification |
| Audit rights | Right to audit or receive audit reports |
| Liability | Allocation of liability for AI outputs |
| Indemnification | Coverage for IP, privacy, or other claims |

**Evidence:** Contract terms, DPAs

---

#### AI.13.3 Model Provenance

**Requirement:** Understand and document the provenance of AI models used.

**Documentation:**

| Element | Content |
|---------|---------|
| Model identity | Name, version, provider |
| Training data | Known information about training data sources |
| Known limitations | Documented limitations, biases, failure modes |
| License terms | How model may be used |
| Update history | Version history, change log |

**Evidence:** Model documentation, provenance records

---

#### AI.13.4 API and Model Monitoring

**Requirement:** Monitor third-party AI APIs and models for security issues.

**Monitoring:**

| Aspect | What to Monitor |
|--------|----------------|
| Availability | API uptime, response times |
| Behaviour changes | Unexpected changes in model behaviour |
| Security advisories | Vendor security announcements |
| Version changes | Model version updates |
| Cost anomalies | Unexpected usage or cost spikes |

**Evidence:** Monitoring dashboards, alert logs

---

### 2. AI Security Training (New Section)

**Control: AI.14 AI Security Awareness**

#### AI.14.1 AI Security Training

**Requirement:** Train relevant personnel on AI security risks and controls.

**Training audiences:**

| Audience | Training Content |
|----------|-----------------|
| All staff | AI acceptable use, recognising AI outputs, reporting concerns |
| AI developers | Secure AI development, prompt injection, adversarial attacks |
| AI operators | Guardrail configuration, HITL processes, incident response |
| Security team | AI threat landscape, AI-specific vulnerabilities, monitoring |
| Leadership | AI risk overview, governance responsibilities |

**Frequency:** Annual for all; additional for role-specific

**Evidence:** Training records, completion rates

---

### 3. AI Business Continuity (New Section)

**Control: AI.15 AI System Continuity**

#### AI.15.1 AI Continuity Planning

**Requirement:** Include AI systems in business continuity planning.

**Considerations:**

| Aspect | Requirement |
|--------|-------------|
| AI system criticality | Classify AI systems by business criticality |
| Fallback procedures | Define fallback when AI unavailable |
| Manual processes | Maintain ability to operate without AI for critical processes |
| Recovery objectives | RTO/RPO for AI systems |
| Vendor dependency | Plan for vendor AI service disruption |

**Evidence:** BCP documentation, AI system RTOs

---

#### AI.15.2 AI System Resilience

**Requirement:** Design AI systems for resilience.

**Implementation:**

| Control | Purpose |
|---------|---------|
| Graceful degradation | System continues with reduced functionality if AI fails |
| Fallback models | Secondary models if primary unavailable |
| Circuit breakers | Prevent cascade failures |
| Timeout handling | Graceful handling of AI timeouts |
| Rate limit handling | Graceful handling of API rate limits |

**Evidence:** Architecture documentation, resilience testing records

---

### 4. AI Intellectual Property (New Section)

**Control: AI.16 AI Intellectual Property**

#### AI.16.1 Model IP Protection

**Requirement:** Protect intellectual property in AI models.

**Protection measures:**

| Asset | Protection |
|-------|------------|
| Custom models | Access controls, encryption, licensing |
| Fine-tuned models | Document base model license compliance |
| Training data | Data rights verification, access controls |
| Prompts/system prompts | Protect as trade secrets where applicable |
| Agent configurations | Version control, access controls |

**Evidence:** IP inventory, protection measures documentation

---

#### AI.16.2 Third-Party IP Compliance

**Requirement:** Ensure AI use complies with third-party IP rights.

**Compliance measures:**

| Risk | Mitigation |
|------|------------|
| Training data rights | Verify rights to use training data |
| Model license compliance | Comply with foundation model licenses |
| Generated content | Understand IP status of AI outputs |
| Copyright infringement | Guardrails to prevent generating infringing content |

**Evidence:** License compliance records, legal review

---

### 5. Outsourced AI Development (Addition to AI.4)

**Control: AI.4.5 Outsourced AI Development**

**Requirement:** Apply security controls to outsourced AI development.

**Requirements for outsourced AI work:**

| Requirement | Purpose |
|-------------|---------|
| Security requirements in contract | Specify required controls |
| Secure development practices | Require secure coding, testing |
| Code review | Review code before acceptance |
| Model validation | Validate models before deployment |
| Data handling | Specify how training/test data handled |
| IP ownership | Clarify ownership of models, code |
| Handover | Secure knowledge transfer |

**Evidence:** Contracts, code review records, validation records

---

## Updated Control Family Index

| ID | Family | Purpose |
|----|--------|---------|
| AI.1 | Governance | Policies, roles, accountability |
| AI.2 | Risk Management | Classification, assessment, monitoring |
| AI.3 | Inventory & Documentation | Registration, documentation, lineage |
| AI.4 | Development Security | Secure development, testing, deployment |
| AI.5 | Data Governance | Data quality, privacy, protection |
| AI.6 | Model Security | Model protection, validation, monitoring |
| AI.7 | Runtime Controls — Guardrails | Inline input/output validation |
| AI.8 | Runtime Controls — LLM-as-Judge | Async assurance and monitoring |
| AI.9 | Human Oversight | HITL, escalation, accountability |
| AI.10 | Agentic Controls | Agent-specific safeguards |
| AI.11 | Logging & Monitoring | Observability, alerting, audit |
| AI.12 | Incident Response | Detection, response, recovery |
| **AI.13** | **AI Supplier Management** | **Vendor assessment, agreements, monitoring** |
| **AI.14** | **AI Security Awareness** | **Training for AI-specific risks** |
| **AI.15** | **AI System Continuity** | **BCP for AI systems** |
| **AI.16** | **AI Intellectual Property** | **Model and data IP protection** |

---

## ISO 27001 Statement of Applicability Considerations

When documenting AI systems in your Statement of Applicability (SoA):

| Control | SoA Consideration |
|---------|-------------------|
| A.5.9 Asset inventory | Include AI systems, models, agents in asset inventory |
| A.5.19-22 Supplier | Include AI/model vendors in supplier management |
| A.8.11 Data masking | Include PII filtering in AI I/O |
| A.8.12 Data leakage | Include AI-specific DLP (prompt/response filtering) |
| A.8.15 Logging | Include AI interaction logging |
| A.8.16 Monitoring | Include Judge monitoring, anomaly detection |
| A.8.25-29 Development | Include AI development lifecycle |

---

## Integration Approach

### If You Have Existing ISO 27001 Certification

1. **Extend, don't replace** — AI controls integrate into existing ISMS
2. **Update risk assessment** — Add AI-specific threats and risks
3. **Update asset inventory** — Add AI systems, models, agents
4. **Update policies** — Add AI policy as extension of infosec policy
5. **Update SoA** — Document AI-specific control implementation
6. **Update supplier management** — Add AI vendor requirements
7. **Update training** — Add AI security awareness
8. **Update BCP** — Include AI systems

### If You're Implementing Both

1. **Implement ISO 27001 first** — Foundation for all information security
2. **Layer AI controls on top** — AI framework extends ISMS
3. **Single integrated audit** — Cover both in one management system

---

## Summary

### Alignment Status

| Category | Status |
|----------|--------|
| Core security controls | ✅ Well aligned — AI systems are information systems |
| Logging and monitoring | ✅ Well aligned — AI.11 exceeds baseline |
| Development security | ✅ Well aligned — AI.4 covers SDLC |
| Access control | ✅ Well aligned — risk tier drives access |
| Incident response | ✅ Well aligned — AI.12 covers AI incidents |
| Supplier management | ⚠️ Gap — Added AI.13 |
| Training | ⚠️ Gap — Added AI.14 |
| Business continuity | ⚠️ Gap — Added AI.15 |
| Intellectual property | ⚠️ Gap — Added AI.16 |

### Changes Made

| Change | Rationale |
|--------|-----------|
| Added AI.13 AI Supplier Management | ISO 27001 A.5.19-22 need AI-specific interpretation |
| Added AI.14 AI Security Awareness | ISO 27001 A.6.3 needs AI-specific training |
| Added AI.15 AI System Continuity | ISO 27001 A.5.29-30 need AI coverage |
| Added AI.16 AI Intellectual Property | ISO 27001 A.5.32 needs AI/model IP coverage |
| Added AI.4.5 Outsourced AI Development | ISO 27001 A.8.30 needs AI coverage |

### Key Principle

**The AI framework operates within the ISMS, not parallel to it.**

AI systems are information systems. ISO 27001 controls apply. The AI framework adds AI-specific controls where the standard controls are insufficient for AI-specific risks (prompt injection, model manipulation, agentic behaviour, etc.).

---

*AI Security Blueprint — Discussion Draft*
