# Multi-Agent Controls

> Extends [Agentic Controls](agentic.md) for systems where agents interact with other agents.
>
> *This document uses the simplified three-tier system (Tier 1/2/3). See [Risk Tiers — Simplified Tier Mapping](risk-tiers.md#simplified-tier-mapping) for the mapping to LOW/MEDIUM/HIGH/CRITICAL.*

## The Problem

Single-agent systems have a clear accountability chain: User → Agent → Tools → Output.

Multi-agent systems break this. When Agent A delegates to Agent B, which calls Agent C:

- **Who is accountable for the final output?**
- **Can Agent B do things Agent A can't?**
- **Does Agent C know who originally requested the action?**
- **If the chain produces a harmful outcome, where did it go wrong?**

These are not edge cases. Every framework that supports agent-to-agent communication (CrewAI, AutoGen, LangGraph, custom MCP chains) creates these scenarios by default.

---

## Trust Topologies

Multi-agent systems fall into three patterns. Each has different control requirements.

| Topology | Description | Risk Profile |
|----------|-------------|-------------|
| **Orchestrator** | One agent coordinates, others execute | Moderate — single point of control and failure |
| **Peer-to-peer** | Agents communicate directly | High — no central authority, lateral movement risk |
| **Hierarchical** | Agents delegate down a chain | High — privilege can accumulate or escalate across levels |

---

## Controls

### 1. Delegation Policy

Every agent-to-agent request must be governed by an explicit delegation policy.

| Rule | Implementation |
|------|---------------|
| **No privilege escalation** | Agent B cannot perform actions that Agent A's principal is not authorised for |
| **Scope inheritance** | Delegated tasks inherit the scope (and constraints) of the requesting agent |
| **Delegation depth limits** | Maximum chain length before requiring human approval |
| **Allowlisted delegation pairs** | Explicitly define which agents can call which agents |

```yaml
# Example delegation policy
delegation:
  max_depth: 3
  require_human_approval_at_depth: 2
  allowed_pairs:
    - from: research-agent
      to: search-agent
      scopes: [web_search, document_retrieval]
    - from: research-agent
      to: summarisation-agent
      scopes: [text_generation]
  denied_pairs:
    - from: research-agent
      to: payment-agent  # research agent should never trigger payments
```

### 2. Identity Propagation

Every request in a multi-agent chain must carry the originating identity.

| Requirement | Why |
|-------------|-----|
| **Principal identity** | The human (or system) that initiated the chain |
| **Agent chain** | Ordered list of agents that have handled the request |
| **Scope at each hop** | What permissions were available at each step |
| **Timestamp at each hop** | When each agent acted |

This is the equivalent of HTTP request tracing (e.g., OpenTelemetry spans) applied to agent interactions. Without it, you cannot audit or attribute outcomes.

```json
{
  "trace_id": "abc-123",
  "principal": "user:jgill@example.com",
  "chain": [
    { "agent": "orchestrator", "scope": ["read", "write"], "timestamp": "2026-02-11T10:00:00Z" },
    { "agent": "research-agent", "scope": ["read"], "timestamp": "2026-02-11T10:00:01Z" },
    { "agent": "search-agent", "scope": ["web_search"], "timestamp": "2026-02-11T10:00:02Z" }
  ]
}
```

### 3. Inter-Agent Guardrails

Each agent in a chain should apply its own guardrails to incoming requests — not trust the upstream agent's validation.

| Position | Guardrail Responsibility |
|----------|------------------------|
| **Receiving agent** | Validate that the request is within its declared scope |
| **Receiving agent** | Verify the delegation policy allows this interaction |
| **Receiving agent** | Apply its own input guardrails to the content, not just the metadata |
| **Sending agent** | Apply output guardrails before forwarding results downstream |

Zero trust applies to agents exactly as it applies to microservices. Trust nothing. Verify everything.

### 4. Circuit Breakers

Multi-agent chains can loop, cascade, or amplify errors. Circuit breakers prevent runaway behaviour.

| Trigger | Action |
|---------|--------|
| **Token budget exceeded** | Halt chain, return partial result with explanation |
| **Delegation depth exceeded** | Halt chain, escalate to human |
| **Error rate threshold** | Disable agent-to-agent path, fall back to simpler flow |
| **Latency threshold** | Timeout and escalate rather than waiting indefinitely |
| **Repeated identical requests** | Detect loops, break them |

### 5. Outcome Attribution

When a multi-agent chain produces an output, you must be able to attribute each component of the output to the agent that generated it.

This is not optional for regulated environments. If an AI system makes a credit decision, the regulator will ask: "Which component made this determination and on what basis?"

| Requirement | Implementation |
|-------------|---------------|
| **Per-agent output logging** | Each agent logs its input, reasoning, and output |
| **Contribution tagging** | Final output is annotated with which agent contributed which parts |
| **Decision audit trail** | For consequential decisions, the full chain is reconstructable |

---

## Protocol-Level Risks

### MCP (Model Context Protocol)

MCP enables agents to use tools, including other agents exposed as tools. Risks:

- **Tool impersonation** — Malicious MCP server posing as a legitimate tool
- **Excessive tool access** — Agent given access to more MCP tools than needed
- **No built-in authentication** — MCP does not natively verify tool identity

**Controls:** Pin MCP server URIs, verify server identity, scope tool access per agent, log all MCP calls.

### A2A (Agent-to-Agent Protocol)

Google's A2A protocol enables cross-vendor agent communication. Risks:

- **Trust boundary collapse** — External agent from another organisation gains access to internal tools
- **Schema injection** — Malformed agent cards that manipulate receiving agents
- **Capability advertisement spoofing** — Agent claims capabilities it doesn't have (or shouldn't use)

**Controls:** Validate agent cards against an allowlist, enforce capability constraints at the receiving end, treat all A2A inputs as untrusted.

---

## Risk Tier Adjustment

Multi-agent systems should be classified at least one risk tier higher than the equivalent single-agent system performing the same task.

| Single-Agent Tier | Multi-Agent Equivalent |
|-------------------|----------------------|
| Tier 1 (Low) | Tier 2 (Medium) minimum |
| Tier 2 (Medium) | Tier 3 (High) minimum |
| Tier 3 (High) | Tier 3 + enhanced controls |

The rationale: every additional agent in a chain is an additional point of failure, an additional attack surface, and an additional accountability gap.
---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
