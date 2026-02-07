# AI Data Retention Guidance

Data retention requirements for AI systems, covering the 10 data states specific to AI and jurisdictional requirements.

---

## The 10 Data States in AI Systems

AI systems create data in states that traditional retention policies may not address:

| State | Description | Retention Consideration |
|-------|-------------|------------------------|
| 1. At rest in feeder systems | Source data before AI processing | Existing policies apply |
| 2. In transit to AI | Data moving to AI system | Transient, no retention |
| 3. In vector store | Embeddings of source documents | May be invertible; retain as source |
| 4. In model context | Prompt + retrieved context | Session-scoped |
| 5. In model memory | Within-session state | Session-scoped |
| 6. In model response | Generated output | Retain per policy |
| 7. In interaction logs | Full interaction records | Key retention decision |
| 8. In Judge evaluation | Judge inputs and outputs | Retain with interaction |
| 9. In HITL queue | Pending human review | Retain with interaction |
| 10. In backups | Copies of above | Mirror source retention |

---

## Retention by Risk Tier

### CRITICAL Systems

| Data Type | Minimum Retention | Maximum Retention | Rationale |
|-----------|------------------|-------------------|-----------|
| Full interaction logs | 7 years | 10 years | Regulatory, audit, litigation |
| System prompts (versioned) | 7 years | Indefinite | Audit trail |
| Guardrail configuration | 7 years | Indefinite | Audit trail |
| Judge evaluations | 7 years | 10 years | Assurance evidence |
| HITL decisions | 7 years | 10 years | Accountability |
| Model versions used | 7 years | Indefinite | Reproducibility |
| Incidents | 7 years | Indefinite | Lessons learned |

### HIGH Systems

| Data Type | Minimum Retention | Maximum Retention | Rationale |
|-----------|------------------|-------------------|-----------|
| Full interaction logs | 3 years | 7 years | Regulatory, investigation |
| System prompts (versioned) | 3 years | Indefinite | Audit trail |
| Guardrail configuration | 3 years | Indefinite | Audit trail |
| Judge evaluations | 3 years | 5 years | Assurance evidence |
| HITL decisions | 3 years | 5 years | Accountability |
| Model versions used | 3 years | Indefinite | Reproducibility |
| Incidents | 5 years | Indefinite | Lessons learned |

### MEDIUM Systems

| Data Type | Minimum Retention | Maximum Retention | Rationale |
|-----------|------------------|-------------------|-----------|
| Metadata + sampled content | 1 year | 3 years | Trend analysis |
| System prompts (versioned) | 1 year | 3 years | Audit trail |
| Guardrail configuration | 1 year | 3 years | Audit trail |
| Judge evaluations (sampled) | 1 year | 3 years | Assurance evidence |
| Model versions used | 1 year | 3 years | Reproducibility |
| Incidents | 3 years | 5 years | Lessons learned |

### LOW Systems

| Data Type | Minimum Retention | Maximum Retention | Rationale |
|-----------|------------------|-------------------|-----------|
| Basic metadata | 90 days | 1 year | Troubleshooting |
| System prompts (current) | 90 days | 1 year | Reference |
| Incidents | 1 year | 3 years | Lessons learned |

---

## Jurisdictional Requirements

### United Kingdom

| Regulation | Data Type | Requirement |
|------------|-----------|-------------|
| **UK GDPR** | Personal data | Delete when no longer necessary; document lawful basis |
| **FCA SYSC 9** | Records of services and transactions | 5 years minimum |
| **FCA COBS 11** | Order records | 5 years |
| **PRA SS1/23** | Model documentation | Duration of model use + 5 years |
| **Consumer Duty** | Evidence of fair outcomes | 5 years |

### European Union

| Regulation | Data Type | Requirement |
|------------|-----------|-------------|
| **GDPR** | Personal data | Delete when no longer necessary; document lawful basis |
| **EU AI Act** | High-risk AI logs | 6 months minimum, longer if needed for obligations |
| **EU AI Act** | Documentation | Duration of AI system lifecycle |
| **MiFID II** | Transaction records | 5 years |
| **PSD2** | Payment records | 5 years |

### United States

| Regulation | Data Type | Requirement |
|------------|-----------|-------------|
| **SOX** | Financial records | 7 years |
| **HIPAA** | Health information | 6 years |
| **GLBA** | Financial customer information | 5 years |
| **CCPA/CPRA** | Consumer data | Varies; disclose retention periods |
| **SEC Rule 17a-4** | Broker-dealer records | 3-6 years depending on type |
| **State laws** | Varies | Check applicable states |

### Banking-Specific (Global)

| Standard | Data Type | Requirement |
|----------|-----------|-------------|
| **Basel III** | Risk model documentation | Duration of use + review cycle |
| **SR 11-7** | Model documentation, validation | Duration of use + examination cycle |
| **BCBS 239** | Risk data | Sufficient for risk reporting |

---

## Interaction Log Content

### What to Log (by Tier)

