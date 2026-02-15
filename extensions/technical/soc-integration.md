# SOC Integration for AI Systems

> Bridging AI security monitoring and existing security operations.

## The Problem

You've implemented guardrails, a judge, and human oversight. They generate alerts, logs, and escalations.

Where do these go?

In most enterprises: nowhere useful. AI security telemetry sits in application logs, disconnected from the SOC that handles every other security event. The SOC team doesn't know what an LLM judge alert means. The AI team doesn't know how to write a SOC-compatible runbook.

---

## Alert Taxonomy

AI systems produce security-relevant events that don't map cleanly to existing SOC categories. Define them explicitly.

| Alert Category | Source | Severity Baseline | Examples |
|---------------|--------|-------------------|----------|
| **Guardrail Block** | Input/output guardrails | Low–Medium | PII detected, toxicity blocked, off-topic rejected |
| **Judge Flag** | LLM-as-Judge | Medium–High | Policy violation detected, hallucination scored high, unsafe reasoning |
| **Anomaly** | Behavioral monitoring | Medium | Usage pattern deviation, output distribution shift, latency spike |
| **Prompt Attack** | Guardrails + Judge | High | Prompt injection detected, jailbreak attempt, system prompt extraction |
| **Data Exfiltration Signal** | Output monitoring | High | Bulk data in response, structured data extraction pattern |
| **Agent Boundary Violation** | Agentic controls | High | Unauthorised tool use, delegation policy breach, scope escalation |
| **Model Drift** | Judge assurance metrics | Medium | Judge accuracy degradation, output distribution change |

### Severity Mapping

Map AI alert severity to your existing SOC severity framework:

| AI Severity | SOC Equivalent | Response SLA |
|-------------|---------------|-------------|
| **Low** (single guardrail block) | Informational | Log, aggregate, review weekly |
| **Medium** (judge flag, single anomaly) | Warning | Triage within 4 hours |
| **High** (attack pattern, data exfil signal) | Alert | Triage within 1 hour |
| **Critical** (confirmed breach, agent compromise) | Incident | Immediate response |

Adjust SLAs to match your existing SOC tiers. Don't create a parallel severity system.

---

## Identity Correlation

The hardest operational problem: linking an AI security event to a real identity.

AI telemetry typically gives you:

| Source | Identity Available |
|--------|-------------------|
| API gateway logs | API key, OAuth token, IP address |
| LLM provider logs (Bedrock, Azure OpenAI) | Service principal, request ID |
| Application logs | Session ID, user ID (if your app logs it) |
| Judge evaluation logs | Request ID (if you propagate it) |
| Vector database logs | Service account, query metadata |

To correlate "User X performed action Y that triggered alert Z," you need to join across at least three of these sources.

### Correlation Pattern

```
API Gateway           →  request_id, user_token, timestamp
     ↓
Application Layer     →  request_id, session_id, user_id
     ↓
LLM Provider          →  request_id (if propagated), model, tokens
     ↓
Judge                  →  request_id, evaluation_result, score
     ↓
SIEM                   →  Correlated event: user_id + evaluation_result + model + timestamp
```

**Critical requirement:** Propagate a correlation ID (request_id or trace_id) across every component. Without this, you're guessing.

### Platform-Specific Patterns

**Amazon Bedrock + CloudWatch:**
- Bedrock invocation logs → CloudWatch Logs → EventBridge → SIEM
- Correlation key: `requestId` from Bedrock invocation metadata
- Identity: IAM role/user from CloudTrail

**Databricks + Unity Catalog:**
- Serving endpoint logs → Delta table → Databricks SQL → SIEM export
- Correlation key: `request_id` from serving endpoint
- Identity: Unity Catalog identity mapped to workspace user

**Azure OpenAI + Sentinel:**
- Diagnostic logs → Log Analytics → Sentinel
- Correlation key: `x-ms-client-request-id` header
- Identity: Entra ID from API Management authentication

---

## Escalation Triggers

Define explicit triggers that move AI events from monitoring to investigation.

### Automated Escalation (no analyst required)

