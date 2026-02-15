# PACE Resilience Overlay for AI Security Controls

**Primary. Alternate. Contingency. Emergency.**

*How the Enterprise AI Security Framework achieves operational resilience through layered, independent control redundancy.*

---

## Why Resilience Needs Its Own Document

This framework's three-layer architecture — Guardrails, LLM-as-Judge, Human Oversight — is designed to catch what each layer misses. But "defence in depth" only delivers resilience if you've answered three questions in advance:

1. **What happens when a layer fails?**
2. **What's the trigger to transition to the next layer?**
3. **What do you do when "turn it off" isn't an option?**

The PACE methodology (Primary, Alternate, Contingency, Emergency) — originally from military communications planning — provides a structured way to answer all three. It ensures that no single point of failure takes down your AI security controls, and that every degradation path is pre-planned, tested, and documented.

This isn't theoretical. If you're running agentic AI systems in production with a small team, and one of those agents has write access to customer-facing systems, you need a plan that works at 3am when your on-call engineer is the only human in the loop.

---

## PACE Mapped to the Three-Layer Architecture

![PACE Resilience Model for AI Security Controls](../images/pace-resilience-layers.svg)

The framework's three control layers map to PACE with an important addition: the **Emergency** layer makes explicit what is often left implicit — the circuit breaker that routes around AI entirely.

| PACE Layer | Control | Mechanism | Speed | What It Catches |
|---|---|---|---|---|
| **P** — Primary | Guardrails | Deterministic pattern matching at API boundary | ~10ms | Known-bad: blocked topics, PII, schema violations, injection patterns |
| **A** — Alternate | LLM-as-Judge | Independent model evaluates outputs asynchronously | ~500ms–5s | Unknown-bad: policy drift, hallucination, subtle manipulation, tone violations |
| **C** — Contingency | Human Oversight | Queue-based review with decision authority | Minutes–hours | Edge cases: ambiguous outputs, regulatory judgment calls, novel scenarios |
| **E** — Emergency | Circuit Breaker | Kill switch, traffic reroute, non-AI fallback path | Immediate | Systemic failure: model compromise, cascading errors, adversarial breach |

### Why This Is Stronger Than Sequential PACE

In traditional PACE communications planning, you transition from Primary to Alternate only when Primary fails. The layers are sequential — you use one at a time.

This framework's architecture is **concurrent by design**. Guardrails and the Judge operate in parallel on every request. Human Oversight is on standby, triggered by confidence thresholds. The Emergency layer is pre-configured and dormant until needed.

This means the system doesn't wait for a layer to fail before engaging the next one. It continuously verifies through multiple independent channels. The PACE overlay adds the pre-planned degradation path — what to do when layers *do* fail — on top of this concurrent verification mesh.

### The Critical PACE Principle: Independent Failure Domains

PACE planning requires that each layer depends on a **different mechanism**, so a single failure can't cascade through all layers simultaneously. The framework satisfies this:

| Layer | Mechanism Type | Dependency |
|---|---|---|
| Guardrails | Deterministic rules engine | Pattern database, API gateway |
| LLM-as-Judge | Probabilistic model inference | Separate LLM, evaluation prompts |
| Human Oversight | Cognitive judgment | Trained personnel, review interface |
| Circuit Breaker | Infrastructure control | Network routing, feature flags, kill switches |

A prompt injection that bypasses guardrails won't automatically fool the Judge (different model, different evaluation criteria). A Judge model failure doesn't impair guardrails or human review. This independence is the foundation of resilience.

---

## The Agentic Problem: When "Turn It Off" Isn't Simple

For a stateless generative AI system — a chatbot answering questions — the Emergency response is trivial: stop the service, route users to a static page, fix the problem, restart.

For agentic AI systems, it's harder. Agents may be:

- **Mid-transaction** — halfway through a multi-step workflow with external system state changes already committed
- **Holding locks** — database transactions, file handles, API sessions that need clean release
- **Orchestrating other agents** — in multi-agent systems, one agent's shutdown cascades unpredictably
- **Operating on schedule** — triggered by timers or events, not human requests, so "stop accepting requests" doesn't stop them
- **Managing state** — accumulated context, memory, and in-progress plans that can't be cleanly discarded

This is why the Emergency layer can't just be "pull the plug." It needs to be a **structured degradation path** with pre-configured transition points.

![Agentic AI Graceful Degradation Path](../images/pace-degradation-path.svg)

### The Five Degradation Phases

| Phase | Autonomy Level | What the Agent Can Do | Human Role | Trigger to Next Phase |
|---|---|---|---|---|
| **Normal** | Full (within boundaries) | Execute actions, call tools, make decisions | Exception-only review | Anomaly detected by Judge or behavioural baseline drift |
| **Constrained** | Reduced scope | Read-only tool access, narrowed action space, tightened thresholds | Review all outputs, approve scope changes | Constraint breach, control failure, or repeated Judge flags |
| **Supervised** | Propose only | Draft actions for approval, no autonomous execution | Approve/reject every action | Integrity compromise, adversarial breach confirmed |
| **Bypassed** | Isolated | Agent quarantined, traffic routed to non-AI fallback | Operate manual/rule-based process | Non-AI fallback also compromised or insufficient |
| **Full Stop** | None | All sessions terminated, audit logs preserved | Incident response, regulatory notification | — |

