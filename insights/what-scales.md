# What Scales: Security Patterns With Viable Scaling Properties

> The previous document ([When the Pattern Breaks](when-the-pattern-breaks.md)) identified where the guardrail → judge → human pattern fails in complex multi-agent systems. This document asks the harder question: what *does* scale, and what fundamental constraints mean some things never will?

---

## The Scaling Test

A security pattern "scales" if its cost (latency, compute, human effort, operational complexity) grows **slower than the system it protects**. Specifically:

| Growth Rate | Assessment | Example |
| --- | --- | --- |
| O(1) — constant regardless of agents | **Scales** | System-wide budget caps |
| O(N) — linear with agent count | **Scales acceptably** | Per-agent identity issuance |
| O(N log N) — slightly superlinear | **Marginal** | Hierarchical trust evaluation |
| O(N²) — quadratic with agent count | **Does not scale** | Per-pair inter-agent guardrails |
| O(human) — requires human throughput | **Does not scale** | Manual edge-case review |

Most of the three-layer pattern's components are O(N²) or O(human). That's why it breaks.

---

## What Scales

### 1. System-Level Invariants — O(1)

**What:** Define constraints that must hold true for the *entire system*, regardless of how many agents exist or how they interact. Monitor those constraints, not individual agent outputs.

**Examples:**
- Total financial exposure across all agents must not exceed $X per hour.
- No agent may create, modify, or exfiltrate credentials.
- No data classified above [level] may leave [trust zone].
- Total token consumption must not exceed budget Y.

**Why it scales:** The number of invariants is a function of your *risk requirements*, not your *agent count*. Whether you have 3 agents or 300, the invariant "don't spend more than $10,000 without human approval" is one check against one aggregate counter. Adding agents doesn't add invariants.

**Where it's appearing in production:** AWS's Agentic AI Security Scoping Matrix describes this as "enforcing agency boundaries" — defining what the *system* is permitted to do, not what each agent is permitted to say. The CSA's Agentic Trust Framework implements this through a centralized Policy Decision Point (PDP) that evaluates every action against system-level rules in real-time.

**The honest limit:** Invariants only catch what you anticipated. They're the equivalent of guardrails at system level — they prevent *known-bad system states*. They don't detect novel emergent failures. You still need something else for unknown-bad.

### 2. Cryptographic Identity and Provenance — O(N)

**What:** Every agent gets a verifiable identity. Every message is signed. Every action is attributed. Every delegation chain is traceable.

**Why it scales:** Identity issuance is O(N) — one identity per agent. Verification is per-message, but message verification is computationally trivial (milliseconds). Unlike content-based guardrails that must *understand* a message to evaluate it, cryptographic verification only needs to confirm *who sent it* and *whether it was tampered with*. That operation doesn't get harder as the content gets more complex or the agent count grows.

**Where it's appearing:**
- The CSA/OWASP-aligned research proposes **Decentralized Identifiers (DIDs)** and **Verifiable Credentials (VCs)** for agent identity — cryptographic proof of an agent's capabilities, provenance, and security posture.
- An **Agent Naming Service (ANS)**, analogous to DNS for agents, enables capability-based discovery with trust verification. GoDaddy has already deployed OWASP's ANS proposal to production.
- HashiCorp Vault is emerging as infrastructure for agent secret management — dynamic, short-lived credentials issued per task, with automated rotation.
- Microsoft Foundry (Ignite 2025) treats agent identity as a first-class platform concept: admins can see every agent, its permissions, and control its access from a central management plane.

**What this solves:** Three of the OWASP Agentic Top 4 risks (ASI02, ASI03, ASI04) are identity-related. Cryptographic identity directly addresses credential delegation abuse, confused deputy attacks, and cross-agent trust exploitation. It doesn't prevent an agent from being manipulated, but it ensures you always know *which* agent acted and *what credentials it used*.

**The honest limit:** Identity tells you *who* did something. It doesn't tell you whether what they did was *correct*. A properly authenticated agent can still hallucinate, follow poisoned instructions, or produce harmful outputs. Identity is necessary infrastructure, not sufficient security.

### 3. Economic Constraints and Budget Envelopes — O(1)

