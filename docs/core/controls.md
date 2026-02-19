# Controls: Guardrails, Judge, and Human Oversight

---

## 1. Guardrails

Real-time controls that block known-bad inputs and outputs.

### Input Guardrails

| Control | What It Catches |
|---------|-----------------|
| **Injection detection** | Attempts to override system prompt |
| **Encoding detection** | Obfuscated attacks (Base64, hex, Unicode) |
| **PII detection** | Personal data in prompts |
| **Content policy** | Prohibited request types |
| **Rate limiting** | Abuse, enumeration |
| **Length limits** | Context stuffing |

**Processing flow:**
```
Input → Decode → Normalise → Pattern Match → ML Classify → Pass/Block
```

### Output Guardrails

| Control | What It Catches |
|---------|-----------------|
| **Content filtering** | Harmful/inappropriate content |
| **PII detection** | Personal data leakage |
| **Grounding check** | Hallucination |
| **Format validation** | Malformed responses |

### Limitations

Guardrails catch **known patterns**. They miss:
- Novel techniques
- Semantic variations
- Context-dependent violations
- Subtle policy violations

This is why the Judge provides the second layer.

> For practical implementation guidance — international PII detection, RAG ingestion filtering, secrets scanning, alerting design, and guardrail exception governance — see **[Practical Guardrails](../insights/practical-guardrails.md)**.

---

## 2. LLM-as-Judge

Async evaluation of interactions for quality and policy compliance.

→ For model selection guidance, see [Judge Model Selection](../extensions/technical/judge-model-selection.md)

### What the Judge Does

| Function | Description |
|----------|-------------|
| Policy compliance | Did the AI follow guidelines? |
| Quality assessment | Accurate, helpful, appropriate? |
| Anomaly detection | Unusual patterns? |
| Risk flagging | What needs human review? |

### What the Judge Does NOT Do

- Block transactions in real-time
- Make final decisions
- Replace human judgment

**The Judge surfaces findings. Humans decide actions.**

### Architecture

**Simple (low volume):**
```
Interactions → Judge → Findings → HITL queue
```

**Two-tier (high volume):**
```
Interactions → Tier 1 (fast/cheap) → Flags only → Tier 2 (thorough) → HITL
```

### Evaluation Criteria

| Criterion | Scoring |
|-----------|---------|
| Policy adherence | Pass / Minor / Major violation |
| Accuracy | Verified / Unverified / Incorrect |
| Appropriateness | Appropriate / Borderline / Inappropriate |
| Safety | Safe / Uncertain / Concerning |

**Output:** PASS / REVIEW / ESCALATE

### Deployment Phases

| Phase | Action on Findings |
|-------|-------------------|
| **Shadow** | Log only, measure accuracy |
| **Advisory** | Surface to humans, learn from feedback |
| **Operational** | Findings drive workflows |

**Start in shadow mode.** Validate accuracy before acting.

### Accuracy

The Judge will make mistakes.

| Error | Impact | Mitigation |
|-------|--------|------------|
| False positive | Unnecessary review | Tune prompts |
| False negative | Missed violations | Human sampling |

**Target:** >90% agreement with human reviewers.

---

## 3. Human Oversight (HITL)

Humans review findings, make decisions, remain accountable.

![HITL Architecture](../images/hitl-architecture.svg)

### Triggers

| Trigger | Response |
|---------|----------|
| Judge flag | Review interaction |
| Guardrail block | Review if legitimate |
| User escalation | Human takes over |
| Sampling | Quality assurance |
| Threshold breach | Investigate pattern |

### Queue Design

| Queue | SLA | Reviewer |
|-------|-----|----------|
| Critical | 1h | Senior + expert |
| High | 4h | Domain expert |
| Standard | 24h | Trained reviewer |
| Sampling | 72h | QA team |

### Actions

| Action | When |
|--------|------|
| Approve | Interaction appropriate |
| Correct | Minor issue, fixable |
| Escalate | Needs senior review |
| Block user | Abuse detected |
| Tune | False positive |

### Prevent Rubber-Stamping

| Control | Purpose |
|---------|---------|
| Canary cases | Verify reviewers catch known-bad |
| Time tracking | Flag too-fast reviews |
| Volume limits | Prevent fatigue |
| Inter-rater checks | Measure consistency |

---

## Implementation Order

1. **Logging** — Can't evaluate what you don't capture
2. **Basic guardrails** — Block obvious attacks
3. **Judge in shadow** — Evaluate without action
4. **HITL queues** — Somewhere for findings
5. **Judge advisory** — Surface to humans
6. **Enhanced guardrails** — Add ML detection
7. **Judge operational** — Drive workflows
8. **Continuous tuning** — Improve from findings

---

*AI Runtime Behaviour Security, 2026 (Jonathan Gill).*
