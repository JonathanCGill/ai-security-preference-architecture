# AI System Model Card Template

Use this template to document AI systems in your inventory (AI.3.1, AI.3.2).

---

## System Identification

| Field | Value |
|-------|-------|
| **System Name** | [Unique identifier] |
| **System ID** | [Registry ID] |
| **Version** | [Current version] |
| **Owner** | [Accountable individual] |
| **Team** | [Responsible team] |
| **Risk Tier** | ☐ CRITICAL ☐ HIGH ☐ MEDIUM ☐ LOW |
| **Status** | ☐ Development ☐ Testing ☐ Production ☐ Deprecated |
| **Last Updated** | [Date] |

---

## System Description

### Purpose

[What does this system do? What business problem does it solve?]

### Users

| User Type | Description | Estimated Volume |
|-----------|-------------|------------------|
| [e.g., Customer service agents] | [How they use the system] | [Users/day] |
| [e.g., Customers] | [How they interact] | [Interactions/day] |

### Inputs and Outputs

| Input | Source | Sensitivity |
|-------|--------|-------------|
| [e.g., Customer query] | [e.g., Chat interface] | [e.g., May contain PII] |
| [e.g., Account data] | [e.g., Core banking API] | [e.g., Financial, PII] |

| Output | Destination | Sensitivity |
|--------|-------------|-------------|
| [e.g., Response text] | [e.g., Customer] | [e.g., May contain PII] |
| [e.g., Recommended action] | [e.g., Agent dashboard] | [e.g., Internal] |

---

## Model Information

### Foundation Model

| Field | Value |
|-------|-------|
| **Provider** | [e.g., Anthropic, OpenAI, AWS Bedrock] |
| **Model** | [e.g., Claude 3.5 Sonnet] |
| **Version** | [Specific version string] |
| **Deployment** | [e.g., API, self-hosted, managed] |
| **Region** | [e.g., us-east-1, eu-west-1] |

### Fine-tuning / Customisation

| Field | Value |
|-------|-------|
| **Fine-tuned?** | ☐ Yes ☐ No |
| **Training data** | [If yes, describe training data] |
| **Training date** | [When fine-tuning occurred] |
| **Validation** | [How fine-tuned model was validated] |

### RAG / Knowledge Base

| Field | Value |
|-------|-------|
| **RAG enabled?** | ☐ Yes ☐ No |
| **Knowledge sources** | [List sources] |
| **Update frequency** | [How often knowledge base is refreshed] |
| **Vector store** | [e.g., Pinecone, pgvector, OpenSearch] |
| **Embedding model** | [Model used for embeddings] |

---

## Risk Assessment

### Risk Tier Rationale

[Why was this tier selected? Reference the control selection guide.]

### Data Sensitivity

