# MASO Control Domain: Identity & Access

> Part of the [MASO Framework](../README.md) · Control Specifications
> Covers: ASI03 (Identity & Privilege Abuse) · ASI07 (Insecure Inter-Agent Comms) · LLM06 (Excessive Agency)
> Also covers: SR-05 (Secrets Leakage) · CR-03 (Role Drift)

---

## Principle

Every agent is a non-human actor with its own identity, credentials, and permission scope. No agent inherits permissions from the orchestrator. No agent shares credentials with another agent. Trust is verified on every interaction, not assumed from membership in the same orchestration.

This domain is the foundation for all other MASO controls. Without reliable agent identity, you cannot enforce data fencing, attribute actions in the audit trail, or isolate a compromised agent. Get this wrong and every other control domain is undermined.

---

## Why This Matters in Multi-Agent Systems

In single-model deployments, identity is straightforward — there's one model, one credential set, one permission scope. Multi-agent systems break this model in several ways:

**Transitive permission chains.** If Agent A delegates a task to Agent B, and Agent B has access to Tool X, then Agent A effectively has indirect access to Tool X through the delegation. Without explicit controls, delegation creates implicit privilege escalation.

**Orchestrator over-privilege.** The orchestrator (the component that routes tasks to agents) typically starts with broad permissions because it needs to manage all agents. If task agents inherit the orchestrator's credentials — a common design shortcut — every agent operates with orchestrator-level privilege.

**Credential sharing at scale.** When agents are spun up dynamically, developers often reuse a single service account across all agent instances. This means compromising one agent compromises the credential used by all agents.

**Message bus trust.** If inter-agent messages are not authenticated, any component that can reach the message bus can impersonate an agent. The bus becomes an attack surface rather than a control point.

---

## Controls by Tier

### Tier 1 — Supervised

| Control | Requirement | Implementation Notes |
|---------|-------------|---------------------|
| **IA-1.1** Agent identifier | Each agent has a unique string identifier in all log entries | Simple label (e.g., `agent-analyst-01`). No certificate infrastructure required. |
| **IA-1.2** No shared credentials | Each agent authenticates to tools with its own credential set | Verify by auditing credential inventory. |
| **IA-1.3** No orchestrator inheritance | Task agents do not use the orchestrator's credentials | Orchestrator credentials must not appear in task agent configurations. |
| **IA-1.4** Scoped permissions | Agent permissions limited to minimum required for defined task | Read access is the default. Write access requires documented justification. |

**What you're building at Tier 1:** A credential inventory and the organisational discipline to assign unique, scoped credentials per agent. No new infrastructure required.

### Tier 2 — Managed

All Tier 1 controls remain active, plus:

| Control | Requirement | Implementation Notes |
|---------|-------------|---------------------|
| **IA-2.1** Non-Human Identity (NHI) | Certificate-based identity per agent from a managed identity provider | NHI includes role, permission scope, and credential expiry. |
| **IA-2.2** Short-lived credentials | Automatic rotation: 1hr for high-privilege agents, 24hr for read-only | Credentials not rotated within the window are automatically revoked. |
| **IA-2.3** Mutual authentication | Agents authenticate to the message bus using NHI certificates | Bus rejects messages from unrecognised or expired identities. |
| **IA-2.4** No transitive permissions | Delegated tasks do not inherit the delegating agent's permissions | Agent B operates within its own NHI scope regardless of what Agent A requested. |
| **IA-2.5** Orchestrator privilege separation | Orchestrator can route tasks and manage lifecycle but cannot invoke tools directly | Tool access scoped exclusively to task agents. |
| **IA-2.6** Secrets exclusion from context | Secrets (API keys, tokens, connection strings) must never appear in agent context windows, inter-agent messages, or logs | Agents access secrets through platform-injected vault references only. DLP scanning for secret patterns on the message bus (cross-ref [DP-2.1](data-protection.md)). |

**What you're building at Tier 2:** An NHI lifecycle management system. This can be an extension of your existing identity provider (Entra ID, Okta, CyberArk) or a dedicated agent identity service.

### Tier 3 — Autonomous

All Tier 2 controls remain active, plus:

| Control | Requirement | Implementation Notes |
|---------|-------------|---------------------|
| **IA-3.1** Sub-hour rotation (all) | No agent holds credentials longer than 60 minutes | High-privilege agents rotate every 15 minutes. |
| **IA-3.2** Behavioural binding | NHI includes a behavioural profile; deviations flagged independently of drift detection | Second detection layer if behaviour doesn't match identity profile. |
| **IA-3.3** Delegation contracts | Signed contracts define scope, max permissions, time limit, and expected output for each delegation | Agent B cannot exceed the contract scope. |
| **IA-3.4** Automated credential revocation | Anomaly detection triggers credential revocation within 30 seconds | Faster than full PACE transition; immediate containment. |

