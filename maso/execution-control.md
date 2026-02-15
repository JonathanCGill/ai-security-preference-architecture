# MASO Control Domain: Execution Control

> Part of the [MASO Framework](../README.md) · Control Specifications
> Covers: ASI02 (Tool Misuse) · ASI05 (Unexpected Code Execution) · ASI08 (Cascading Failures) · LLM05 (Improper Output Handling)

---

## Principle

Every agent action is bounded: bounded by permission, bounded by impact, bounded by time. No single agent can cause unlimited damage. When an agent fails, the failure is contained to that agent. When errors cascade, automated circuit breakers engage before human response is required.

Execution control is where the PACE resilience methodology meets real-time operations. The controls in this domain define the triggers that move the system from Primary to Alternate and beyond.

---

## Why This Matters in Multi-Agent Systems

**Tool misuse compounds across agents.** In a single-model system, a tool misuse event is contained to one context. In a multi-agent system, Agent A's misuse of Tool X produces output that becomes Agent B's input for Tool Y. The damage from chained tool misuse can far exceed what any single agent could accomplish alone.

**Code execution pathways multiply.** When agents generate and execute code, each agent is a potential entry point for code injection. If Agent A generates code that Agent B executes in its sandbox, the security boundary depends on both the generation controls (Agent A) and the execution controls (Agent B). A weakness in either is exploitable.

**Cascading failures are the default, not the exception.** Multi-agent systems are tightly coupled by design — agents depend on each other's outputs. A hallucination in one agent becomes a flawed plan in the next, becomes a destructive action in the third. Without explicit isolation, errors propagate at the speed of the orchestration.

**Runaway loops consume resources exponentially.** Two agents triggering each other in a cycle can generate exponential resource consumption. The loop may look like productive work to a naive monitor — each agent is calling tools, producing outputs, and delegating tasks — but the system is burning tokens and compute on a recursive dead end.

---

## Controls by Tier

### Tier 1 — Supervised

| Control | Requirement | Implementation Notes |
|---------|-------------|---------------------|
| **EC-1.1** Human approval gate | Every write operation, external API call, and state-modifying action requires human approval | System presents proposed action (tool, parameters, target) and waits for confirmation. |
| **EC-1.2** Tool allow-lists | Each agent has a defined list of permitted tools; unlisted tools are blocked | Enforced at the guardrails layer. |
| **EC-1.3** Per-agent rate limits | Maximum actions per time window per agent | Prevents runaway loops before human review catches them. Recommended: 100 calls/hr. |
| **EC-1.4** Read auto-approval | Read operations within scoped permissions proceed without human approval | Establishes the efficiency baseline that Tier 2 will extend. |

### Tier 2 — Managed

All Tier 1 controls remain active, plus:

| Control | Requirement | Implementation Notes |
|---------|-------------|---------------------|
| **EC-2.1** Action classification | Every action classified as auto-approve, escalate, or block | Classification considers: action type, target system, data classification, agent history, LLM-as-Judge evaluation. |
| **EC-2.2** Sandboxed execution | Agents generating/executing code operate in isolated environments | Per-agent sandbox with defined filesystem, network, and process boundaries. Sandbox destroyed and recreated after each execution. |
| **EC-2.3** Blast radius caps | Maximum impact scope per agent | Max records modifiable, max financial value, max external API calls. Exceeding any cap triggers PACE escalation. |
| **EC-2.4** Circuit breakers | Agent paused on error threshold | E.g., 3 guardrail blocks in 10 minutes triggers circuit breaker. Agent is paused, event is logged, monitoring evaluates next step. |
| **EC-2.5** LLM-as-Judge gate | Evaluation model reviews all agent outputs before external commit | Evaluates: factual accuracy, policy compliance, goal integrity, output safety, data leakage. |

### Tier 3 — Autonomous

All Tier 2 controls remain active, plus:

| Control | Requirement | Implementation Notes |
|---------|-------------|---------------------|
| **EC-3.1** Infrastructure-enforced blast radius | Caps enforced at platform level, not by agent or orchestrator | Agent cannot override its own blast radius through any mechanism. Analogous to OS-level resource limits. |
| **EC-3.2** Self-healing circuit breakers | Circuit breaker triggers PACE P→A automatically; backup agent activates; system returns to P on stabilisation | Self-healing cycle limit: max 3 in 24 hours before auto-escalation to Contingency. |
| **EC-3.3** Multi-model cross-validation | High-consequence actions validated by LLM-as-Judge AND an independent second model | Disagreement between validators triggers human escalation. |
| **EC-3.4** Time-boxing | Every autonomous task has a maximum execution time | Task not completed within time box → agent paused, state captured, task escalated. Prevents indefinite autonomous operation on drifted tasks. |

---

## Action Classification Rules (Tier 2+)

The action classification engine is the core mechanism that replaces per-action human approval with risk-proportionate automation. Rules should be defined collaboratively between the AI security team and the business function that owns the agent system.

**Auto-approve (no human involvement):**