| Data Type | Present? | Handling |
|-----------|----------|----------|
| PII | ☐ Yes ☐ No | [How it's protected] |
| Financial data | ☐ Yes ☐ No | [How it's protected] |
| Health data | ☐ Yes ☐ No | [How it's protected] |
| Credit data | ☐ Yes ☐ No | [How it's protected] |
| Authentication credentials | ☐ Yes ☐ No | [Should be NO] |

### Decision Impact

| Question | Answer |
|----------|--------|
| Does this system make consequential decisions? | ☐ Yes ☐ No |
| Are decisions fully automated or human-reviewed? | ☐ Automated ☐ Human-reviewed |
| What is the impact of a wrong decision? | [Describe] |

### Identified Risks

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| [e.g., Hallucination] | [H/M/L] | [H/M/L] | [Control applied] |
| [e.g., Data leakage] | [H/M/L] | [H/M/L] | [Control applied] |
| [e.g., Prompt injection] | [H/M/L] | [H/M/L] | [Control applied] |

---

## Controls Applied

### Guardrails (AI.7)

| Control | Implemented | Configuration |
|---------|-------------|---------------|
| Input length limits | ☐ Yes ☐ No | [Max tokens] |
| Input format validation | ☐ Yes ☐ No | [Rules] |
| Prompt injection detection | ☐ Yes ☐ No | [Method] |
| Output PII filtering | ☐ Yes ☐ No | [Method] |
| Output content filtering | ☐ Yes ☐ No | [Categories blocked] |
| Grounding verification | ☐ Yes ☐ No | [Method] |

### Judge (AI.8)

| Control | Implemented | Configuration |
|---------|-------------|---------------|
| Judge evaluation enabled | ☐ Yes ☐ No | |
| Sampling rate | | [Percentage] |
| Evaluation criteria | | [List criteria] |
| Finding SLA | | [Hours/days] |
| Escalation path | | [Where findings go] |

### Human Oversight (AI.9)

| Control | Implemented | Configuration |
|---------|-------------|---------------|
| HITL review required | ☐ Yes ☐ No | [When] |
| Override capability | ☐ Yes ☐ No | |
| Escalation procedures | ☐ Yes ☐ No | [Path] |
| Accountability assigned | ☐ Yes ☐ No | [Who] |

### Agentic Controls (AG.1-AG.4)

*Complete if system is agentic*

| Control | Implemented | Configuration |
|---------|-------------|---------------|
| Plan disclosure | ☐ Yes ☐ No ☐ N/A | |
| Plan approval | ☐ Yes ☐ No ☐ N/A | [When required] |
| Action guardrails | ☐ Yes ☐ No ☐ N/A | |
| Circuit breakers | ☐ Yes ☐ No ☐ N/A | [Limits] |
| Scope enforcement | ☐ Yes ☐ No ☐ N/A | [Boundaries] |
| Tool controls | ☐ Yes ☐ No ☐ N/A | [Tool list] |
| Trajectory logging | ☐ Yes ☐ No ☐ N/A | |

### Logging (AI.11)

| Field | Value |
|-------|-------|
| Logging enabled | ☐ Yes ☐ No |
| Log content | ☐ Full ☐ Metadata only ☐ Sampled |
| Retention period | [Days/years] |
| Tamper protection | ☐ Yes ☐ No |
| Log location | [Where logs are stored] |

---

## Dependencies

### Upstream Systems

| System | Data Provided | Criticality |
|--------|---------------|-------------|
| [e.g., Core Banking] | [e.g., Account balances] | [HIGH/MEDIUM/LOW] |

### Downstream Systems

| System | Data Consumed | Impact if Unavailable |
|--------|---------------|----------------------|
| [e.g., CRM] | [e.g., Interaction summary] | [Describe] |

### Third-Party Dependencies

| Vendor | Service | Criticality | Fallback |
|--------|---------|-------------|----------|
| [e.g., Anthropic] | [e.g., Claude API] | [HIGH] | [e.g., Switch to OpenAI] |

---

## Performance

### SLAs

| Metric | Target | Current |
|--------|--------|---------|
| Availability | [e.g., 99.9%] | [Actual] |
| Response latency (p50) | [e.g., 500ms] | [Actual] |
| Response latency (p99) | [e.g., 2000ms] | [Actual] |
| Error rate | [e.g., <1%] | [Actual] |

### Quality Metrics

| Metric | Target | Current |
|--------|--------|---------|
| Judge quality score | [e.g., >90%] | [Actual] |
| Guardrail block rate | [e.g., <5%] | [Actual] |
| False positive rate | [e.g., <1%] | [Actual] |
| HITL escalation rate | [e.g., <10%] | [Actual] |
| Customer satisfaction | [e.g., >4.0/5] | [Actual] |

---

## Limitations and Known Issues

### Model Limitations

| Limitation | Impact | Mitigation |
|------------|--------|------------|
| [e.g., Knowledge cutoff date] | [e.g., Can't answer about recent events] | [e.g., RAG with current data] |
| [e.g., Hallucination risk] | [e.g., May generate false information] | [e.g., Grounding verification] |

### Known Issues

| Issue | Status | Workaround |
|-------|--------|------------|
| [Describe issue] | ☐ Open ☐ In progress ☐ Accepted | [If any] |

---

## Compliance

### Regulatory Alignment

| Regulation | Applicable? | Compliance Status |
|------------|-------------|-------------------|
| GDPR | ☐ Yes ☐ No | ☐ Compliant ☐ In progress ☐ Gap |
| EU AI Act | ☐ Yes ☐ No | ☐ Compliant ☐ In progress ☐ Gap |
| SR 11-7 / SS1/23 | ☐ Yes ☐ No | ☐ Compliant ☐ In progress ☐ Gap |
| HIPAA | ☐ Yes ☐ No | ☐ Compliant ☐ In progress ☐ Gap |
| [Other] | ☐ Yes ☐ No | ☐ Compliant ☐ In progress ☐ Gap |

### Audit History

| Date | Type | Outcome | Findings |
|------|------|---------|----------|
| [Date] | [e.g., Internal audit] | [Pass/Fail] | [Summary] |

---

## Change History

| Date | Version | Change | Author |
|------|---------|--------|--------|
| [Date] | [Version] | [What changed] | [Who] |

---

## Approvals

| Role | Name | Date | Signature |
|------|------|------|-----------|
| System Owner | | | |
| Security Review | | | |
| Risk Sign-off | | | |
| Governance Approval | | | |

---

## Review Schedule

| Review Type | Frequency | Next Due |
|-------------|-----------|----------|
| Full reassessment | [Per tier] | [Date] |
| Control verification | [Per tier] | [Date] |
| Performance review | [Monthly/Quarterly] | [Date] |

---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