**What:** Hard limits on what the system can *spend* — in tokens, API calls, financial transactions, compute time, or data volume.

**Why it scales:** A budget is an aggregate constraint. Whether 3 agents or 300 are consuming tokens, the total budget check is one comparison. Budget enforcement turns unbounded AI behavior into a bounded-cost problem. OWASP's "Unbounded Consumption" (LLM10:2025) exists precisely because most systems lack this.

**In practice:**
- Token budgets per workflow prevent infinite agent loops (a known failure mode in multi-agent orchestration).
- Financial transaction caps per time window prevent cascading unauthorized spending (relevant to the crypto theft incidents documented in the Adversa AI 2025 report).
- API rate limits per agent prevent resource exhaustion and model extraction.

**The honest limit:** Budget caps are blunt instruments. They prevent catastrophic loss but don't prevent harm within budget. An agent can cause significant damage well within a $10,000 daily budget.

### 4. Trust Zones with Controlled Boundaries — O(Z), where Z << N

**What:** Group agents into zones based on data sensitivity, action scope, and trust level. Within a zone, agents interact freely. Between zones, all communication passes through controlled boundary enforcement.

**Why it scales:** The number of trust zones (Z) is determined by your data classification and risk architecture, not your agent count. You might have 200 agents across 5 trust zones. The number of *zone-to-zone boundaries* to secure is Z² = 25, not N² = 40,000.

**This is directly analogous to network segmentation** — a pattern enterprise security teams already know. VLANs, firewalls, and zero-trust network segments work the same way: reduce the combinatorial problem by grouping entities and controlling boundaries.

**Where it's appearing:** OWASP's Agentic Top 10 explicitly recommends "defining trust zones and isolation boundaries, with constrained scopes and tightly controlled inter-zone comms." AWS's Scoping Matrix maps four architectural scopes (no agency → prescribed → supervised → full) which function as trust tiers with different boundary enforcement requirements.

**The honest limit:** Trust zones are only as good as your classification. If you put the wrong agent in the wrong zone, or if the boundaries are misconfigured, you have network segmentation without proper firewall rules — a false sense of security. Also, zone boundaries add latency. Complex multi-agent workflows that cross multiple zones will be slower.

### 5. Circuit Breakers and Kill Switches — O(1) per zone

**What:** Automated mechanisms that halt agent execution when system-level invariants are violated, anomalous behavior is detected, or operational thresholds are exceeded.

**Why it scales:** A circuit breaker monitors an aggregate signal (error rate, anomaly score, budget consumption) and makes a binary decision: continue or halt. This is one check regardless of agent count. OWASP calls these "a non-negotiable, auditable, and physically isolated mechanism."

**The critical implementation detail:** Kill switches must be *outside* the agent control plane. An agent that can disable its own kill switch isn't actually controlled. The mechanism must be physically or architecturally isolated — a separate service with its own authentication and no dependency on the agents it controls.

**The honest limit:** Kill switches are the nuclear option. They prevent catastrophic harm but they also halt legitimate work. High false-positive rates in anomaly detection can cause kill switches to fire constantly, creating a denial-of-service against your own operations. Tuning the sensitivity is the operational challenge.

### 6. Progressive Trust (Maturity-Based Autonomy) — O(N) amortised

**What:** Agents start with minimal autonomy and earn expanded permissions through demonstrated reliable behavior over time.

**The CSA's Agentic Trust Framework** codifies this with four maturity levels:

| Level | Name | Capabilities | Oversight |
| --- | --- | --- | --- |
| 1 | Intern | Read-only. Can analyse, cannot act. | Full logging, no approval needed for reads |
| 2 | Junior | Can recommend actions. Requires human approval before execution. | Human-in-the-loop for all actions |
| 3 | Senior | Can execute within defined scope. Flagged for review on exceptions. | Monitoring + exception review |
| 4 | Principal | Autonomous within broad scope. Periodic audit. | Audit-based, not per-action |

**Why it scales:** The progressive model reduces monitoring overhead for trusted agents over time. New and untrusted agents get heavy oversight (expensive but bounded to a small number of new agents). Established agents with proven behavioral records get lighter oversight (cheap, applied to the majority). The *steady-state* monitoring cost converges to the audit-based model, which is O(1) per audit cycle regardless of agent count.