- Read operations within the agent's scoped permissions
- Write operations to internal staging areas (reversible, low-consequence)
- Tool invocations within pre-approved parameter ranges
- Actions that the LLM-as-Judge approves and the blast radius cap is not at risk

**Escalate (human approval required):**

- Write operations to production systems
- Actions involving external parties (email sends, API calls to third-party services)
- Irreversible operations (data deletion, financial transactions)
- Actions flagged by the LLM-as-Judge for any reason
- Actions where the blast radius cap would exceed 50% of the defined maximum
- First-time use of a tool by an agent (no baseline data)

**Block (automatic denial):**

- Actions outside the agent's tool allow-list
- Actions that violate the guardrails layer
- Actions targeting systems not in the agent's scope
- Actions during a PACE Alternate or Contingency phase that exceed the phase-specific restrictions

---

## Testing Criteria

### Tier 1 Tests

| Test ID | Test | Pass Criteria |
|---------|------|---------------|
| EC-T1.1 | Approval gate | Submit a write operation. Confirm it blocks until human approval. Reject the approval and confirm the action is prevented. |
| EC-T1.2 | Tool scope | Attempt to invoke a tool not on the agent's allow-list. Guardrail blocks it. |
| EC-T1.3 | Rate limit | Submit actions exceeding the configured rate. Throttling engages. |
| EC-T1.4 | Read auto-approval | Submit a read operation within scope. Confirm it executes without human approval. |

### Tier 2 Tests

| Test ID | Test | Pass Criteria |
|---------|------|---------------|
| EC-T2.1 | Action classification | Submit reads, low-consequence writes, high-consequence writes, and out-of-scope actions. Each is classified correctly. |
| EC-T2.2 | Sandbox isolation | From within an agent's sandbox, attempt to access the host filesystem, network outside allowed ranges, and other agent processes. All attempts blocked. |
| EC-T2.3 | Blast radius cap | Attempt to exceed a defined blast radius cap. System blocks the excess and triggers PACE escalation. |
| EC-T2.4 | Circuit breaker | Trigger the error threshold. Agent is paused within 30 seconds. Event is logged. |
| EC-T2.5 | LLM-as-Judge detection | Submit known-bad outputs (policy violations, goal drift, data leakage). Measure judge detection rate. Target: > 95% for HIGH severity, > 80% for MEDIUM. |

### Tier 3 Tests

| Test ID | Test | Pass Criteria |
|---------|------|---------------|
| EC-T3.1 | Infrastructure blast radius | From within the agent's execution environment, attempt every known technique to exceed the blast radius. Infrastructure blocks all attempts. |
| EC-T3.2 | Self-healing cycle | Inject anomalous behaviour. Confirm P→A transition, backup activation, and automatic A→P return on stabilisation. |
| EC-T3.3 | Self-healing limit | Trigger the self-healing cycle limit (3 in 24hr). Confirm automatic escalation to Contingency. |
| EC-T3.4 | Cross-validation disagreement | Submit an action where one validator approves and the other rejects. Confirm human escalation. |
| EC-T3.5 | Time-box expiry | Start a task with a tight time box. Let it expire. Confirm pause, state capture, and escalation. |

---

## Maturity Indicators

| Level | Indicator |
|-------|-----------|
| **Initial** | Agents can invoke any available tool. No rate limits. No blast radius caps. Human reviews outputs manually with no systematic process. |
| **Managed** | Tool allow-lists defined. Human approval gate for all writes. Rate limits configured. Actions logged with approval status. |
| **Defined** | Action classification engine operational. Sandboxed execution. Blast radius caps. Circuit breakers. LLM-as-Judge gate. |
| **Quantitatively Managed** | Classification accuracy measured. Judge false positive/negative rates tracked and reported. Circuit breaker engagement frequency monitored. Blast radius cap utilisation tracked per agent. |
| **Optimising** | Infrastructure-enforced blast radius. Self-healing P↔A cycles. Multi-model cross-validation. Time-boxing. Action classification rules tuned based on operational data. |

---

## Common Pitfalls

**Blast radius caps that are too generous.** A cap of "10,000 records per hour" for an agent that normally modifies 50 records per hour is not a cap — it's a ceiling so high it provides no protection. Caps should be set at 2–3x the expected peak volume, not at theoretical maximums.

**Circuit breakers that only count errors.** An agent that never triggers guardrails but produces subtly incorrect output is more dangerous than one that fails loudly. Circuit breakers should include quality metrics (LLM-as-Judge scores) not just error counts.

**Sandboxes with network access.** A sandbox that isolates the filesystem but allows unrestricted network access is not a sandbox — it's a launchpad. Network scope should be limited to the specific endpoints the agent's tools require.

**Conflating the LLM-as-Judge with the task agent.** The judge must be independent — a different model, ideally from a different provider, with no access to the task agent's system prompt or configuration. If the judge uses the same model as the task agent, they share the same blindspots.

---

*Previous: [Data Protection](data-protection.md) · Back to: [MASO Framework](../README.md) · Next: [Observability](observability.md)*
