# Non-Human Identity Lifecycle

> Managing AI agents as identities, not just applications.

## The Problem

AI agents act autonomously. They call APIs, access data, invoke tools, and communicate with other agents. In identity terms, they are non-human identities (NHIs) — principals that need the same lifecycle management as human users and service accounts.

Most enterprises don't treat them this way. Agents run under shared service accounts, use long-lived API keys, and have permissions that were set at deployment and never reviewed.

---

## Lifecycle Phases

### 1. Provisioning

| Requirement | Implementation |
|-------------|---------------|
| **Unique identity** | Every agent instance gets a unique identity in your IdP (Entra ID, Okta, etc.) |
| **Classification** | Tag the identity as `non-human:ai-agent` with metadata: owning team, risk tier, purpose |
| **Sponsorship** | Every agent identity has a human sponsor who is accountable for its actions |
| **Initial permissions** | Least privilege. Start with no permissions; grant only what's needed for declared purpose |
| **Credential issuance** | Short-lived credentials preferred (OAuth tokens, OIDC). Avoid long-lived API keys where possible |

### 2. Authentication

| Control | Implementation |
|---------|---------------|
| **Machine identity certificates** | mTLS or workload identity (SPIFFE/SPIRE) for agent-to-service authentication |
| **OAuth 2.0 client credentials** | For API-based agent interactions; tokens scoped to specific resources |
| **No shared credentials** | Each agent instance authenticates independently. No shared API keys across agents |
| **Credential rotation** | Automated rotation on a schedule (30–90 days) or on every deployment |
| **No hardcoded secrets** | Credentials in a secrets manager (Vault, AWS Secrets Manager, etc.), not in code or config |

### 3. Authorisation

| Principle | Implementation |
|-----------|---------------|
| **Least privilege** | Agent can only access what it needs for its declared purpose |
| **Scope constraints** | Define allowed actions, allowed data sources, allowed tools explicitly |
| **Time-bounded access** | Permissions expire and must be re-granted (just-in-time access) |
| **Delegation constraints** | If agent can delegate to other agents, delegation scope ≤ agent's own scope |
| **User context propagation** | When acting on behalf of a user, agent's effective permissions = intersection of agent permissions and user permissions |

```yaml
# Example agent authorisation policy
agent_policy:
  agent_id: "research-agent-prod-01"
  sponsor: "jgill@example.com"
  risk_tier: 2
  
  allowed_actions:
    - web_search
    - document_retrieval
    - text_generation
  
  denied_actions:
    - payment_processing
    - user_data_modification
    - credential_management
  
  data_access:
    - scope: "knowledge-base"
      access: "read"
    - scope: "customer-data"
      access: "none"
  
  tool_access:
    - tool: "search-api"
      allowed: true
    - tool: "database-write"
      allowed: false
  
  delegation:
    allowed: true
    max_depth: 2
    allowed_targets: ["search-agent", "summarisation-agent"]
  
  credential:
    type: "oauth2_client_credentials"
    rotation_days: 30
    expires: "2026-08-11T00:00:00Z"
```

### 4. Monitoring

| What to Monitor | Why |
|----------------|-----|
| **API call patterns** | Detect anomalous behaviour (see [Anomaly Detection Ops](/extensions/technical/anomaly-detection-ops.md)) |
| **Permission usage** | Are all granted permissions being used? Remove unused ones |
| **Credential access** | When and from where are credentials being retrieved? |
| **Tool invocations** | Which tools is the agent calling? Are they all on the allowlist? |
| **Delegation events** | Is the agent delegating as expected, or attempting new delegation paths? |

### 5. Access Review

| Activity | Frequency |
|----------|-----------|
| **Permission review** | Quarterly — same cadence as human access reviews |
| **Sponsor confirmation** | Quarterly — sponsor confirms agent is still needed and permissions are appropriate |
| **Unused permission removal** | Monthly — automated detection and removal of permissions not used in 30 days |
| **Credential audit** | Monthly — verify no expired or orphaned credentials |

### 6. Deprovisioning

| Trigger | Action |
|---------|--------|
| **Agent retired** | Revoke all credentials, remove permissions, archive audit logs, mark identity as deactivated |
| **Sponsor leaves** | Reassign sponsor within 5 business days or deactivate agent |
| **Security incident** | Immediate credential revocation, isolate agent, preserve logs for investigation |
| **Risk tier change** | Re-evaluate permissions against new risk tier requirements |

---

## Mapping to Existing IAM Frameworks

| Enterprise IAM Concept | AI Agent Equivalent |
|-----------------------|-------------------|
| User account | Agent identity |
| Service account | Not sufficient — agents need richer metadata and lifecycle management |
| Role | Agent permission set (allowed actions + data access + tool access) |
| Group | Agent class (all research agents, all customer-facing agents) |
| Access review | Agent permission review |
| Joiner/mover/leaver | Provisioning/re-scoping/deprovisioning |
| Privileged access management | Tier 3 agent controls — JIT access, session recording, approval workflows |

---

## Platform-Specific Guidance

| Platform | NHI Approach |
|----------|-------------|
| **Azure / Entra ID** | Register agents as Managed Identities or App Registrations with appropriate API permissions |
| **AWS** | IAM roles for agents; use STS for short-lived credentials; scope with IAM policies |
| **Google Cloud** | Workload Identity Federation; service accounts with IAM conditions |
| **Kubernetes** | Service accounts + RBAC; SPIFFE/SPIRE for workload identity |

---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
