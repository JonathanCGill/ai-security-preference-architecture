# When Agents Talk to Agents

*Multi-agent systems create accountability gaps that require careful governance*

---

One agent is hard enough to secure. Multiple agents — collaborating, delegating, negotiating — compound the problem in ways that single-agent frameworks don't address.

Multi-agent architectures are arriving in production. The security model hasn't caught up.

---

## The Coordination Problem

A single agent has a clear accountability path: it receives input, takes actions, produces output. You can trace cause to effect.

Multi-agent systems break this:

**Agent A** receives a user request.
**Agent A** delegates part of the task to **Agent B**.
**Agent B** queries **Agent C** for information.
**Agent C** returns data that contains an embedded instruction.
**Agent B** acts on the instruction.
**Agent A** incorporates the result without knowing what happened.
**User** receives a compromised output.

Where did the failure occur? Who's accountable? What control should have caught it?

The answer is unclear because the architecture diffuses responsibility.

---

## New Attack Surfaces

### Agent-to-agent injection

When agents communicate, their messages become input. If Agent B trusts Agent A's output, and an attacker can influence Agent A, they can inject through the agent chain.

This is prompt injection at scale — not user-to-agent, but agent-to-agent.

### Emergent behaviour

Individual agents may behave correctly in isolation. Together, they exhibit behaviour nobody designed.

Agent A optimises for speed. Agent B optimises for thoroughness. Together, they oscillate — A rushing, B slowing, A overriding, B resisting. The system fails not because either agent is broken but because their interaction is pathological.

Emergent behaviour is hard to test for and hard to predict.

### Cascading failures

Agent A fails. Agent B, waiting for Agent A, times out. Agent C, depending on both, enters an error state. Agent D, orchestrating all of them, retries — triggering a cascade that amplifies the original failure.

Single-agent failure modes are contained. Multi-agent failure modes can propagate.

### Responsibility diffusion

When something goes wrong, each agent can point at another. "I just followed the instruction from Agent B." "I just returned the data Agent C requested." "I just aggregated what everyone gave me."

No single agent did anything wrong. The system produced harm anyway.

---

## What the Framework Covers (Partially)

The existing framework addresses single agents:

| Control | Single Agent | Multi-Agent Gap |
|---------|--------------|-----------------|
| Scope enforcement | Agent stays in its lane | Agents may expand scope through delegation |
| Action validation | Actions checked before execution | Who validates when Agent B acts on Agent A's request? |
| Tool output sanitisation | External data treated as untrusted | Is Agent A's output "external"? |
| Circuit breakers | Stop runaway execution | Distributed execution harder to stop |
| Human approval | Human approves high-impact actions | Approval for each agent? For the orchestrator? For the final action only? |

The principles apply. The implementation is unclear.

---

## Framework Extensions Needed

### Agent identity and trust

Each agent needs identity. Agent A knows it's receiving a message from Agent B, not a spoofed message claiming to be from Agent B.

Trust must be explicit:
- What can Agent A ask Agent B to do?
- What data can Agent B share with Agent A?
- Can Agent A delegate to agents it hasn't been authorised to use?

Implicit trust ("we're all in the same system") is a vulnerability.

### End-to-end accountability

Even if individual agents pass inspection, the system needs end-to-end accountability:

- Who requested the original task? (Traceable through the entire chain)
- What was the final outcome? (Attributable to the originating request)
- Which agents contributed? (Logged and auditable)
- Where did policy violations occur? (Identifiable despite diffusion)

The orchestrator — or a supervisory layer — needs visibility into the whole chain, not just its direct reports.

### Distributed guardrails

Guardrails at the edge (user input, final output) aren't enough. You need validation at agent boundaries:

- Agent A → Orchestrator → Guardrail check → Agent B
- Agent B → Guardrail check → External tool
- Agent C → Guardrail check → Agent A

Every boundary is a potential injection point. Every boundary needs inspection.

This is expensive. It may also be necessary.

### System-level Judge

A Judge evaluating individual agent interactions misses system-level issues. You need a Judge that can:

- Review the full multi-agent conversation
- Identify coordination failures
- Detect emergent behaviour patterns
- Assess whether the system outcome matches the user intent

This is harder than single-agent evaluation. The Judge needs to understand agent roles, expected interactions, and acceptable deviations.

---

## Practical Guidance

**If you're deploying multi-agent systems:**

1. Start simple — minimise agent count until you understand the dynamics
2. Treat agent-to-agent messages as untrusted input
3. Log the full conversation across all agents
4. Implement circuit breakers at the orchestrator level
5. Require human approval for system-level outcomes, not just individual actions
6. Design for graceful degradation when agents fail

**If you're building framework:**

1. Extend agent controls to cover agent-to-agent communication
2. Add orchestrator-level controls and accountability
3. Define trust relationships between agents explicitly
4. Build Judge capability for multi-turn, multi-agent evaluation
5. Plan for emergent behaviour — monitoring, anomaly detection, kill switches

---

## The Trajectory

Multi-agent architectures will become common. They solve problems single agents can't — complex tasks, specialised knowledge, parallel execution.

The security and governance models will lag. We're building single-agent controls for multi-agent systems.

Expect failures. Design for them. Learn from them.

---

*From the [Enterprise AI Security Framework](https://github.com/JonathanCGill/enterprise-ai-security-controls-framework) — operational controls for enterprise AI systems.*
