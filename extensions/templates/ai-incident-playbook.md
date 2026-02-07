# AI Incident Response Playbook

Incident response procedures specific to AI systems. These playbooks supplement, not replace, existing incident response procedures.

---

## Playbook Index

| # | Incident Type | Severity | Page |
|---|--------------|----------|------|
| 1 | [Prompt Injection Attack](#1-prompt-injection-attack) | High-Critical | Below |
| 2 | [Data Leakage via AI Output](#2-data-leakage-via-ai-output) | Critical | Below |
| 3 | [Hallucination with Business Impact](#3-hallucination-with-business-impact) | Medium-Critical | Below |
| 4 | [AI System Producing Biased Outputs](#4-ai-system-producing-biased-outputs) | High-Critical | Below |
| 5 | [Model Provider Breach](#5-model-provider-breach) | High-Critical | Below |
| 6 | [Guardrail Bypass](#6-guardrail-bypass) | High | Below |
| 7 | [Judge System Failure](#7-judge-system-failure) | Medium-High | Below |
| 8 | [Agentic AI Taking Unintended Actions](#8-agentic-ai-taking-unintended-actions) | Critical | Below |
| 9 | [Knowledge Base Poisoning](#9-knowledge-base-poisoning) | High-Critical | Below |
| 10 | [Silent Quality Degradation](#10-silent-quality-degradation) | Medium | Below |

---

## Severity Classification

| Severity | Definition | Response Time |
|----------|------------|---------------|
| **Critical** | Active exploitation, data breach, regulatory breach, or significant customer harm | Immediate (< 1 hour) |
| **High** | Potential for significant harm, control bypass, or integrity compromise | < 4 hours |
| **Medium** | Quality issues, limited scope impact, potential for escalation | < 24 hours |
| **Low** | Minor issues, no customer impact, easily contained | < 72 hours |

---

## 1. Prompt Injection Attack

### Indicators
- Guardrails flagging unusual input patterns
- AI outputs that deviate from expected behaviour
- Instructions appearing in outputs that don't match system prompt
- User reporting unexpected AI behaviour
- Judge flagging anomalous interactions

### Immediate Actions (First 30 minutes)
1. **Assess scope** — Is this a single incident or pattern?
2. **Preserve evidence** — Capture full interaction logs (input, context, output, metadata)
3. **Determine if attack was successful** — Did the AI follow injected instructions?
4. **Identify attack vector** — Direct input? Indirect via retrieved content? Tool output?

### Containment
| If... | Then... |
|-------|---------|
| Attack via direct user input | Update input guardrails with pattern |
| Attack via RAG content | Quarantine affected knowledge base content |
| Attack via tool output | Disable affected tool integration |
| Attack successful and ongoing | Consider taking system offline |

### Investigation
1. Query logs for similar patterns across all interactions
2. Identify if other users/sessions were affected
3. Determine what information or actions the attacker obtained
4. Review guardrail effectiveness — why wasn't this blocked?
5. Check if attack pattern is known (OWASP, public research)

### Recovery
1. Deploy updated guardrails
2. Re-enable system with monitoring on high alert
3. Inform affected users if data was exposed
4. Update Judge criteria to detect this pattern

### Post-Incident
- Root cause analysis
- Guardrail gap analysis
- Update adversarial testing suite
- Consider architectural changes if fundamental weakness identified

---

## 2. Data Leakage via AI Output

### Indicators
- Output guardrails flagging PII/sensitive data
- Customer complaint about receiving another customer's data
- Judge detecting sensitive content in outputs
- Audit finding of improper data disclosure

### Immediate Actions (First 15 minutes)
1. **Capture the output** — Preserve exactly what was disclosed
2. **Identify the data** — What was leaked? Whose data? Classification level?
3. **Identify recipients** — Who received the leaked data?
4. **Stop the bleeding** — Can you prevent further disclosure?

### Containment
| Data Type | Action |
|-----------|--------|
| Single customer's data to another customer | Disable feature, contact both customers |
| Multiple customers' data | Take system offline |
| Regulated data (PII, financial, health) | Invoke data breach procedure |
| Internal/confidential business data | Restrict access, assess impact |

### Regulatory Notification
| Jurisdiction | Notification Requirement | Timeline |
|--------------|-------------------------|----------|
| UK (ICO) | Notify if risk to individuals | 72 hours |
| EU (GDPR) | Notify if risk to individuals | 72 hours |
| US (varies by state) | Check state requirements | Varies |
| Sector-specific (PCI, HIPAA) | Check specific requirements | Varies |

### Investigation
1. How did the data enter the AI context? (RAG, prior conversation, training?)
2. Why didn't output guardrails catch it?
3. Was this a one-time error or systematic issue?
4. Full scope assessment — who else might be affected?

### Recovery
1. Deploy enhanced output guardrails
2. Review data minimisation in prompts and RAG
3. Implement cross-reference checks (verify output doesn't contain data belonging to other users)
4. Consider data isolation architecture

---

## 3. Hallucination with Business Impact

### Indicators
- Customer complaint about incorrect information
- Downstream system acting on false AI-generated data
- Judge flagging unsupported claims
- Audit finding discrepancy between AI output and source data

### Immediate Actions
1. **Verify the hallucination** — Confirm output is actually false
2. **Assess impact** — What decisions were made based on this? What harm occurred?
3. **Identify affected parties** — Who received the false information?

### Impact Categories
| Impact | Example | Response Level |
|--------|---------|----------------|
| Informational error, no action taken | Wrong answer in internal chat | Low |
| Customer received incorrect advice | Wrong product feature description | Medium |
| Business decision based on false data | Incorrect financial figure in report | High |
| Regulatory/legal implications | False compliance statement | Critical |
| Safety implications | Incorrect safety guidance | Critical |

### Containment
1. Correct the record with affected parties
2. If output was used for decisions, flag those decisions for review
3. If output was forwarded downstream, trace and correct

### Investigation
1. Was this a random hallucination or systematic pattern?
2. Did the AI have access to correct source data?
3. Did the AI ignore source data or fabricate?
4. Are guardrails/Judge configured to catch this type of hallucination?

### Recovery
1. Implement grounding verification for this output type
2. Update Judge criteria for hallucination detection
3. Consider requiring source citation for this use case
4. Review if use case risk tier is appropriate

---

## 4. AI System Producing Biased Outputs

### Indicators
- Disparate outcomes detected across protected characteristics
- Customer complaints alleging discrimination
- Audit/testing reveals bias
- Judge flagging potential fairness issues
- Regulatory inquiry

### Immediate Actions
1. **Verify the bias** — Statistical analysis of outputs across groups
2. **Assess scope** — How long has this been occurring? How many affected?
3. **Preserve evidence** — Full logs for investigation
4. **Legal/compliance notification** — This may be a regulatory matter

### Containment
| Bias Severity | Action |
|---------------|--------|
| Statistical anomaly, unclear if bias | Continue monitoring, deeper analysis |
| Clear disparate impact, limited scope | Disable affected feature |
| Systematic discrimination | Take system offline |
| Active regulatory/legal matter | Follow legal counsel |

### Investigation
1. Is the bias in the model itself or in the data/prompts?
2. Can you identify the source of bias?
3. What protected characteristics are affected?
4. What is the quantified impact (rejection rates, outcomes, etc.)?

### Remediation Options
| Source | Remediation |
|--------|-------------|
| Training data bias (foundation model) | Different model, fine-tuning, output calibration |
| RAG data bias | Curate knowledge base |
| Prompt bias | Revise prompts, add debiasing instructions |
| Structural bias | Architectural changes, human review |

### Regulatory Considerations
- Document everything — investigation, findings, remediation
- Consider proactive disclosure vs. wait for inquiry
- Engage legal counsel for discrimination-related bias
- Monitor for similar issues across other AI systems

---

## 5. Model Provider Breach

### Indicators
- Provider notification of security incident
- News reports of provider breach
- Unexplained changes in model behaviour
- Provider communication about data exposure

### Immediate Actions
1. **Confirm the breach** — Contact provider, review official communications
2. **Assess exposure** — What data of yours did the provider have?
3. **Determine if your data was affected** — Request specific confirmation

### Data at Risk Assessment
| Data Category | Risk Level | Action |
|---------------|------------|--------|
| API keys/credentials | Critical | Rotate immediately |
| Customer data in prompts | High | Assess scope, prepare notification |
| System prompts | Medium | Review for sensitive content |
| Interaction logs | Medium-High | Depends on content |
| Fine-tuning data | High | Assess sensitivity |

### Containment
1. Rotate all API keys and credentials
2. If zero-retention was not enabled, assume data was accessible
3. Consider temporarily switching to alternative provider
4. Review what data should not have been sent to provider

### Regulatory Implications
- If customer data was exposed via provider, this may be your breach too
- Notification obligations may apply
- Document your data processing agreement and provider's obligations

---

## 6. Guardrail Bypass

### Indicators
- Attack that should have been blocked reached the model
- Known-bad content appearing in inputs or outputs
- Adversarial testing reveals gap
- User reports inappropriate content

### Immediate Actions
1. **Capture the bypass** — Exact input that evaded detection
2. **Assess impact** — What happened after the bypass?
3. **Determine method** — How did the attacker bypass? (encoding, rephrasing, etc.)

### Bypass Methods and Responses
| Method | Detection | Mitigation |
|--------|-----------|------------|
| Encoding tricks (base64, rot13) | Add decoder to guardrails | Pattern expansion |
| Semantic rephrasing | Classifier miss | Retrain classifier, add examples |
| Multi-turn manipulation | Per-message check only | Add conversation-level analysis |
| Language switching | English-only guardrails | Multilingual guardrails |
| Prompt structure manipulation | Pattern too specific | More flexible patterns |

### Recovery
1. Deploy fix for specific bypass
2. Add to adversarial test suite
3. Review for related bypass vectors
4. Consider if Judge would have caught this (async defence)

---

## 7. Judge System Failure

### Indicators
- Judge not producing evaluations
- Judge producing inconsistent/wrong evaluations
- Backlog of unevaluated interactions
- Judge costs spiking unexpectedly

### Immediate Actions
1. **Assess type of failure** — Down? Degraded? Producing wrong results?
2. **Determine duration** — When did this start? What's the gap?
3. **Assess risk** — How many interactions were not evaluated? What tiers?

### Impact Assessment
| Tier | Judge Down Impact | Action |
|------|-------------------|--------|
| CRITICAL | 100% evaluation required, gap is serious | Increase HITL, consider pause |
| HIGH | Significant sampling, gap matters | Backfill evaluation when restored |
| MEDIUM | Lower sampling, moderate gap | Backfill on best-effort |
| LOW | Spot checks, limited impact | Resume when restored |

### Recovery
1. Restore Judge operation
2. Backfill evaluations for gap period (prioritise by tier)
3. Review why failure occurred
4. Implement redundancy if single point of failure

---

## 8. Agentic AI Taking Unintended Actions

### Indicators
- Agent performed action outside expected scope
- Downstream system received unexpected commands
- Resource consumption spike (API calls, compute, cost)
- Agent achieved goal through unintended means

### Immediate Actions (First 5 minutes)
1. **Stop the agent** — Halt execution immediately
2. **Assess what happened** — What actions were taken?
3. **Determine reversibility** — Can actions be undone?

### Action Assessment
| Action Type | Example | Reversibility |
|-------------|---------|---------------|
| Read-only queries | Database reads | N/A (no harm) |
| Reversible writes | Draft email saved | Undo |
| Sent communications | Email sent | Cannot undo, can follow up |
| Financial transactions | Payment made | May be reversible |
| Data deletion | Records deleted | Restore from backup |
| External API calls | Third-party action triggered | Depends on API |

### Containment
1. Undo reversible actions
2. For irreversible actions, assess damage and notify affected parties
3. Preserve full trajectory log for investigation
4. Disable agent until investigation complete

### Investigation
1. Review full trajectory — planning, actions, reasoning
2. Identify where agent deviated from expected behaviour
3. Was scope enforcement working? Did agent exceed boundaries?
4. Was this goal hijacking (prompt injection) or emergent behaviour?
5. Were circuit breakers triggered? If not, why not?

### Recovery
1. Tighten scope definitions
2. Add circuit breaker for this action type
3. Require approval checkpoint before this action
4. Update Judge to detect this trajectory pattern

---

## 9. Knowledge Base Poisoning

### Indicators
- AI outputs contain unexpected content
- RAG returning content that shouldn't exist
- Knowledge base audit reveals unauthorised content
- AI behaviour changed without prompt changes

### Immediate Actions
1. **Identify the poisoned content** — What was added/modified?
2. **Quarantine** — Remove or isolate affected content
3. **Assess exposure** — How many interactions used poisoned content?

### Investigation
1. How was content modified? (Authorised user? Compromised account? Data pipeline?)
2. When was content modified?
3. What was the intent? (Injection attack? Misinformation? Vandalism?)
4. Full audit of knowledge base for other modifications

### Recovery
1. Restore from known-good backup
2. Implement content integrity validation (checksums, signatures)
3. Review access controls on knowledge base
4. Add anomaly detection on content changes

---

## 10. Silent Quality Degradation

### Indicators
- Gradual decline in user satisfaction scores
- Increasing escalation rates
- Baseline comparison showing drift
- Judge finding rates increasing
- HITL reviewers reporting quality issues

### Immediate Actions
1. **Verify degradation** — Compare current performance to baseline
2. **Identify scope** — All outputs or specific categories?
3. **Determine cause** — Model change? Data change? Guardrail change?

### Common Causes
| Cause | Detection | Mitigation |
|-------|-----------|------------|
| Provider model update | Check model version | Pin version, evaluate new version |
| RAG data staleness | Check data freshness | Refresh data, fix pipeline |
| Guardrail over-filtering | Check false positive rate | Tune guardrails |
| Prompt drift | Review prompt changes | Revert or fix prompts |
| Concept drift | Compare input distributions | Retrain/update as needed |

### Recovery
1. If model version changed, evaluate rolling back
2. If data staleness, refresh and validate
3. If guardrail issue, tune and test
4. Implement more aggressive baseline monitoring

---

## Incident Report Template

```
INCIDENT REPORT

Incident ID: [YYYYMMDD-###]
Date/Time Detected: 
Date/Time Resolved:
Severity: [Critical/High/Medium/Low]
System Affected:
Risk Tier of System:

SUMMARY
[One paragraph description]

TIMELINE
[Chronological events]

ROOT CAUSE
[What caused the incident]

IMPACT
- Users affected:
- Data affected:
- Business impact:
- Regulatory implications:

RESPONSE ACTIONS
[What was done]

REMEDIATION
[What will prevent recurrence]

LESSONS LEARNED
[What we learned]

FOLLOW-UP ACTIONS
| Action | Owner | Due Date | Status |
|--------|-------|----------|--------|
| | | | |
```

---

*AI Security Blueprint — Incident Response Playbook*
