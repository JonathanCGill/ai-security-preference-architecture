# Worked Example: High-Volume Customer Communications

> When time-to-detect equals time-to-harm, control timing becomes a design decision.

This example follows Sentinel Bank (fictional) as they deploy AI-generated customer communications at scale. The critical difference from interactive chat: messages are sent, not displayed. Once delivered, harm is done.

---

## Why This Example Matters

In high-throughput communications (fraud alerts, collections, service notifications), **latency determines whether you have governance or post-mortem reporting**.

The question isn't "should we monitor?" but "when must we act, and how fast?"

---

## The Use Case

**System Name:** Compass (AI-Powered Customer Communications)

**What it does:**
- Generates personalised outbound messages (email, SMS, in-app)
- Responds to customer enquiries across channels
- Drafts follow-up communications for agents
- Handles fraud alerts, payment reminders, service updates

**Scale:**
- 2 million messages per day
- 15 million customers across channels
- Peak: 50,000 messages per hour (fraud alert scenarios)
- 200+ intent categories

**Technology:**
- Claude via AWS Bedrock
- RAG for policy/product retrieval
- Customer data API integration
- Multi-channel delivery (email, SMS, push)

**The critical constraint:** Messages are delivered to customers. Unlike chatbots where output is displayed and the customer responds, these communications go out. A bad message reaches the customer before you know it was bad.

---

## Step 1: Risk Classification

### Assessment

| Factor | Assessment | Score |
|--------|------------|-------|
| **Decision Impact** | Communications may trigger customer action | Medium |
| **Data Sensitivity** | Customer PII, account details, balances | High |
| **User Population** | External customers (15M) | High |
| **Autonomy Level** | Can send messages without human review | High |
| **Regulatory Scope** | Banking (FCA, PRA), GDPR, PECR | High |
| **Reputational Risk** | Direct customer communication at scale | High |
| **Reversibility** | Messages cannot be unsent | High |

### Classification Decision

**Risk Tier: CRITICAL (Tier 4)**