**Each phase must be pre-configured, tested, and documented before the system goes into production.** You don't design your Emergency phase during the emergency.

---

## Risk Tier × PACE Action Matrix

The controls required at each PACE layer should be proportionate to risk. A Tier 1 internal summarisation tool doesn't need the same Emergency infrastructure as a Tier 3 autonomous trading agent.

### Tier 1 — Low Risk
*Internal tools, content generation, employee productivity. No customer-facing output, no regulated decisions.*

| PACE Layer | Required Controls | Trigger Criteria | Example Actions |
|---|---|---|---|
| **Primary** | Basic guardrails: topic filters, output length limits, PII detection | Always active | Block off-topic responses, redact internal identifiers |
| **Alternate** | Lightweight Judge: periodic sampling (10–20% of outputs) | Guardrail bypass rate exceeds threshold | Flag outputs that passed guardrails but violated policy; log for review |
| **Contingency** | Async human review of flagged outputs | Judge flags >5 policy violations in rolling window | Review queue for content quality team; adjust guardrail rules |
| **Emergency** | Disable AI feature; revert to manual workflow | Sustained guardrail failure or data leak detected | Feature flag off; users redirected to non-AI tool; incident logged |

**Pre-configuration requirement:** Feature flag to disable AI component. Documented manual workflow as fallback. Contact list for escalation.

---

### Tier 2 — Medium Risk
*Customer-facing content, decision support, document processing. Human reviews output before action.*

| PACE Layer | Required Controls | Trigger Criteria | Example Actions |
|---|---|---|---|
| **Primary** | Full guardrail suite: content filters, schema validation, injection detection, hallucination indicators | Always active on all inputs/outputs | Block harmful content, validate against schema, flag low-confidence outputs |
| **Alternate** | LLM-as-Judge on all outputs: policy compliance, factual grounding, tone, regulatory language | Continuous parallel evaluation | Score every output; auto-hold outputs below confidence threshold for human review |
| **Contingency** | Mandatory human-in-the-loop for all outputs below Judge confidence score | Judge confidence <0.7 or >3 flags in session | Human reviewer approves/edits/rejects before output reaches customer |
| **Emergency** | Circuit breaker: route to rule-based system or templated responses | Judge unavailable, guardrail bypass confirmed, or data integrity concern | Automated switchover to static response templates; incident response initiated |

**Pre-configuration requirement:** Rule-based fallback system tested and maintained. Human review queue with SLA. Automated circuit breaker with health checks. Customer communication template for degraded service.

---

### Tier 3 — High Risk
*Regulated decisions, autonomous agents with write access, financial/medical/legal domains. Direct business impact.*

| PACE Layer | Required Controls | Trigger Criteria | Example Actions |
|---|---|---|---|
| **Primary** | Hardened guardrails: multi-layer injection defence, regulatory constraint enforcement, action-level permissions, schema-strict output validation | Always active; no bypass path | Enforce regulatory constraints, validate every tool call against permission matrix, block any action outside defined scope |
| **Alternate** | LLM-as-Judge on all outputs AND actions: dual-model evaluation, chain-of-thought audit, action-consequence verification | Continuous parallel; all actions evaluated before and after execution | Verify action intent matches declared goal; detect goal drift; flag novel action patterns; evaluate downstream consequences |
| **Contingency** | Human oversight with domain expertise for all high-impact actions; mandatory dual-approval for irreversible actions | Any Judge flag, any novel action pattern, any action above defined impact threshold | Domain expert reviews action plan before execution; dual sign-off for financial transactions, data modifications, external communications |
| **Emergency** | Full degradation path: Constrained → Supervised → Bypassed → Full Stop (see degradation phases above) | Any confirmed control bypass, integrity compromise, or unplanned autonomous action | Immediate scope reduction; revoke write permissions; activate non-AI workflow; isolate agent; preserve forensic evidence; notify stakeholders and regulators as required |

**Pre-configuration requirement:** Complete degradation path tested quarterly. Non-AI fallback process staffed and exercised. Regulatory notification templates prepared. Forensic evidence preservation automated. Rollback procedures for all external state changes documented and tested.

---

## Designing the Emergency Layer for Agentic Systems

The Emergency layer deserves special attention because it's where most organisations have the least preparation and the most risk. For agentic AI systems, the Emergency layer must address:

### 1. Transaction Completion or Rollback

Before an agent can be stopped, in-flight transactions need to be resolved. For each tool or system the agent can access:

- **Can the action be rolled back?** If yes, rollback is part of the Emergency procedure.
- **Can the action be completed safely without the agent?** If yes, a human or rule-based system completes it.
- **Is the action stuck in an indeterminate state?** If yes, the procedure must define how to resolve it.