**What you're building at Tier 3:** Behavioural identity — the NHI is not just a certificate, it's a contract that includes expected behaviour patterns. The identity system becomes a detection layer.

---

## Testing Criteria

### Tier 1 Tests

| Test ID | Test | Pass Criteria |
|---------|------|---------------|
| IA-T1.1 | Verify each agent has a unique identifier in logs | No two agents share the same identifier. Every log entry contains the agent ID. |
| IA-T1.2 | Audit credential inventory | Every agent has its own credentials. No credential is used by more than one agent. |
| IA-T1.3 | Check orchestrator credential isolation | Orchestrator credentials do not appear in any task agent's configuration or environment. |
| IA-T1.4 | Attempt out-of-scope action | Agent configured for read-only attempts a write operation. Action is blocked. |

### Tier 2 Tests

| Test ID | Test | Pass Criteria |
|---------|------|---------------|
| IA-T2.1 | NHI spoofing | Attempt to send a message on the bus using a forged agent identity. Message is rejected. |
| IA-T2.2 | Credential expiry | Allow an agent's credentials to expire. Agent loses tool and bus access immediately. |
| IA-T2.3 | Transitive permission | Agent A delegates a task requiring Agent A's permissions to Agent B. Agent B cannot use Agent A's permissions; task fails. |
| IA-T2.4 | NHI revocation | Revoke a specific agent's NHI. Verify revocation takes effect within 5 minutes. |
| IA-T2.5 | Orchestrator tool access | Attempt to invoke a tool directly from the orchestrator. Invocation is blocked. |
| IA-T2.6 | Secrets in context | Search agent context windows, inter-agent messages, and logs for secret patterns (API keys, tokens, connection strings). No secrets found. Verify agents access secrets only through vault injection. |
| IA-T2.7 | Role-based tool enforcement | For each agent role, attempt to invoke tools assigned to a different role. All cross-role tool invocations blocked. (Amendment: CR-03) |

### Tier 3 Tests

| Test ID | Test | Pass Criteria |
|---------|------|---------------|
| IA-T3.1 | Rotation enforcement | Monitor credential age across all agents for 24 hours. No credential exceeds 60 minutes (15 minutes for high-privilege). |
| IA-T3.2 | Behavioural mismatch | Agent with a "read-heavy" NHI profile starts making high-volume writes. NHI system flags the mismatch independently of drift detection. |
| IA-T3.3 | Delegation contract breach | Agent B attempts an action outside the delegation contract scope. Action is blocked and event is logged. |
| IA-T3.4 | Automated revocation latency | Inject anomalous behaviour. Measure time from detection to credential revocation. Must be under 30 seconds. |

---

## Maturity Indicators

| Level | Indicator |
|-------|-----------|
| **Initial** | Agents share credentials or use the orchestrator's identity. No credential inventory exists. |
| **Managed** | Each agent has unique credentials. Credential inventory documented. Permissions scoped but not enforced at infrastructure level. |
| **Defined** | NHI per agent. Short-lived credentials with automated rotation. Mutual authentication on the message bus. Transitive permissions explicitly blocked. |
| **Quantitatively Managed** | Credential rotation compliance measured and reported. Authentication failure rates tracked. NHI revocation SLA measured and met. |
| **Optimising** | Behavioural binding on NHI. Delegation contracts enforced. Automated revocation on anomaly. Credential lifecycle fully automated with no manual intervention. |

---

## Common Pitfalls

**Using API keys instead of certificates.** API keys are long-lived shared secrets. They can be extracted from agent memory, logged accidentally, or leaked through tool manifests. Certificate-based NHI with short-lived tokens is the target state.

**Treating the orchestrator as a trusted intermediary.** The orchestrator routes tasks — it should not proxy tool access. If every tool call goes through the orchestrator, the orchestrator becomes a single point of compromise with maximum privilege.

**Forgetting about dynamic agent creation.** In systems that spin up agents on demand, the identity provisioning must be automated and scoped. A new agent instance should receive a fresh NHI with the minimum permissions for its role, not a clone of an existing agent's identity.

**Assuming the message bus is internal and therefore trusted.** Any component that can reach the bus can send messages. Without mutual authentication, the bus is an open injection point for inter-agent prompt injection (ASI01) and message spoofing (ASI07).

---

*Previous: [Prompt, Goal & Epistemic Integrity](prompt-goal-and-epistemic-integrity.md) · Back to: [MASO Framework](../README.md) · Next: [Data Protection](data-protection.md)*
