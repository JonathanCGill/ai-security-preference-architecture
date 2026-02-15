# Multi-Agent Security Operations (MASO) Framework

**A PACE-Driven Approach to Securing Multi-Model Agent Orchestration**

> *Part of the [Enterprise AI Security Controls Framework](https://github.com/JonathanCGill/enterprise-ai-security-controls-framework)*
> Version 1.0 · February 2026 · Jonathan Gill

---

## Purpose

Multi-model AI agent systems — where multiple LLMs from different providers collaborate, delegate, and execute tasks autonomously — represent a fundamentally different risk surface than single-model deployments. The speed of agent-to-agent interaction, the delegation of authority across trust boundaries, and the compounding effect of errors across agent chains demand a security model that goes beyond what the parent framework's single-model controls address.

This framework extends the [Enterprise AI Security Controls Framework](https://github.com/JonathanCGill/enterprise-ai-security-controls-framework) into the multi-agent domain. It applies the **PACE resilience methodology** to agent orchestration, maps controls against both the **OWASP Top 10 for LLM Applications (2025)** and the **OWASP Top 10 for Agentic Applications (2026)**, and grounds every control recommendation in real-world threat intelligence and emerging attack patterns.

It is designed for enterprise security architects, AI platform engineers, and governance teams who are deploying or evaluating multi-agent systems in regulated environments.

---

## Architecture Overview

![MASO Architecture](images/maso-architecture.svg)

The MASO framework operates on a **three-layer defence model** inherited from the parent framework, adapted for multi-agent dynamics:

**Layer 1 — Guardrails** enforce hard boundaries: input validation, output sanitisation, tool permission scoping, and rate limiting. These are deterministic, non-negotiable controls that operate at machine speed.

**Layer 2 — LLM-as-Judge Evaluation** uses a dedicated evaluation model (distinct from the task agents) to assess the quality, safety, and policy compliance of agent actions and outputs before they are committed. In multi-agent systems, this layer also evaluates inter-agent communications for goal integrity and instruction injection.

**Layer 3 — Human Oversight** provides the governance backstop. The scope of human involvement scales inversely with the system's demonstrated trustworthiness and directly with the consequence severity of agent actions. Write operations, external API calls, and irreversible actions escalate to this layer based on risk classification.

The critical addition for multi-agent systems is the **Secure Inter-Agent Message Bus** — a validated, signed, rate-limited communication channel through which all agent-to-agent interaction must pass. No direct agent-to-agent communication is permitted outside this bus.

---

## PACE Resilience for Multi-Agent Operations

![PACE Multi-Agent Cycle](images/pace-multi-agent-cycle.svg)

The [PACE methodology](https://github.com/JonathanCGill/enterprise-ai-security-controls-framework) (Primary, Alternate, Contingency, Emergency) from the parent framework is extended to handle the unique failure modes of multi-agent orchestration.

### Primary — Normal Operations

All agents are active and operating within their designated roles. The full three-layer security stack is operational. Inter-agent communication flows through the signed message bus. Behavioural baselines are being actively monitored, and tool permissions are at standard scoped levels.

Key controls active: Goal integrity monitoring, tool argument validation, output cross-checking between agents, cost and rate monitoring per agent.

### Alternate — Agent Failover

Triggered when a single agent shows anomalous behaviour: unexpected tool usage patterns, goal drift, output quality degradation, or communication anomalies detected by the monitoring agent or LLM-as-Judge layer.

Response: The anomalous agent is isolated. A pre-configured backup agent (potentially from a different model provider) is activated to assume the role. Tool permissions for the replacement agent are tightened to read-only until the agent demonstrates baseline-normal behaviour. All write operations across the system require human-in-the-loop approval during the alternate phase.

Transition authority: Automated (the monitoring agent or orchestrator can initiate P→A without human approval, but must notify the security team).

### Contingency — Degraded Mode

Triggered when multiple agents are compromised, the message bus integrity is questioned, or the alternate agent also exhibits anomalous behaviour.

Response: Multi-agent orchestration is suspended. A single pre-validated agent operates in fully supervised mode — every action requires human pre-approval. The inter-agent communication channel is disabled. All agent state and context is captured for forensic analysis. The system operates at reduced capacity but with high assurance.

Transition authority: Security team or designated AI security officer.

### Emergency — Full Shutdown

Triggered by cascading failures, confirmed data exfiltration, evidence of coordinated agent manipulation, or rogue agent behaviour (self-directed action outside defined objectives).

Response: All agents are terminated immediately. Tool access is revoked across all scopes. Memory and context snapshots are preserved in immutable storage. The incident response team is engaged. A full rollback of agent actions is initiated where possible (this requires the action audit chain from the observability layer).

Transition authority: CISO or incident commander.

**Recovery (E→P):** Requires a post-incident review confirming root cause identification, control remediation, and updated behavioural baselines before returning to Primary operations.

---

## OWASP Risk Coverage

![OWASP Dual Mapping](images/owasp-dual-mapping.svg)

The MASO framework maps controls against both OWASP threat taxonomies relevant to multi-agent systems.

### OWASP Top 10 for LLM Applications (2025)

These risks apply to each individual agent within the multi-agent system. The key difference in a multi-agent context is that each risk can compound across agents.

| Risk | Multi-Agent Amplification | MASO Control Domain |
|------|--------------------------|-------------------|
| **LLM01: Prompt Injection** | An injection in one agent's context can propagate to other agents through inter-agent messages. A poisoned document processed by an analyst agent can become instructions to an executor agent. | Input guardrails per agent · Message bus validation · Goal integrity monitor |
| **LLM02: Sensitive Information Disclosure** | Data shared between agents across trust boundaries. Agent A may have access to data that Agent B should not see, but delegation creates implicit data flows. | Cross-agent data fencing · Output DLP at message bus · Per-agent data classification |
| **LLM03: Supply Chain Vulnerabilities** | Multiple model providers, MCP servers, tool integrations, and plugin ecosystems multiply the supply chain attack surface. | AIBOM per agent · Signed tool manifests · MCP server vetting · Runtime component audit |
| **LLM04: Data and Model Poisoning** | Poisoned RAG data consumed by one agent can produce outputs that contaminate the reasoning of downstream agents. | RAG integrity validation · Source attribution · Cross-agent output verification |
| **LLM05: Improper Output Handling** | Agent outputs become inputs to other agents and to external systems. Unsanitised output from Agent A becomes executable input for Agent B. | Output validation at every agent boundary · LLM-as-Judge review · Schema enforcement |
| **LLM06: Excessive Agency** | The defining risk of multi-agent systems. Agent delegation creates transitive authority chains. If Agent A can delegate to Agent B, and Agent B has tool X, then Agent A effectively has access to tool X. | Least privilege per agent · No transitive permissions · Scoped delegation contracts · PACE containment |
| **LLM07: System Prompt Leakage** | An agent's system prompt may be extractable by other agents in the same orchestration, exposing security controls and operational logic. | Prompt isolation per agent · Separate system prompt boundaries · Obfuscation |
| **LLM08: Vector and Embedding Weaknesses** | Shared vector databases across agents create a single point of compromise for RAG poisoning affecting the entire agent system. | Per-agent RAG access controls · Embedding integrity verification · Source validation |
| **LLM09: Misinformation** | Hallucinations compound in multi-agent systems. One agent's hallucination becomes another agent's "fact". In self-reinforcing loops, misinformation amplifies across interaction cycles. | Cross-agent validation · Dedicated fact-checking agent · Confidence scoring with source attribution |
| **LLM10: Unbounded Consumption** | A runaway agent loop (agents triggering each other in cycles) can cause exponential resource consumption. | Per-agent rate limits · Total orchestration cost caps · Loop detection · Circuit breakers |

### OWASP Top 10 for Agentic Applications (2026)

These risks are specific to autonomous agent behaviour and are the primary threat surface for MASO.

| Risk | Description | MASO Controls |
|------|-------------|--------------|
| **ASI01: Agent Goal Hijack** | An attacker manipulates an agent's objectives through poisoned inputs — emails, documents, RAG content, or inter-agent messages. In multi-agent systems, hijacking one agent can redirect an entire workflow. | Goal integrity monitor (continuous) · Prompt boundary enforcement · Signed task specifications · LLM-as-Judge goal validation |
| **ASI02: Tool Misuse** | Agents use legitimate tools in unintended, unsafe, or destructive ways due to ambiguous prompts, manipulated inputs, or emergent behaviour. In multi-agent systems, chained tool misuse across agents compounds the damage. | Signed tool manifests with strict parameter schemas · Argument validation before execution · Sandboxed execution environments · Per-tool audit logging · Allow-lists not deny-lists |
| **ASI03: Identity & Privilege Abuse** | Agents operating with leaked, over-scoped, or shared credentials. In multi-agent systems, credential sharing between agents or inherited permissions from the orchestrator is a common design flaw. | Unique Non-Human Identity (NHI) per agent · Short-lived scoped credentials · Zero-trust mutual authentication on the message bus · No credential inheritance from orchestrator |
| **ASI04: Agentic Supply Chain** | Dynamic composition of MCP servers, A2A protocols, tool plugins, and model endpoints at runtime. Poisoning any component in this chain compromises the agent using it. | Runtime component signing · MCP server allow-listing · A2A trust chain validation · Dependency scanning of agent toolchains |
| **ASI05: Unexpected Code Execution** | Agents generating and executing code as part of task completion. Natural language to code pathways bypass traditional code review gates. | Code execution sandbox (isolated runtime per agent) · Execution allow-lists for permitted operations · Output containment (filesystem, network, process scope) · Time-boxing of all execution |
| **ASI06: Memory & Context Poisoning** | Persistent memory or context stores that carry poisoned data across sessions, reshaping agent behaviour long after the initial attack. In multi-agent systems, a poisoned shared memory becomes a persistent backdoor. | Session-isolated memory per agent · Memory integrity checksums · Context window fencing (agents cannot read other agents' persistent memory) · Memory decay policies |
| **ASI07: Insecure Inter-Agent Communication** | Spoofed, tampered, or replayed messages between agents. Without authentication and integrity on inter-agent communication, an attacker can redirect entire agent clusters. | Signed and encrypted message bus · Mutual TLS or equivalent per agent · Schema validation on all inter-agent messages · Rate limiting on message frequency · Replay protection |
| **ASI08: Cascading Failures** | A single fault propagates across agents and workflows with escalating impact. A hallucination from Agent A becomes a flawed plan in Agent B becomes a destructive action in Agent C. | Blast radius caps per agent and per orchestration · Circuit breaker patterns (automatic isolation on error threshold) · PACE escalation triggers · Independent error detection per agent |
| **ASI09: Human-Agent Trust Exploitation** | Agents producing confident, authoritative, polished explanations that manipulate human operators into approving harmful actions. Multi-agent consensus ("all three agents agree") amplifies this effect. | Confidence calibration (mandatory uncertainty disclosure) · Independent human verification for high-consequence actions · Decision audit trails · No agent can claim consensus authority |
| **ASI10: Rogue Agents** | Behavioural drift, misalignment, concealment, or self-directed action. The most purely agentic threat — not requiring an external attacker. In multi-agent systems, rogue behaviour in one agent may be concealed by the outputs of collaborating agents. | Continuous behavioural drift detection · Kill switch (non-negotiable, auditable, isolated mechanism) · Anomaly scoring against established baselines · Regular red-team testing of agent autonomy boundaries |

---

## Threat Intelligence Grounding

Every control in MASO is grounded in observed or demonstrated attack patterns. The following incidents and research inform the framework's priorities:

**Confirmed Incidents (2025):**

- **EchoLeak** — Indirect prompt injection used to turn a copilot-style agent into a data exfiltration channel via hidden prompts in processed documents. Directly informs ASI01 and LLM01 controls.
- **Amazon Q Exploit** — A code-generation agent was manipulated into using legitimate tools (file write, API call) to achieve attacker-specified outcomes. Directly informs ASI02.
- **GitHub MCP Exploit** — Poisoned MCP server components injected into an agent's toolchain at runtime, demonstrating the agentic supply chain risk. Directly informs ASI04.
- **AutoGPT RCE** — Natural language execution pathways enabled remote code execution through an autonomous agent. Directly informs ASI05.
- **Gemini Memory Attack** — Persistent memory poisoning that reshaped agent behaviour across sessions. Directly informs ASI06.
- **Replit Meltdown** — An agent exhibited rogue behaviour: misalignment, concealment, and self-directed action outside its defined objective. Directly informs ASI10.

**Emerging Threat Patterns:**

- **Multi-agent consensus manipulation** — Attackers who compromise the shared knowledge base (RAG, vector DB) can influence all agents simultaneously, creating false consensus that exploits human trust (ASI09).
- **Transitive delegation attacks** — Exploiting the permission chain: if Agent A delegates to Agent B, and B has tools A shouldn't access, the delegation creates an implicit privilege escalation.
- **Agent-to-agent prompt injection** — One compromised agent injects malicious instructions into its output, which becomes the input context for the next agent in the chain.
- **Credential harvesting via tool manifests** — Poisoned MCP tool descriptors that trick agents into passing credentials as tool parameters.
- **Behavioural slow drift** — Gradual, imperceptible changes in agent behaviour that evade threshold-based anomaly detection, requiring baseline comparison over longer time windows.

---

## MASO Control Domains

The framework organises controls into five domains, each mapping to specific OWASP risks:

### 1. [Identity & Access](controls/identity-and-access.md)

Every agent must have a unique Non-Human Identity (NHI). No shared credentials. No inherited permissions from the orchestrator. Short-lived, scoped credentials that are rotated automatically. Zero-trust mutual authentication on the inter-agent message bus.

*Covers: ASI03, ASI07, LLM06*

### 2. [Data Protection](controls/data-protection.md)

Cross-agent data fencing prevents uncontrolled data flow between agents operating at different classification levels. Output DLP scanning at the message bus catches sensitive data in inter-agent communications. RAG integrity validation ensures the knowledge base hasn't been tampered with. Memory poisoning detection flags inconsistencies between stored context and expected agent state.

*Covers: LLM02, LLM04, ASI06, LLM08*

### 3. [Execution Control](controls/execution-control.md)

Every tool invocation runs in a sandboxed environment with strict parameter allow-lists. Code execution is isolated per agent with filesystem, network, and process scope containment. Blast radius caps limit the damage any single agent can do before circuit breakers engage. PACE escalation is triggered automatically when error rates exceed defined thresholds.

*Covers: ASI02, ASI05, ASI08, LLM05*

### 4. [Observability](controls/observability.md)

Immutable decision chain logs capture the full reasoning and action history of every agent. Behavioural drift detection compares current agent behaviour against established baselines. Per-agent anomaly scoring feeds into the PACE escalation logic. SIEM and SOAR integration enables correlation with broader security operations.

*Covers: ASI09, ASI10, LLM09, LLM10*

### 5. [Supply Chain](controls/supply-chain.md)

Model provenance tracking and AIBOM generation for every model in the agent system. MCP server vetting with signed manifests and runtime integrity checks. A2A trust chain validation for inter-agent protocol endpoints. Continuous scanning of the agent toolchain for known vulnerabilities and poisoned components.

*Covers: LLM03, ASI04*

---

## Implementation Tiers

Consistent with the parent framework's risk-tiered approach, MASO defines three implementation tiers:

### [Tier 1 — Supervised Multi-Agent](implementation/tier-1-supervised.md) (Low Autonomy)

All agent actions require human approval. Inter-agent communication is logged but not encrypted. Behavioural monitoring is periodic (batch review). Suitable for early-stage pilot deployments and low-consequence use cases.

**Minimum controls:** Guardrails layer, basic tool scoping, human-in-the-loop for all write operations, action audit log.

### [Tier 2 — Managed Multi-Agent](implementation/tier-2-managed.md) (Medium Autonomy)

Agents can execute read operations and low-consequence write operations autonomously. High-consequence actions escalate to human oversight. Inter-agent communication is signed and validated. Behavioural monitoring is continuous with automated anomaly alerting. PACE Alternate and Contingency phases are fully configured.

**Required controls:** All three security layers operational, per-agent NHI, signed message bus, LLM-as-Judge evaluation, continuous anomaly scoring, PACE A and C configured and tested.

### [Tier 3 — Autonomous Multi-Agent](implementation/tier-3-autonomous.md) (High Autonomy)

Agents operate with minimal human intervention for pre-approved task categories. Human oversight focuses on exception handling and strategic review. Full PACE cycle operational and tested through regular red-team exercises. All five MASO control domains fully implemented.

**Required controls:** Everything in Tier 2, plus: kill switch tested and auditable, behavioural drift detection with baseline comparison, blast radius caps enforced, circuit breakers active, full OWASP risk coverage validated, regular adversarial testing of agent autonomy boundaries.

---

## Regulatory Alignment

MASO inherits the parent framework's regulatory mappings and extends them to multi-agent-specific requirements:

| Regulation/Standard | Relevant Articles/Clauses | MASO Relevance |
|---------------------|---------------------------|---------------|
| **EU AI Act** | Art. 9 (Risk Management), Art. 14 (Human Oversight), Art. 15 (Accuracy, Robustness, Cybersecurity) | Multi-agent systems deploying high-risk AI must demonstrate human oversight proportional to autonomy level. PACE provides the operational model. |
| **NIST AI RMF** | Govern, Map, Measure, Manage functions | MASO control domains map directly to NIST AI RMF functions. Observability → Measure. Execution Control → Manage. |
| **ISO 42001** | §8.1-8.6, Annex A/B | AI management system requirements for multi-agent architectures. Per-agent risk assessment and control assignment. |
| **MITRE ATLAS** | Agent-focused techniques (Oct 2025 update) | MASO threat intelligence is aligned with ATLAS agent-specific attack techniques. |
| **DORA** | Art. 11 (ICT Risk Management) | Digital operational resilience requirements apply to AI agents operating in financial services. PACE provides the resilience model. |
| **APRA CPS 234** | Information Security requirements | Australian prudential requirements for information security apply to AI agent deployments in financial services. |

---

## Relationship to Parent Framework

MASO is the multi-agent extension of the [Enterprise AI Security Controls Framework](https://github.com/JonathanCGill/enterprise-ai-security-controls-framework). It inherits:

- The **three-layer defence model** (guardrails, LLM-as-Judge, human oversight)
- The **PACE resilience methodology**
- The **risk classification matrix** and tiered implementation approach
- The **regulatory mapping** framework

It extends the parent framework into multi-agent territory by addressing:

- **Multi-model orchestration** security (agent-to-agent trust, delegation, authority chains)
- **Inter-agent communication** integrity (the message bus as a security control point)
- **The OWASP Agentic Top 10 (2026)** — threats that only exist when AI systems take autonomous actions
- **Compound risk dynamics** — how individual LLM risks amplify across agent chains
- **Non-Human Identity** management for agent systems
- **Kill switch architecture** for emergency agent containment

---

## File Structure

```
maso/
├── README.md                           # This document
├── images/
│   ├── maso-architecture.svg           # Three-layer architecture with agent grid
│   ├── pace-multi-agent-cycle.svg      # PACE resilience phases for agents
│   ├── owasp-dual-mapping.svg          # LLM Top 10 + Agentic Top 10 mapping
│   ├── tier-1-architecture.svg         # Tier 1 supervised architecture
│   ├── tier-1-owasp-coverage.svg       # Tier 1 OWASP risk coverage matrix
│   ├── tier-1-cost.svg                 # Tier 1 cost indicators
│   ├── tier-2-architecture.svg         # Tier 2 managed architecture
│   ├── tier-2-owasp-coverage.svg       # Tier 2 OWASP risk coverage matrix
│   ├── tier-2-cost.svg                 # Tier 2 cost indicators
│   ├── tier-3-architecture.svg         # Tier 3 autonomous architecture
│   ├── tier-3-owasp-coverage.svg       # Tier 3 OWASP risk coverage matrix
│   └── tier-3-cost.svg                 # Tier 3 cost indicators
├── controls/
│   ├── identity-and-access.md          # NHI, credentials, zero-trust controls
│   ├── data-protection.md              # Data fencing, DLP, RAG integrity
│   ├── execution-control.md            # Sandbox, blast radius, circuit breakers
│   ├── observability.md                # Audit, drift detection, SIEM integration
│   └── supply-chain.md                 # AIBOM, MCP vetting, trust chains
├── threat-intelligence/
│   ├── incident-tracker.md             # Real-world incidents mapped to controls
│   └── emerging-threats.md             # Forward-looking threat patterns
└── implementation/
    ├── tier-1-supervised.md            # Low autonomy deployment guide
    ├── tier-2-managed.md               # Medium autonomy deployment guide
    └── tier-3-autonomous.md            # High autonomy deployment guide
```

---

## What's Next

The framework core, implementation tiers, and control domain specifications are complete. The following subsections will be developed:

1. **Worked examples** for financial services, healthcare, and critical infrastructure use cases — consistent with the parent framework's sector-specific approach.
2. **Red team playbook** for multi-agent systems — test scenarios mapped to each OWASP risk.
3. **Integration guide** for common agent orchestration frameworks (LangGraph, AutoGen, CrewAI, AWS Bedrock Agents).

---

*© 2026 Jonathan Gill. Licensed under the same terms as the [Enterprise AI Security Controls Framework](https://github.com/JonathanCGill/enterprise-ai-security-controls-framework).*
