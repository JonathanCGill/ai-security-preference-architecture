# AI Vendor Assessment Questionnaire

Use this questionnaire when assessing AI vendors, foundation model providers, and AI SaaS platforms. Adapt based on the risk tier of your intended use case.

---

## Instructions

| Risk Tier | Required Sections |
|-----------|------------------|
| CRITICAL | All sections, independent verification required |
| HIGH | All sections |
| MEDIUM | Sections 1-5, 7 |
| LOW | Sections 1-3 |

---

## 1. Vendor Identification

| Question | Response |
|----------|----------|
| Vendor legal name | |
| Primary contact | |
| Contract owner (internal) | |
| Assessment date | |
| Next review date | |
| Service/product being assessed | |
| Intended use case(s) | |
| Risk tier of use case | |

---

## 2. Security Certifications and Compliance

| Question | Response | Evidence Required |
|----------|----------|-------------------|
| Does the vendor hold SOC 2 Type II certification? | Yes / No / In Progress | Certificate, scope |
| Does the vendor hold ISO 27001 certification? | Yes / No / In Progress | Certificate, scope |
| Is the vendor ISO 42001 certified (AI Management System)? | Yes / No / In Progress | Certificate |
| What regulatory frameworks does the vendor comply with? | | Attestation |
| Has the vendor completed an independent AI security assessment? | Yes / No | Report |
| When was the last penetration test? | Date | Summary report |
| Are there any outstanding critical/high findings? | Yes / No | Remediation plan |

---

## 3. Data Handling

### 3.1 Data Processing

| Question | Response |
|----------|----------|
| What data does the service process? | |
| Where is data processed (regions/jurisdictions)? | |
| Is data encrypted in transit? (Protocol) | |
| Is data encrypted at rest? (Algorithm) | |
| Who has access to customer data? | |
| How is access logged and monitored? | |

### 3.2 Data Retention

| Question | Response |
|----------|----------|
| How long is input data retained? | |
| How long is output data retained? | |
| How long are interaction logs retained? | |
| Can data retention be configured/disabled? | Yes / No |
| Is zero-retention option available? | Yes / No |
| What is the data deletion process? | |
| What is the data deletion SLA? | |

### 3.3 Data Use

| Question | Response | Acceptable? |
|----------|----------|-------------|
| Is customer data used to train models? | Yes / No / Opt-out | |
| Is customer data used to improve services? | Yes / No / Opt-out | |
| Is customer data shared with third parties? | Yes / No | |
| Can data use be contractually restricted? | Yes / No | |

---

## 4. Model Information

### 4.1 Model Provenance

| Question | Response |
|----------|----------|
| What model(s) power the service? | |
| Who developed/trained the model(s)? | |
| What is the model version? | |
| When was the model last updated? | |
| Is model versioning available? | Yes / No |
| Can model version be pinned? | Yes / No |

### 4.2 Training Data

| Question | Response |
|----------|----------|
| What data was used to train the model? | |
| How was training data sourced and curated? | |
| What bias mitigation was applied during training? | |
| What content filtering was applied to training data? | |
| Is training data provenance documented? | Yes / No |
| Has the model been tested for bias? | Yes / No |

### 4.3 Model Behaviour

| Question | Response |
|----------|----------|
| What guardrails/safety measures are built into the model? | |
| What content policies does the model enforce? | |
| How is the model monitored for drift? | |
| What is the known false positive/negative rate for safety measures? | |
| Can guardrails be configured by the customer? | Yes / No |

---

## 5. Operational Security

### 5.1 Access Control

| Question | Response |
|----------|----------|
| What authentication methods are supported? | |
| Is MFA supported/required? | |
| Is SSO/SAML supported? | |
| How are API keys managed? | |
| Is key rotation supported? | |
| What is the minimum privilege model? | |

### 5.2 Logging and Monitoring

| Question | Response |
|----------|----------|
| What is logged? | |
| Are logs tamper-evident? | |
| Can logs be exported to customer SIEM? | |
| What is log retention period? | |
| Is real-time monitoring available? | |
| What alerting capabilities exist? | |

### 5.3 Incident Response

| Question | Response |
|----------|----------|
| What is the incident notification SLA? | |
| How are security incidents communicated? | |
| What is the vendor's incident response process? | |
| Has the vendor had any security breaches in the past 3 years? | |
| If yes, what was the root cause and remediation? | |

---

## 6. Model Updates and Change Management

| Question | Response |
|----------|----------|
| How are model updates communicated? | |
| What is the advance notice period for breaking changes? | |
| Can customers opt out of automatic updates? | |
| What is the deprecation policy for model versions? | |
| How are behaviour changes documented? | |
| Is there a changelog available? | |

---

## 7. Business Continuity and Exit

### 7.1 Availability

| Question | Response |
|----------|----------|
| What is the SLA for uptime? | |
| What is the historical uptime (last 12 months)? | |
| What redundancy/failover exists? | |
| What is the RTO/RPO? | |
| Is there a multi-region option? | |

### 7.2 Vendor Lock-in and Exit

| Question | Response |
|----------|----------|
| What is the contract termination notice period? | |
| What data is returned upon termination? | |
| What format is data returned in? | |
| Is there an exit assistance clause? | |
| What alternative vendors exist for this capability? | |
| How difficult would migration be? | |

---

## 8. AI-Specific Risks

### 8.1 Prompt Injection and Adversarial Attacks

| Question | Response |
|----------|----------|
| What protection exists against prompt injection? | |
| Has the model been tested against adversarial inputs? | |
| What is the process for reporting and fixing vulnerabilities? | |
| Is there a bug bounty or vulnerability disclosure programme? | |

### 8.2 Output Quality and Safety

| Question | Response |
|----------|----------|
| What output filtering/guardrails exist? | |
| How is hallucination risk managed? | |
| What happens when the model doesn't know an answer? | |
| Is confidence scoring available? | |
| Can harmful content categories be configured? | |

### 8.3 Explainability and Auditability

| Question | Response |
|----------|----------|
| What explainability features are available? | |
| Can decision rationale be logged? | |
| Is source attribution available (for RAG systems)? | |
| How can outputs be audited? | |

---

## 9. Commercial and Legal

| Question | Response |
|----------|----------|
| What is the liability model for AI outputs? | |
| Is there indemnification for IP infringement? | |
| What insurance does the vendor carry? | |
| Are audit rights included in the contract? | |
| What jurisdiction governs the contract? | |
| Is there a DPA (Data Processing Agreement)? | |

---

## 10. Assessment Summary

| Category | Score (1-5) | Critical Issues | Notes |
|----------|-------------|-----------------|-------|
| Security certifications | | | |
| Data handling | | | |
| Model provenance | | | |
| Operational security | | | |
| Change management | | | |
| Business continuity | | | |
| AI-specific risks | | | |
| Commercial terms | | | |

**Overall Assessment:**

| Decision | Conditions |
|----------|-----------|
| ☐ Approved | |
| ☐ Approved with conditions | |
| ☐ Not approved | |

**Assessor:** ____________________  
**Date:** ____________________  
**Review date:** ____________________

---

*AI Security Blueprint — Vendor Assessment Template*