| Trigger | Action |
|---------|--------|
| >10 guardrail blocks from same user in 5 minutes | Block user, alert SOC |
| Judge flags >3 high-severity outputs in 1 hour | Disable AI feature for user, alert SOC |
| Prompt injection pattern detected | Log full request/response, alert SOC |
| Agent attempts tool outside allowlist | Halt agent, alert SOC + AI team |

### Analyst-Driven Escalation

| Signal | Triage Question | Escalation Path |
|--------|----------------|-----------------|
| Repeated judge flags, low severity | Is the user testing boundaries or doing legitimate work? | → AI team for context |
| Anomalous output volume | Is this a batch job or data exfiltration? | → SOC L2 for investigation |
| Model drift detected | Did the provider update the model, or is this adversarial? | → AI team + vendor liaison |
| New prompt injection technique | Is this a known pattern or novel? | → SOC L2 + threat intel |

---

## Triage Procedures for AI Alerts

SOC analysts need clear guidance for AI-specific alerts. They are not AI experts. Don't expect them to be.

### Guardrail Block (Low Severity)

```
1. Check: Is this a single event or part of a pattern?
   - Single event → Log and close
   - Pattern (>5 from same user) → Escalate to Medium

2. Check: What was blocked?
   - PII in output → Verify PII type, check if user has legitimate access to that data
   - Toxicity → Log, no further action unless repeated
   - Off-topic → Log, no further action

3. No user contact required for single events.
```

### Judge Flag (Medium Severity)

```
1. Review the judge evaluation alongside the original request and response.

2. Check: Does the response violate policy?
   - Yes → Escalate to AI team for remediation
   - Unclear → Flag for human review in next calibration cycle
   - No (false positive) → Log as FP, feed back to judge tuning

3. Check: Is the user's request legitimate?
   - Research/testing → Confirm with user's manager
   - Appears adversarial → Escalate to High
```

### Prompt Attack (High Severity)

```
1. Capture full request and response (do not summarise — exact content matters).

2. Check: Did the attack succeed?
   - Yes (guardrails/judge bypassed) → Incident. Disable endpoint. Notify AI team immediately.
   - No (blocked by guardrails) → Log technique, update threat intel, monitor for variants.

3. Check: Is this a known technique?
   - Known → Verify guardrails are current, close.
   - Novel → Escalate to AI security team for analysis and guardrail update.

4. Do NOT attempt to reproduce the attack in production.
```

---

## SIEM Integration

### Log Format

Emit AI security events in a format your SIEM can parse. Extend your existing schema rather than creating a new one.

```json
{
  "event_type": "ai_security",
  "timestamp": "2026-02-11T14:30:00Z",
  "severity": "medium",
  "category": "judge_flag",
  "request_id": "req-abc-123",
  "user_id": "jgill@example.com",
  "model": "claude-sonnet-4-5-20250929",
  "judge_model": "gpt-4o",
  "judge_score": 0.82,
  "judge_verdict": "policy_violation",
  "policy_violated": "financial_advice_without_disclaimer",
  "guardrail_result": "pass",
  "tokens_in": 450,
  "tokens_out": 1200,
  "endpoint": "/api/v1/chat",
  "risk_tier": 2
}
```

### Detection Rules (Examples)

**Splunk SPL:**
```spl
index=ai_security category="prompt_attack" 
| stats count by user_id, src_ip 
| where count > 3
| alert severity=high
```

**Sentinel KQL:**
```kql
AISecurity_CL
| where category_s == "judge_flag" and judge_score_d > 0.8
| summarize count() by user_id_s, bin(TimeGenerated, 1h)
| where count_ > 5
```

---

## Runbook Integration

For each alert category, create a runbook in your existing ITSM (ServiceNow, PagerDuty, Jira).

| Alert Category | Runbook Owner | Tool Integration |
|---------------|--------------|-----------------|
| Guardrail Block | AI Platform Team | Automated — no ticket unless threshold breached |
| Judge Flag | AI Security + SOC L1 | Ticket auto-created at Medium severity |
| Prompt Attack | SOC L2 + AI Security | Incident auto-created at High severity |
| Agent Boundary Violation | AI Platform Team + SOC L2 | Incident auto-created |
| Model Drift | AI Platform Team | Alert to AI team, no SOC ticket |

---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