**Promotion criteria** include minimum time at level, performance thresholds, security validation, and governance sign-off. This prevents gaming — an agent can't fast-track itself to full autonomy.

**The honest limit:** This assumes agent behavior is *stable* — that an agent that performed well last week will perform well next week. In practice, model updates, tool changes, data drift, and adversarial manipulation can change agent behavior between audit cycles. A "Principal" agent that gets a poisoned memory injection has earned trust that it no longer deserves. The model needs continuous anomaly detection as a safety net behind the maturity levels.

### 7. Append-Only Audit Logs — O(N) with volume, but cheap

**What:** Immutable, tamper-evident logs of every agent action, tool call, credential use, and inter-agent communication.

**Why it scales:** Storage is cheap. Append-only writes are fast. The cost is proportional to message volume, but the *per-message* cost is negligible. What matters is that the logs exist and are queryable — the analysis can happen async, at any cadence.

**Why this is more important than it looks:** Audit logs transform security from *prevention* to *detection and accountability*. In a system where you cannot prevent all failures (which is every AI system at scale), the ability to *reconstruct what happened, which agent was responsible, and what credentials were used* is the foundation for incident response, forensics, and continuous improvement.

**The honest limit:** Logs only help after the fact. They are essential for accountability but they don't prevent harm in real-time. And log volume at scale (potentially millions of agent messages per day) requires investment in log management, search, and analysis infrastructure.

---

## What Definitively Does Not Scale

For completeness and honesty:

**Per-agent content guardrails in multi-agent communication** — O(N²). Each agent pair needs bidirectional inspection. Impractical beyond ~10 agents.

**LLM-as-Judge evaluating every agent output** — O(N) in cost, but each evaluation adds 500ms-5s latency *per agent hop*. In a 10-agent pipeline, you've added 5-50 seconds. Also, each judge evaluation costs tokens — at scale, the cost of judging exceeds the cost of the agents doing the work.

**Human review of edge cases** — O(human). Human throughput is fixed at roughly 50-200 decisions per hour for complex cases. A system generating 10,000 flagged interactions per hour requires 50-200 human reviewers. This is a staffing problem that grows linearly with system throughput and doesn't compress.