Rationale: High autonomy (auto-send) + irreversibility (can't unsend) + scale (millions of messages) + regulatory exposure = maximum control requirements.

---

## Step 2: The Latency Problem

### Traditional Thinking (Broken)

```
Generate → Send → Log → Evaluate (hours later) → Discover problem
                                                        ↓
                                              50,000 bad messages sent
```

**This doesn't work.** By the time nearline evaluation catches a problem, thousands of messages are already delivered.

### Time-Band Thinking (Required)

Controls must match the **reversibility window**. For communications:
- Once sent, irreversible
- Therefore, high-risk checks must complete before send

---

## Step 3: Three Time Bands

### Band A: Inline (Milliseconds)

**What belongs here:** Anything that must prevent harm before it happens.

| Control | Purpose | Latency |
|---------|---------|---------|
| Bedrock Guardrails | Block toxic, harmful, off-topic content | ~50ms |
| PII/DLP validators | Prevent data leakage | ~10ms |
| Schema validation | Ensure message structure | ~5ms |
| Tool allowlists | Restrict data access | ~5ms |
| Rate limiting | Prevent flood/abuse | ~1ms |

**Implementation:**

```
┌─────────────────────────────────────────────────────────────┐
│                      INLINE CONTROLS                        │
│                                                             │
│  Request → Rate Limit → Schema → Guardrails → Generate     │
│                                       ↓                     │
│                              Output Guardrails → DLP        │
│                                       ↓                     │
│                              PASS/BLOCK decision            │
│                                       │                     │
│                         ┌─────────────┴─────────────┐       │
│                         ↓                           ↓       │
│                      BLOCKED                    PASSED      │
│                    (immediate)              (to next band)  │
└─────────────────────────────────────────────────────────────┘
```

**Latency budget:** 100ms total for inline checks.

**What inline controls catch:**
- Prompt injection attempts
- Toxic or harmful content
- PII in output (account numbers, addresses)
- Off-topic responses
- Malformed messages

**What they miss:**
- Subtle policy violations
- Incorrect information
- Inappropriate tone for context
- Hallucinated product details
- Drift from training baseline

---

### Band B: Near-Real-Time (Seconds)

**What belongs here:** Signals that may not block, but must trigger action quickly.

| Signal | Trigger | Action |
|--------|---------|--------|
| Repeated soft guardrail hits | Same intent, 5 hits in 10 min | Pause auto-send for intent |
| Retrieval anomalies | Empty or conflicting retrievals | Force human review |
| Escalation spike | 3x normal escalation rate | Alert ops, tighten policy |
| Model version rollout | Any metric deviation | Auto-rollback |

**Implementation:**

```
┌─────────────────────────────────────────────────────────────┐
│                    NEAR-REAL-TIME CONTROLS                  │
│                                                             │
│  Events → EventBridge → Kinesis → Aggregator → Alert Rules │
│                                        ↓                    │
│                               ┌────────┴────────┐           │
│                               ↓                 ↓           │
│                          Threshold          Anomaly         │
│                           Alerts           Detection        │
│                               │                 │           │
│                               └────────┬────────┘           │
│                                        ↓                    │
│                              Automated Response             │
│                              - Pause auto-send              │
│                              - Tighten policy               │
│                              - Force human review           │
│                              - Rollback prompt version      │
└─────────────────────────────────────────────────────────────┘
```

**Latency budget:** 5-30 seconds from event to action.

**Example: Detecting Drift After Prompt Rollout**

```
10:00:00 - New prompt version deployed
10:00:15 - First messages generated
10:00:30 - Aggregator sees 12% guardrail soft-hit rate (baseline: 2%)
10:00:35 - Alert fires: "Guardrail anomaly on intent:payment_reminder"
10:00:40 - Circuit breaker triggers: intent moves to draft-only
10:00:45 - Ops notified, investigation begins

Messages affected: ~50 (vs 5,000 if detected hourly)
```

---

### Band C: Nearline/Offline (Minutes to Hours)

**What belongs here:** Heavy analysis and learning loops.

| Analysis | Purpose | Frequency |
|----------|---------|-----------|
| LLM-as-Judge scoring | QA sample evaluation | Every 15 min |
| Drift analysis | Population-level patterns | Hourly |
| Failure clustering | New failure modes | Daily |
| Human calibration | Judge accuracy check | Weekly |

**Implementation:**

```
┌─────────────────────────────────────────────────────────────┐
│                     NEARLINE CONTROLS                       │
│                                                             │
│  Message Log → Sample → Judge Evaluation → Findings Store  │
│                              ↓                              │
│                    ┌─────────┴─────────┐                    │
│                    ↓                   ↓                    │
│              QA Dashboard         Anomaly Jobs              │
│                    │                   │                    │
│                    └─────────┬─────────┘                    │
│                              ↓                              │
│                      Policy Updates                         │
│                      - Guardrail rules                      │
│                      - Prompt improvements                  │
│                      - Training data                        │
└─────────────────────────────────────────────────────────────┘
```

**Why delays don't break safety:** These controls don't stop individual messages. They improve the system over time.

---

## Step 4: The Auto-Send Decision

**Critical design choice:** Not all messages should auto-send.

### Intent Risk Classification

| Intent Category | Risk Level | Send Mode | Rationale |
|-----------------|------------|-----------|-----------|
| Balance notification | Low | Auto-send | Templated, factual, low harm |
| Payment reminder | Low | Auto-send | Templated, clear grounding |
| Fraud alert | Medium | Auto-send | Time-critical, but templated |
| Product recommendation | Medium | Draft-only | Personalised, regulatory risk |
| Complaint response | High | Draft-only | Requires human judgment |
| Collections message | High | Draft-only | Regulatory, reputational |
| Rate change notification | High | Draft-only | Contractual implications |
| Hardship communication | Critical | Draft-only | Vulnerability, regulation |

### The Risk Envelope

Auto-send only when ALL conditions are met:

```
AUTO-SEND = (
    intent.risk_level <= MEDIUM
    AND retrieval.confidence >= 0.9
    AND guardrail.soft_hits == 0
    AND intent.auto_send_enabled == true
    AND circuit_breaker.status == CLOSED
)
```

If any condition fails → draft-only, human approval required.

---

## Step 5: Circuit Breakers

### What They Do

Circuit breakers automatically tighten controls when anomalies are detected, without waiting for human intervention.

### Implementation

```python
class CircuitBreaker:
    def __init__(self, intent_id):
        self.intent_id = intent_id
        self.state = "CLOSED"  # CLOSED = normal, OPEN = blocked
        self.failure_count = 0
        self.threshold = 5
        self.window_seconds = 300
    
    def record_event(self, event_type, severity):
        if event_type in ["guardrail_soft_hit", "retrieval_empty", "judge_flag"]:
            self.failure_count += 1
        
        if self.failure_count >= self.threshold:
            self.trip()
    
    def trip(self):
        self.state = "OPEN"
        # Immediate effects:
        # 1. Disable auto-send for this intent
        # 2. Force human review
        # 3. Alert operations
        # 4. Log for investigation
```

### Circuit Breaker States

| State | Behaviour | Trigger |
|-------|-----------|---------|
| **CLOSED** | Normal operation, auto-send enabled | Default |
| **OPEN** | Auto-send disabled, draft-only | Threshold breached |
| **HALF-OPEN** | Test mode, limited auto-send | Manual reset, testing |

### Example Thresholds

| Metric | Threshold | Window | Action |
|--------|-----------|--------|--------|
| Guardrail soft hits | 5 | 5 min | OPEN |
| Retrieval empty rate | 10% | 10 min | OPEN |
| Judge flags | 3 | 15 min | OPEN |
| Escalation rate | 3x baseline | 30 min | Alert + review |
| Any hard block | 1 | immediate | Alert |

---

## Step 6: Alert Aggregation

### The Problem

Individual alerts are noise. Pattern detection requires aggregation.

### Event Schema

```json
{
  "event_id": "evt_abc123",
  "timestamp": "2024-01-15T14:32:01Z",
  "message_id": "msg_xyz789",
  "intent_id": "payment_reminder",
  "customer_segment": "retail",
  "channel": "email",
  "model_version": "claude-3-sonnet-20240229",
  "prompt_version": "v2.3.1",
  
  "inline_results": {
    "guardrail_passed": true,
    "guardrail_soft_hits": ["tone_formal"],
    "dlp_passed": true,
    "latency_ms": 87
  },
  
  "retrieval_results": {
    "documents_found": 3,
    "confidence": 0.92,
    "conflicts": false
  },
  
  "decision": {
    "action": "auto_send",
    "circuit_breaker_state": "CLOSED"
  }
}
```

### Aggregation Dimensions

| Dimension | Why It Matters |
|-----------|----------------|
| `intent_id` | Problem with specific use case |
| `model_version` | Regression after update |
| `prompt_version` | Prompt change caused issue |
| `customer_segment` | Certain populations affected |
| `channel` | Channel-specific problems |
| `time_window` | Temporal patterns |

### Alert Rules

```yaml
rules:
  - name: "Guardrail anomaly by intent"
    condition: >
      COUNT(guardrail_soft_hits) BY intent_id
      OVER 5_MINUTES > 5
    action:
      - trip_circuit_breaker(intent_id)
      - alert(severity: HIGH, team: ops)
  
  - name: "Retrieval degradation"
    condition: >
      AVG(retrieval.confidence) BY intent_id
      OVER 10_MINUTES < 0.7
    action:
      - alert(severity: MEDIUM, team: ops)
      - force_human_review(intent_id)
  
  - name: "Model version regression"
    condition: >
      (guardrail_soft_hit_rate BY model_version CURRENT)
      > 1.5 * (guardrail_soft_hit_rate BY model_version PREVIOUS)
    action:
      - alert(severity: HIGH, team: ml)
      - consider_rollback(model_version)
```

---

## Step 7: Latency Matrix

### Control Latency by Risk Tier

| Control | Tier 1-2 | Tier 3 | Tier 4 |
|---------|----------|--------|--------|
| Input guardrails | Async | Inline | Inline |
| Output guardrails | Async | Inline | Inline |
| DLP/PII check | Async | Inline | Inline |
| Retrieval validation | Skip | Inline | Inline |
| LLM-as-Judge | Sample (1%) | Sample (10%) | All (async) |
| Human review | Escalation only | High-risk intents | All drafts |
| Circuit breakers | Manual | Threshold | Aggressive |
| Drift detection | Daily | Hourly | Real-time |

### Message Latency Budget (Tier 4)

```
Customer request received           0ms
├─ Rate limiting                   +1ms
├─ Input schema validation         +5ms
├─ Input guardrails               +50ms
├─ Retrieval                     +200ms
├─ Generation                    +800ms
├─ Output guardrails              +50ms
├─ DLP/PII check                  +10ms
├─ Auto-send decision              +5ms
└─ Total                       ~1,120ms

If draft-only:
├─ Queue for review                +5ms
└─ Total (to queue)            ~1,125ms

Human review SLA: 15 minutes for standard, 5 minutes for urgent
```

---

## Step 8: Response Timing

### Two Different "Responses"

**A. Response to the Customer**

Time-sensitive, but you control it.

| Scenario | Response |
|----------|----------|
| Auto-send eligible | Send immediately |
| Draft-only | Queue for human review |
| Uncertain | Send safe acknowledgement |

Safe acknowledgement template:
> "Thank you for your message. We're reviewing this and will respond shortly."

This buys time without sending potentially problematic content.

**B. Response to Misbehaving AI**

This is what your alerting pipeline drives.

| Response Type | Timing | Actions |
|---------------|--------|---------|
| Automated | Seconds | Disable auto-send, tighten policy, rollback prompt |
| Ops team | Minutes | Investigate, escalate, manual intervention |
| Compliance | Hours | Full review, regulatory assessment |

---

## Step 9: AWS Implementation

### Inline (Milliseconds)

```
API Gateway
    ↓
Lambda (router + validation)
    ↓
Bedrock (guardrails + generation)
    ↓
Lambda (output validation + DLP)
    ↓
Decision: Send / Queue / Block
```

### Near-Real-Time (Seconds)

```
All Lambdas → EventBridge
                  ↓
              Kinesis
                  ↓
          Lambda (aggregator)
                  ↓
    ┌─────────────┼─────────────┐
    ↓             ↓             ↓
OpenSearch    DynamoDB      SNS/PagerDuty
(alerting)   (circuit      (notifications)
             breakers)
```

### Nearline (Minutes+)

```
S3 (message logs)
    ↓
Step Functions (sample selector)
    ↓
Bedrock (judge evaluation)
    ↓
OpenSearch (findings store)
    ↓
QuickSight (dashboards)
```

---

## Step 10: Metrics Dashboard

### Real-Time Panel

| Metric | Target | Alert Threshold |
|--------|--------|-----------------|
| Messages/hour | varies | >2x baseline |
| Auto-send rate | 60-70% | <40% or >85% |
| Guardrail block rate | <1% | >2% |
| Guardrail soft-hit rate | <5% | >10% |
| Draft queue depth | <100 | >500 |
| Circuit breakers open | 0 | >0 |

### Quality Panel (Nearline)

| Metric | Target | Alert Threshold |
|--------|--------|-----------------|
| Judge pass rate | >95% | <90% |
| Tone score | >4.0/5.0 | <3.5 |
| Factual accuracy | >98% | <95% |
| Human override rate | <5% | >10% |

### Drift Panel (Daily)

| Metric | Target | Alert Threshold |
|--------|--------|-----------------|
| Intent distribution | stable | >20% shift |
| Failure clustering | known patterns | new clusters |
| Model confidence | stable | >0.1 drop |

---

## Key Takeaways

1. **Time-to-detect = time-to-harm** for irreversible actions. Design controls accordingly.

2. **Three bands, three purposes:**
   - Inline prevents known-bad
   - Near-real-time detects drift before scale
   - Nearline improves the system

3. **Auto-send is a privilege, not a default.** Earn it through risk classification and confidence thresholds.

4. **Circuit breakers are your safety net.** They act faster than humans can.

5. **Aggregation reveals patterns.** Individual events are noise; correlated events are signal.

6. **The safest delay is the one the customer never notices.** A holding message buys time without sending bad content.

---

## Architecture Diagram

![High-Volume Communications Architecture](../../images/example-comms-architecture.svg)

---

## Related

- [Behavioral Anomaly Detection](../../insights/behavioral-anomaly-detection.md) — Aggregation and ML patterns
- [The Verification Gap](../../insights/the-verification-gap.md) — Why latency matters for verification
- [Controls Reference](../../core/controls.md) — Full control specifications