| Field | CRITICAL | HIGH | MEDIUM | LOW |
|-------|----------|------|--------|-----|
| Timestamp | ✓ | ✓ | ✓ | ✓ |
| User identity | ✓ | ✓ | ✓ | Optional |
| Session ID | ✓ | ✓ | ✓ | ✓ |
| Model version | ✓ | ✓ | ✓ | Optional |
| Model parameters | ✓ | ✓ | Optional | Optional |
| System prompt version | ✓ | ✓ | ✓ | Optional |
| Full user input | ✓ | ✓ | Sampled | Optional |
| Retrieved context (RAG) | ✓ | ✓ | Reference only | No |
| Full model output | ✓ | ✓ | Sampled | Optional |
| Guardrail results | ✓ | ✓ | ✓ | ✓ |
| Latency metrics | ✓ | ✓ | ✓ | ✓ |
| Cost | ✓ | ✓ | ✓ | Optional |
| Judge evaluation | ✓ | ✓ | Sampled | No |

### What NOT to Log

| Data Type | Reason | Alternative |
|-----------|--------|-------------|
| Full credit card numbers | PCI-DSS | Mask (last 4 digits) |
| Full SSN/national ID | Regulatory | Mask or tokenise |
| Passwords/credentials | Security | Never log |
| Raw biometric data | Privacy | Hash or don't log |
| Health data (unless required) | HIPAA/GDPR | Minimise or mask |

---

## PII in Logs

### Detection and Handling

| Stage | Action |
|-------|--------|
| **At logging time** | Detect PII using guardrails; flag or redact |
| **In storage** | Encrypt at rest; access controls |
| **At retrieval** | Verify authorisation; mask if displaying |
| **At deletion** | Ensure complete removal including backups |

### Redaction vs. Tokenisation

| Approach | Use When | Tradeoff |
|----------|----------|----------|
| **Redaction** | PII not needed for any purpose | Data lost permanently |
| **Tokenisation** | Need to re-identify for investigation | Token mapping must be secured |
| **Masking** | Partial visibility sufficient | Some data visible |
| **Encryption** | Full data needed, access controlled | Key management overhead |

---

## Vector Store Retention

Vector embeddings require special consideration:

| Concern | Guidance |
|---------|----------|
| **Embeddings can be inverted** | Treat embeddings with same classification as source |
| **Deletion complexity** | Deleting from vector store may require rebuild |
| **Versioning** | Track which documents are in which version of store |
| **Staleness** | Set refresh/review cycles (see AI.5.4) |

### Recommended Approach

1. **Classify** vector store content at source data level
2. **Track** lineage from source documents to embeddings
3. **Implement** deletion procedures that work with your vector DB
4. **Verify** deletions are complete (not just soft-deleted)

---

## Judge and HITL Data

### Judge Evaluation Retention

Judge evaluations contain:
- Copy of interaction being evaluated
- Judge's analysis and findings
- Metadata (Judge model version, evaluation time)

**Retain Judge evaluations for the same period as the underlying interaction** — they're part of the audit trail.

### HITL Decision Retention

HITL decisions must capture:
- What the human reviewed
- What decision they made
- Why (if documented)
- Who made the decision
- When

**Retain HITL decisions for accountability** — typically same as interaction retention or longer.

---

## Deletion Procedures

### Standard Deletion

| Step | Action | Verification |
|------|--------|--------------|
| 1 | Identify data eligible for deletion | Query by retention date |
| 2 | Verify no legal hold | Check with legal |
| 3 | Delete from primary storage | Confirm deletion |
| 4 | Delete from backups (per backup policy) | Confirm in next backup cycle |
| 5 | Delete from vector stores if applicable | Verify removal |
| 6 | Log deletion | Maintain deletion record |

### Legal Hold

When litigation or regulatory investigation is anticipated:
1. **Identify** potentially relevant data
2. **Suspend** deletion for that data
3. **Document** the hold scope and duration
4. **Notify** relevant personnel
5. **Release** hold only when legal confirms

---

## Backup Considerations

| Backup Type | Retention Approach |
|-------------|-------------------|
| **Daily incremental** | 30-90 days |
| **Weekly full** | 90 days - 1 year |
| **Monthly archive** | Per data classification |
| **Disaster recovery** | Mirror primary retention |

**Key principle:** Backup retention should not exceed primary retention without explicit justification. Otherwise you have data you should have deleted.

---

## Audit and Compliance

### Documentation Requirements

Maintain documentation of:
- Retention policy (this document)
- Data inventory (what AI data exists where)
- Deletion logs (what was deleted when)
- Legal holds (active and historical)
- Exceptions (with justification and approval)

### Periodic Review

| Review Type | Frequency | Scope |
|-------------|-----------|-------|
| Policy review | Annual | Update for regulatory changes |
| Implementation audit | Annual | Verify policy is followed |
| Deletion verification | Quarterly | Sample check that deletion occurred |
| Legal hold review | Quarterly | Confirm holds still needed |

---

## Implementation Checklist

### Initial Setup

- [ ] Classify all AI data by tier and type
- [ ] Configure log retention periods
- [ ] Implement automated deletion
- [ ] Set up deletion verification
- [ ] Document exceptions process
- [ ] Train operations team

### Ongoing

- [ ] Monitor deletion job success
- [ ] Review and respond to legal holds
- [ ] Update policy for regulatory changes
- [ ] Audit compliance quarterly
- [ ] Report retention metrics

---

*AI Security Blueprint — Data Retention Guidance*
