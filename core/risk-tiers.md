# Risk Tiers and Control Selection

---

## Tier Definitions

### CRITICAL

Direct, automated decisions affecting customers, finances, or safety.

- Autonomous decision-making with real-world impact
- Financial transactions or credit decisions
- Health, safety, or legal implications
- Minimal human review before action

**Examples:** Credit approval, fraud blocking, medical triage, automated trading

### HIGH

Significant influence on decisions or access to sensitive data.

- Recommendations typically followed
- Access to confidential customer data
- External-facing with brand impact
- Decisions affecting employment or access

**Examples:** Customer service with account access, HR screening, legal document analysis

### MEDIUM

Moderate impact, primarily internal, human review expected.

- Internal users with domain expertise
- Output is input to human decision
- Limited sensitive data access
- Recoverable errors

**Examples:** Internal Q&A, document drafting, code generation with review

### LOW

Minimal impact, non-sensitive context.

- Public information only
- No personal data access
- No decisions, just information
- Easy to verify or ignore

**Examples:** Public FAQ bot, content suggestions, general lookup

---

## Control Matrix

### Input Guardrails

| Control | LOW | MEDIUM | HIGH | CRITICAL |
|---------|-----|--------|------|----------|
| Injection detection | Basic | Standard | Enhanced + ML | Multi-layer |
| PII detection | — | Warn | Block | Block + alert |
| Content policy | Basic | Standard | Strict | Maximum |
| Rate limiting | Standard | Standard | Strict | Strict + anomaly |

### Output Guardrails

| Control | LOW | MEDIUM | HIGH | CRITICAL |
|---------|-----|--------|------|----------|
| Content filtering | Basic | Standard | Enhanced | Maximum |
| PII in output | Warn | Block | Block + alert | Block + alert + log |
| Grounding check | — | Basic | Required | Required + citation |
| Confidence threshold | — | — | Required | Required + escalation |

### Judge Evaluation

| Aspect | LOW | MEDIUM | HIGH | CRITICAL |
|--------|-----|--------|------|----------|
| Coverage | — | 10% sampling | 100% | 100% |
| Timing | — | Batch (daily) | Near real-time | Real-time |
| Depth | — | Basic quality | Full policy | Full + reasoning |
| Escalation | — | Weekly | Same-day | Immediate |

### Human Oversight

| Aspect | LOW | MEDIUM | HIGH | CRITICAL |
|--------|-----|--------|------|----------|
| Review trigger | Exceptions | Sampling + flags | All flags | All significant |
| Review SLA | 72h | 24h | 4h | 1h |
| Reviewer | General | Domain knowledge | Expert | Senior + expert |
| Approval required | — | — | High-impact | All external |

### Logging

| Aspect | LOW | MEDIUM | HIGH | CRITICAL |
|--------|-----|--------|------|----------|
| Content | Metadata | Full | Full + context | Full + reasoning |
| Retention | 90 days | 1 year | 3 years | 7 years |
| Protection | Standard | Standard | Enhanced | Immutable |

---

## Classification Process

### Step 1: Score Impact Dimensions

| Dimension | Question |
|-----------|----------|
| Decision authority | Makes decisions or informs them? |
| Reversibility | Can errors be undone? At what cost? |
| Data sensitivity | PII? Financial? Confidential? |
| Audience | Internal experts or external customers? |
| Scale | How many affected? |
| Regulatory | Regulated activity? |

### Step 2: Apply Highest Tier

If any dimension suggests higher tier, use it.

| Scenario | Key Factor | Tier |
|----------|------------|------|
| Internal Q&A, no PII | Low stakes | MEDIUM |
| Internal Q&A, HR data access | Sensitive data | HIGH |
| Customer chat, public info | External but low stakes | LOW |
| Customer chat, sees accounts | Sensitive data | HIGH |
| Customer chat, takes actions | Actions + external | CRITICAL |

### Step 3: Document

- Tier assigned
- Driving factors
- Mitigating controls
- Review date (annual minimum)

---

## Tier Changes

**Upgrade triggers:**
- Adding sensitive data access
- Adding action capability
- Moving internal → external
- Incident revealing higher risk

**Downgrade requirements:**
- 6+ months stable operation
- No significant incidents
- Reduced scope documented
- Governance approval
