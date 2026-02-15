# Secure AI Fast Lane

*Pre-approved controls for low-risk AI. If your deployment qualifies, security has already said yes.*

> *This document uses the simplified three-tier system (Tier 1/2/3). See [Risk Tiers — Simplified Tier Mapping](core/risk-tiers.md#simplified-tier-mapping) for the mapping to LOW/MEDIUM/HIGH/CRITICAL.*

---

## The Problem This Solves

Most AI security frameworks optimise for high-risk scenarios. The controls are thorough, the documentation requirements are heavy, and the testing cadences are frequent. That's appropriate for autonomous agents making regulated decisions. It's not appropriate for an internal team using a chatbot to draft meeting notes.

When every AI deployment goes through the same approval process regardless of risk, two things happen: low-risk experiments get delayed by weeks waiting for security review, and security teams drown in assessments for systems that don't need them. Both outcomes slow AI adoption.

The Fast Lane is the framework's answer. It defines a set of **qualification criteria** and a **minimal control set** that allow low-risk AI deployments to go live without a bespoke security assessment. If the deployment meets all four criteria, the controls below are sufficient. No negotiation, no waiting.

---

## Qualification Criteria

All four must be true. If any one fails, the deployment takes the standard path through [Risk Tiers](core/risk-tiers.md).

![Fast Lane Qualification Gate](/images/fast-lane-qualification.svg)

| # | Criterion | What It Means | Why It Matters |
|---|---|---|---|
| 1 | **Internal users only** | The AI output is consumed exclusively by employees. No customer, partner, or public exposure. | Blast radius is limited to the organisation. Reputational and regulatory risk is minimal. |
| 2 | **Read-only** | The AI system does not write to external systems, databases, APIs, or file stores. It generates text, summaries, analysis, or recommendations — but does not execute actions. | No irreversible side effects. If the output is wrong, nothing breaks except a human's document. |
| 3 | **No regulated data** | The system does not process, generate, or have access to PII, financial records, health data, legal documents, or other data subject to regulatory requirements. | Eliminates compliance risk. No data breach, no regulatory notification, no audit trail requirements beyond standard logging. |
| 4 | **Human always in the loop** | A human reviews the AI output before acting on it or passing it downstream. The AI is a tool, not a decision-maker. | The human is the final control layer. The AI's mistakes are caught before they have consequences. |

### Edge Cases

Some deployments will be close to the boundary. The intent is to be practical, not legalistic:

- **"Internal users only" but output gets emailed externally:** Not Fast Lane. The output reaches people outside the organisation.
- **"Read-only" but saves to a shared drive:** Fast Lane, if the shared drive is internal and the save is initiated by the human, not the AI.
- **"No regulated data" but employees paste in customer names:** Depends on jurisdiction. If customer names are PII in your regulatory context, not Fast Lane. If they're not, proceed — but add PII detection to the guardrails.
- **"Human in the loop" but nobody actually reads the output:** This is an operational problem, not a control problem. The criterion is that the process *requires* review, not that every individual user performs it diligently. Address compliance through training, not control architecture.

---

## Fast Lane Control Set

These are the **only** controls required for a qualified Fast Lane deployment.

### 1. Basic Guardrails

**What:** A content filter that blocks known-bad output categories relevant to your organisation.

**Minimum scope:** Topic boundaries (prevent the AI from responding to queries outside its intended purpose), basic toxicity/harm filters, and output length limits.

**What you don't need:** Injection detection, schema validation, hallucination indicators, multi-layer filter chains. These are Tier 2/3 controls. A Fast Lane deployment relies on the human reviewer to catch edge cases that basic filters miss.

**Implementation:** Use your LLM provider's built-in content filters (most have them), or a lightweight open-source guardrail library. This should take hours, not weeks.

### 2. Usage Logging

**What:** A log of every AI interaction — input, output, timestamp, user identity.

**Purpose:** Audit trail for post-hoc review, usage analytics, and incident investigation if something goes wrong. Not real-time evaluation.

**What you don't need:** Real-time output scoring, confidence metrics, automated flagging. Those are Judge functions. Fast Lane deployments log for retrospective analysis, not live intervention.

**Retention:** Follow your organisation's standard log retention policy. No special requirements.

### 3. Feature Flag

**What:** A mechanism to disable the AI feature instantly without a deployment or code change.

**Purpose:** This is your entire Emergency layer. If something goes wrong, turn it off. Users revert to whatever they did before the AI existed.

**Implementation:** A feature flag in your configuration system, an environment variable, or even a manual deployment toggle. The requirement is that one person can disable the feature within minutes, not that it's automated.

### 4. Known Fallback

**What:** Documentation — even a single sentence — of what users do when the AI feature is off.

**Purpose:** Prevents the scenario where six months after deployment, nobody remembers how to do the task without the AI, and disabling the feature causes a work stoppage.

**What this looks like:** "If [AI feature] is unavailable, [team/users] will [manual process]. Contact [name] to restore."

---

## What You Don't Need

Being explicit about what's not required is as important as what is. For a qualified Fast Lane deployment:

| Control | Required? | Why Not |
|---|---|---|
| LLM-as-Judge evaluation | No | The human reviewer serves as the quality layer. Adding a Judge adds latency and cost without proportionate risk reduction. |
| Dedicated human review queue | No | Users review their own outputs as part of their normal workflow. No separate review function needed. |
| Formal PACE resilience document | No | The PACE plan is: "Feature flag off. Users do it manually. Contact [name]." That's it. |
| Quarterly control testing | No | Annual check that the feature flag works and someone knows the fallback process. |
| Non-AI fallback system | No | The fallback is the manual process that existed before the AI. No new system to build or maintain. |
| Incident response playbook | No | Standard IT incident process applies. No AI-specific playbook needed. |
| Risk function sign-off | No | The Fast Lane criteria *are* the risk assessment. If all four are met, the risk is pre-accepted. |

---

## PACE for the Fast Lane

Even Fast Lane deployments get a PACE plan. It's just simple.

| PACE Layer | Fast Lane Implementation |
|---|---|
| **P — Primary** | AI feature running with basic guardrails and usage logging. |
| **A — Alternate** | Guardrail degraded → pass traffic, log everything, fix at next opportunity. (Fail-open is acceptable.) |
| **C — Contingency** | Feature disabled via feature flag. Users revert to manual process. |
| **E — Emergency** | Same as C. There's no scenario where "turn it off" isn't sufficient for a Fast Lane system. |

Testing: Annually, confirm the feature flag works and the manual process is still known.

---

## Governance

### Who Approves a Fast Lane Deployment?

The deployment team self-certifies against the four criteria. No security review required **if and only if** all four criteria are unambiguously met.

If there's ambiguity on any criterion, the deployment goes through standard Tier 1 assessment. The Fast Lane is for clear cases, not borderline ones.

### What If Conditions Change?

If a Fast Lane deployment later evolves to violate any criterion — it starts processing PII, or its output reaches customers, or it gains write access — it **must** be re-assessed through [Risk Tiers](core/risk-tiers.md) before the change goes live. The Fast Lane qualification is not permanent; it applies to a specific configuration.

The usage logs from the Fast Lane period are available to support the re-assessment.

### Periodic Review

Fast Lane deployments should be reviewed annually to confirm they still meet all four criteria. This isn't a security assessment — it's a five-minute check: "Is it still internal? Still read-only? Still no regulated data? Still human-reviewed?" If yes, carry on. If no, re-assess.

---

## Examples

**Qualifies for Fast Lane:**
- Internal chatbot that helps employees draft emails (internal users, read-only, no regulated data, human reviews before sending)
- Code review assistant that suggests improvements (internal developers, read-only, no regulated data, developer decides whether to accept)
- Meeting summary tool that generates notes from transcripts (internal users, read-only, no regulated data, attendees review before distribution)
- Research assistant that summarises internal documents (internal users, read-only, no regulated data, analyst reviews before including in reports)

**Does not qualify:**
- Customer service chatbot (external users — Tier 2+)
- AI that auto-files support tickets (write access — standard Tier 1)
- HR assistant that processes employee records (PII — standard Tier 1+)
- Code generation tool that auto-commits to production (write access, no human review — Tier 2+)
- Internal tool that summarises patient notes (health data — Tier 2+)
- **Any multi-agent system** (multiple agents communicating, delegating, or acting autonomously — see [MASO Framework](maso/))

---

## Relationship to Risk Tiers

The Fast Lane sits below Tier 1. It's not a separate classification — it's a **pre-qualification** that exempts a deployment from the full Tier 1 assessment process.

```
Fast Lane  →  Tier 1  →  Tier 2  →  Tier 3
(self-certify)  (security review)  (security + risk)  (security + risk + regulatory)
```

If a Fast Lane deployment is re-assessed and found to need Tier 1 controls, the gap is small: add a Judge (even sampling at 10-20%), define a proper PACE plan with transition triggers, and schedule quarterly testing. The usage logs from the Fast Lane period provide the baseline data to calibrate the Judge.

---

## For Security Teams

The Fast Lane reduces your workload by removing low-risk assessments from your queue. In return, it asks you to trust the qualification criteria and the self-certification process.

This trust is justified because:

1. The criteria are restrictive — internal, read-only, unregulated, human-reviewed. A system that meets all four genuinely has low blast radius.
2. Usage logging provides a retrospective audit trail. If something goes wrong, you have the data to investigate.
3. The annual review catches scope creep. A Fast Lane system that silently evolved into a Tier 2 system will be identified.
4. The feature flag provides a reliable kill switch. If a Fast Lane system causes problems, one person can stop it in minutes.

What the Fast Lane does *not* do is remove security's authority. If a deployment doesn't clearly meet all four criteria, it goes through you. The Fast Lane handles the easy calls so you can focus on the hard ones.

---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