Document this for every tool in the agent's permission set. This is your **transaction resolution matrix**.

### 2. Multi-Agent Cascade Prevention

If Agent A shuts down while Agent B is waiting for its output:

- Agent B must have a **timeout** and **fallback behaviour** that doesn't assume Agent A will respond.
- The orchestrator must detect the missing agent and **redistribute or queue** pending work.
- No agent should be able to **indefinitely block** another agent's progress.

This is analogous to PACE's requirement that each communication method be independent of the others.

### 3. State Preservation for Post-Incident Analysis

When the Emergency layer activates:

- All agent memory, context, and in-progress plans must be **snapshotted before termination**.
- Audit logs must be **immutable** — the agent cannot modify its own logs during shutdown.
- The snapshot must include the **Judge's evaluation state** — what it flagged, when, and why.

### 4. The Non-AI Fallback Path

Every agentic AI system operating at Tier 2 or above must have a documented, tested, and maintained **non-AI fallback path** — a way to continue the business process without AI involvement.

This fallback path is not the same as "the old system." It's a deliberately designed degraded-mode process that:

- Handles the **most critical subset** of the AI system's functions
- Is **staffed** (or can be staffed within a defined timeframe)
- Has been **exercised** within the last quarter
- Does not depend on any component shared with the AI system

If you can't define this fallback path, you have a single point of failure. PACE requires you to fix that before you go to production.

---

## Trigger Points: When to Transition

One of the hardest parts of PACE planning is defining **when** to move from one layer to the next. In military communications, the trigger is usually obvious: the radio stops working. In AI security, the triggers are more nuanced.

### Recommended Trigger Criteria

| Transition | Quantitative Triggers | Qualitative Triggers |
|---|---|---|
| P → A becomes primary defence | Guardrail bypass rate >2% over rolling 1h window; >5 novel input patterns in 15 min | New attack pattern identified in threat intelligence; vendor advisory on guardrail vulnerability |
| A → C activated | Judge confidence score <0.7 on >10% of outputs; Judge latency >10s (degraded evaluation); Judge flags same violation type >3 times in session | Judge model updated with known issues; correlated alerts from multiple monitoring systems |
| C → E activated | Human review queue exceeds SLA by >2x; confirmed adversarial bypass of both guardrails AND Judge; any unplanned autonomous action at Tier 3 | Security team declares incident; regulator inquiry received; reputational risk assessment triggers escalation |

### Transition Discipline

Transitions must be:

- **Pre-defined** — documented in runbooks, not invented during incidents
- **Bi-directional** — include criteria for stepping *back up* to normal operation
- **Communicated** — all stakeholders know the current operational state
- **Logged** — every transition is an auditable event

---

## Testing Your PACE Plan

A PACE plan that hasn't been tested is a plan that won't work. Testing should validate:

| Test Type | Frequency | What It Validates |
|---|---|---|
| **Guardrail bypass testing** | Monthly | Primary layer catches known attacks; bypass rate is within tolerance |
| **Judge failure simulation** | Quarterly | System behaves correctly when Judge is unavailable or returns errors |
| **Human escalation exercise** | Quarterly | Flagged items reach reviewers within SLA; reviewers have sufficient context to decide |
| **Circuit breaker activation** | Quarterly | Non-AI fallback activates cleanly; in-flight transactions resolve; users experience acceptable degraded service |
| **Full degradation walkthrough** | Semi-annually | End-to-end transition through all five phases; recovery back to Normal validates |

For Tier 3 systems, the full degradation walkthrough should involve the same personnel who would handle a real incident, using the same tools and communication channels.

---

## Summary

The PACE overlay doesn't replace the framework's three-layer architecture. It adds the **resilience engineering** that turns three good controls into a system that degrades gracefully under failure rather than failing catastrophically.

The key additions:

1. **The Emergency layer** — making explicit what happens when all three control layers are compromised
2. **The degradation path** — structured phases from full autonomy to full stop, pre-configured for each risk tier
3. **Trigger criteria** — defined, measurable conditions for transitioning between layers
4. **The non-AI fallback** — a deliberately maintained path that doesn't depend on AI
5. **Testing** — regular validation that the plan actually works

If you're deploying AI at Tier 2 or above, and especially if you're running agentic systems with a small team, the PACE overlay is not optional. It's the difference between an incident and a crisis.

---

## Related Framework Documents

| Document | Relevance |
|---|---|
| [Controls](../core/controls.md) | The three-layer architecture that PACE overlays |
| [Agentic](../core/agentic.md) | Additional controls for autonomous agents — where graceful degradation is most critical |
| [Risk Tiers](../core/risk-tiers.md) | Classification that determines PACE requirements |
| [Incident Playbook](../extensions/templates) | Templates for Emergency layer activation |
| [Infrastructure Beats Instructions](infrastructure-beats-instructions.md) | Why the Emergency layer must be infrastructure, not prompts |

---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