**Static RBAC for agent permissions** — designed for hundreds of known roles, not thousands of ephemeral, dynamically-created agents with varying scopes. Breaks at scale both operationally (role explosion) and conceptually (agents don't have stable roles).

---

## The Emerging Architecture

The patterns that scale point toward a recognisable architecture. It's not the guardrail → judge → human pattern. It's closer to **how we already secure distributed systems at scale**:

[![The Emerging Scalable Architecture](../images/scalable-architecture-stack.svg)](../images/scalable-architecture-stack.svg)

This is not radically new. It's **zero-trust network architecture applied to AI agents**. The CSA, OWASP, and AWS are converging on this independently. Enterprise security teams already have the conceptual vocabulary: trust zones are VLANs, the PDP is a next-gen firewall, cryptographic identity is PKI, circuit breakers are IDS/IPS, audit logs are SIEM.

The difference is that the *entities* being governed are probabilistic, natural-language-driven, and capable of independent reasoning — which means the boundary enforcement must be richer than IP/port rules. But the *architecture* is familiar.

---

## What Remains Unsolved

Three problems have no production-proven solution yet:

**1. Emergent multi-agent behavior detection.**

System-level invariants catch *anticipated* bad states. But the defining property of complex multi-agent systems is that they produce behaviors no individual agent was designed to produce and no invariant was written to catch. Detecting emergent harmful behavior requires understanding the *collective* system state, not just individual agent outputs. This is an open research problem. It's analogous to detecting emergent failures in complex distributed systems, and distributed systems engineering has been working on that for decades without a general solution.

**2. Cross-organisational agent federation.**

When your agents interact with agents from other organisations (via MCP, A2A protocol, or direct API), your trust zones end at your organisational boundary. You cannot enforce invariants on systems you don't control. No production-proven security pattern exists for federated multi-agent systems. The proposals (mutual attestation, zero-knowledge proofs of compliance, reputation systems) are theoretically sound and practically untested.

**3. Securing against goal hijack in multi-turn, multi-agent conversations.**

OWASP's ASI01 (Agent Goal Hijack) is the agentic equivalent of prompt injection — but harder, because the attack surface is every message from every other agent, and the hijack can be subtle and multi-step. Palo Alto's "Agent Session Smuggling" demonstration showed that a malicious agent can adapt its strategy and build false trust over multiple interactions. No guardrail or invariant catches a gradually shifting goal.

---

## Practical Implications for the Framework

For **systems this framework already covers** (single-agent, Stage 1-2): the three-layer pattern works. Keep using it.

For **multi-agent orchestration** (Stage 3, 3-10 agents): augment the three-layer pattern with system-level invariants, trust zones, and cryptographic identity. The framework's existing [Agentic controls](../core/agentic.md) provide a starting point; the architectural patterns above extend them.

For **autonomous multi-agent systems** (Stage 4+): the three-layer pattern is foundation, not solution. The scaling architecture described here is the emerging direction. Implement it incrementally:

1. Start with audit logs and cryptographic identity (cheapest, most immediately useful).
2. Add system-level invariants and budget enforcement (prevents catastrophic outcomes).
3. Implement trust zones (reduces the combinatorial problem).
4. Deploy circuit breakers (provides emergency containment).
5. Adopt progressive trust (reduces steady-state monitoring cost).
6. Accept that emergent behavior detection and cross-org federation are unsolved. Design for *containment* of failures you cannot *prevent*.

---

## The Uncomfortable Truth

No security architecture for complex multi-agent AI systems is proven at production scale yet. The patterns described here have strong theoretical foundations and are backed by converging industry consensus (OWASP, AWS, CSA, Microsoft, HashiCorp). But "converging consensus" is not "battle-tested." The gap between what we think will work and what actually works in production will only close through deployment experience and, inevitably, through failures that expose what we missed.

The honest position: we know enough to start building. We don't know enough to promise it works.

> "It is not possible to manage what you do not understand. But understanding is not the same as control." 

---

## Sources

| Source | Contribution |
| --- | --- |
| [CSA: Agentic Trust Framework (Feb 2026)](https://cloudsecurityalliance.org/blog/2026/02/02/the-agentic-trust-framework-zero-trust-governance-for-ai-agents) | Progressive trust maturity model, PDP architecture, OWASP alignment |
| [CSA: Fortifying the Agentic Web (Sep 2025)](https://cloudsecurityalliance.org/blog/2025/09/12/fortifying-the-agentic-web-a-unified-zero-trust-architecture-against-logic-layer-threats) | Trust Fabric, DID/VC-based identity, Trust-Adaptive Runtime Environments |
| [AWS: Agentic AI Security Scoping Matrix (Nov 2025)](https://aws.amazon.com/blogs/security/the-agentic-ai-security-scoping-matrix-a-framework-for-securing-autonomous-ai-systems/) | Four-scope model, progressive security controls |
| [Microsoft: Zero-Trust Agent Architecture (Nov 2025)](https://techcommunity.microsoft.com/blog/educatordeveloperblog/zero-trust-agent-architecture-how-to-actually-secure-your-agents/4473995) | Foundry Agent Service, centralised identity management, Prompt Shields at gateway |
| [HashiCorp: Zero Trust for Agentic Systems (re:Invent 2025)](https://www.hashicorp.com/blog/zero-trust-for-agentic-systems-managing-non-human-identities-at-scale) | Dynamic secret management, NHI lifecycle, Vault as agent credential infrastructure |
| [Narajala et al.: Zero-Trust Identity Framework for Agentic AI (May 2025)](https://arxiv.org/abs/2505.19301) | ANS, DID/VC for agents, ZKP for privacy-preserving compliance |
| [OWASP Top 10 for Agentic Applications (Dec 2025)](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/) | Definitive risk taxonomy, least agency principle |
| [KPMG AI Pulse Q4 2025](https://kpmg.com/us/en/media/news/q4-ai-pulse.html) | 80% cite cybersecurity as top barrier; 65% cite system complexity |
| [Obsidian Security: AI Agent Landscape (Jan 2026)](https://www.obsidiansecurity.com/blog/ai-agent-market-landscape) | 90% agents over-permissioned; 16x data movement vs humans |

---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
