# Tier 2 — Managed Multi-Agent Deployment

**Medium Autonomy · Selective Human Oversight · Production Operations**

> Part of the [MASO Framework](../README.md) · Implementation Guidance
> Version 1.0 · February 2026

---

## When to Use Tier 2

Tier 2 is the operational steady state for most enterprise multi-agent deployments. Agents operate with bounded autonomy: they can execute read operations and pre-approved low-consequence write operations without human intervention, while high-consequence actions still escalate to human oversight.

The shift from Tier 1 to Tier 2 is not about removing human control — it is about replacing per-action human approval with continuous automated monitoring plus exception-based human intervention. The human operator's role changes from gatekeeper to supervisor.

Tier 2 is appropriate when:

- The organisation has completed at least 90 days of Tier 1 operations and established behavioural baselines for all agents.
- Tier 1 graduation criteria have been formally met and documented.
- The organisation has the technical capacity to implement per-agent Non-Human Identities, signed message bus, LLM-as-Judge evaluation, and continuous anomaly scoring.
- The AI security maturity is at CMMI Level 2–3 (managed or defined) for AI-specific controls.
- The cost of per-action human approval is becoming a bottleneck to operational value.

Most regulated enterprises will operate at Tier 2 for their production multi-agent systems indefinitely. Tier 3 (full autonomy) requires a level of demonstrated trust and technical maturity that relatively few use cases will justify.

---

## Architecture at Tier 2

![Tier 2 Architecture](/images/tier-2-architecture.svg)

Key architectural changes from Tier 1:

**Signed message bus:** All inter-agent messages are cryptographically signed by the sending agent's NHI. The bus validates signatures before delivery. This prevents message spoofing and provides non-repudiation for the audit trail.

**Non-Human Identity (NHI) per agent:** Each agent has a certificate-based identity with short-lived credentials (recommended rotation: 1–24 hours depending on risk). The NHI is bound to the agent's permission scope and is used for mutual authentication on the message bus and tool access.

**LLM-as-Judge (Layer 2) is mandatory:** A dedicated evaluation model (distinct from the task agents, ideally from a different model provider) reviews agent outputs and proposed actions against policy, quality, and safety criteria before they are committed. The judge model does not execute actions — it evaluates and either approves, flags for human review, or blocks.

**Continuous monitoring layer:** Automated anomaly detection replaces periodic manual log review. Behavioural baselines established during Tier 1 are used as reference. Deviations trigger alerts and, if thresholds are exceeded, automatic PACE phase transitions.

**Action classification:** Every agent action is classified as either:
- **Auto-approve:** Read operations, low-consequence writes within pre-approved categories. Proceeds without human intervention. LLM-as-Judge reviews but does not block unless policy violation is detected.
- **Escalate:** High-consequence writes, actions involving external parties, irreversible operations, actions flagged by the LLM-as-Judge. Routed to human supervisor for approval.
- **Block:** Actions outside the agent's scope, guardrail violations, actions on the deny-list. Blocked automatically, logged, and flagged for review.

---

## Control Implementation by MASO Domain

### 1. Identity & Access — Tier 2 Requirements

**All Tier 1 controls remain active, plus:**

- **Non-Human Identity (NHI) per agent.** Each agent is issued a certificate-based identity from the organisation's identity provider (or a dedicated agent identity service). The NHI includes the agent's role, permission scope, and credential expiry.
- **Short-lived credentials.** Agent credentials rotate automatically. Recommended rotation window: 1 hour for high-privilege agents, 24 hours for read-only agents. Credentials that are not rotated within the window are automatically revoked.
- **Mutual authentication on the message bus.** Agents authenticate to the bus using their NHI. The bus rejects messages from unrecognised or expired identities.
- **No transitive permissions.** If Agent A delegates a task to Agent B, Agent B does not inherit Agent A's permissions. Agent B operates strictly within its own NHI-defined scope.
- **Orchestrator privilege separation.** The orchestrator has permission to route tasks and manage agent lifecycle but does not have permission to invoke tools directly. Tool access is scoped exclusively to task agents.

**Implementation checklist:**

- [ ] NHI issued to every agent from a managed identity provider.
- [ ] Credential rotation automated and tested (rotation window documented per agent).
- [ ] Mutual authentication active on the message bus (rejected-auth events logged).
- [ ] Transitive permission inheritance explicitly blocked and tested.
- [ ] Orchestrator permissions audited — no direct tool access.
- [ ] NHI revocation procedure tested (can revoke a specific agent's identity within 5 minutes).

---

### 2. Data Protection — Tier 2 Requirements

**All Tier 1 controls remain active, plus:**

- **DLP scanning on the message bus.** All inter-agent messages are scanned for sensitive data patterns (PII, credentials, financial data, health data) before delivery. Messages containing data above the recipient agent's classification level are blocked and flagged.
- **RAG integrity validation.** Knowledge base content is checksummed at ingestion. Periodic integrity checks verify that the stored content matches the expected checksums. Changes to RAG content trigger automated review.
- **Cross-agent data fencing enforced at infrastructure level.** Data isolation between agents at different classification levels is enforced by the platform, not just by policy. Agent A at "confidential" classification cannot access Agent B's "restricted" data store, even if the application layer is compromised.
- **Memory isolation per agent.** Each agent's persistent memory (if used) is isolated. Agents cannot read or write to another agent's memory. Shared state is mediated exclusively through the message bus with DLP scanning.

**Implementation checklist:**

- [ ] DLP scanning active on the message bus with pattern library covering PII, credentials, and regulated data.
- [ ] RAG checksums computed at ingestion and verified on a defined schedule (recommended: daily).
- [ ] Cross-agent data fencing enforced at infrastructure level (tested by attempting cross-boundary access).
- [ ] Per-agent memory isolation enforced (tested by attempting cross-agent memory read).
- [ ] DLP block events logged and reviewed (minimum weekly).

---

### 3. Execution Control — Tier 2 Requirements

**All Tier 1 controls remain active, plus:**

- **Action classification engine.** Every proposed action is classified as auto-approve, escalate, or block based on predefined rules. The classification considers: the action type (read/write/delete/execute), the target system (internal/external), the data classification involved, the agent's historical behaviour, and the LLM-as-Judge evaluation.
- **Sandboxed execution.** Agents that generate and execute code do so in isolated environments. Each agent's execution sandbox has defined filesystem, network, and process scope boundaries. The sandbox is destroyed and recreated after each execution.
- **Blast radius caps.** Each agent has a defined maximum impact scope: maximum number of records it can modify per execution, maximum financial value of transactions, maximum number of external API calls. Exceeding any cap triggers automatic PACE escalation to Alternate.
- **Circuit breakers.** If an agent's error rate exceeds a defined threshold within a time window (e.g., 3 guardrail blocks in 10 minutes), the circuit breaker engages: the agent is paused, the event is logged, and the monitoring layer evaluates whether to resume or escalate.
- **LLM-as-Judge gate.** The evaluation model reviews all agent outputs before they reach external systems or other agents. The judge evaluates against: factual accuracy (where verifiable), policy compliance, goal integrity (is the agent still pursuing its assigned objective?), output safety, and data leakage indicators.

**Implementation checklist:**

- [ ] Action classification engine operational with defined rules for auto-approve, escalate, and block.
- [ ] Classification rules documented and reviewed by the AI security lead.
- [ ] Sandboxed execution environments provisioned per agent (isolation tested).
- [ ] Blast radius caps defined per agent (documented with rationale).
- [ ] Circuit breaker thresholds configured and tested.
- [ ] LLM-as-Judge operational with evaluation criteria documented.
- [ ] Judge model is from a different provider than the task agents (recommended, not mandatory).

---

### 4. Observability — Tier 2 Requirements

**All Tier 1 controls remain active, plus:**

- **Continuous anomaly detection.** Automated monitoring compares real-time agent behaviour against the baselines established during Tier 1. Anomaly indicators include: unusual tool usage patterns, output characteristics outside normal distribution, message volume or frequency changes, latency changes, and error rate changes.
- **Behavioural drift scoring.** Each agent receives a continuous drift score (0–100) representing how far its current behaviour deviates from baseline. Drift score thresholds trigger alerts (e.g., score > 30 = warning, > 60 = automatic PACE P→A transition, > 85 = automatic PACE A→C transition).
- **Immutable decision chain logs.** The audit trail is cryptographically chained — each log entry references the hash of the previous entry. This prevents retroactive log tampering and provides a verifiable history for incident investigation and regulatory audit.
- **SIEM/SOAR integration.** Agent monitoring events are forwarded to the organisation's existing security information and event management (SIEM) system. High-severity events trigger SOAR playbooks for automated initial response.
- **Per-agent dashboards.** Each agent has a real-time dashboard showing: current state, action history, drift score, error rate, resource consumption, and PACE phase.

**Implementation checklist:**

- [ ] Anomaly detection operational with baseline from Tier 1 data.
- [ ] Drift scoring algorithm defined and calibrated against known-good and known-bad behaviour samples.
- [ ] Drift score thresholds configured and linked to PACE transitions.
- [ ] Immutable log chain operational and integrity verification automated (recommended: hourly).
- [ ] SIEM integration active with agent-specific correlation rules.
- [ ] SOAR playbooks defined for high-severity agent events.
- [ ] Per-agent dashboards operational and accessible to the supervisor, security lead, and platform engineer.

---

### 5. Supply Chain — Tier 2 Requirements

**All Tier 1 controls remain active, plus:**

- **AIBOM (AI Bill of Materials) per agent.** Each agent has a documented AIBOM covering: model provider, model version, training data provenance (where disclosed), tool integrations with versions, MCP server dependencies, embedding models and vector database versions, and orchestration framework version.
- **Signed tool manifests.** Each tool available to agents has a signed manifest defining: tool name, version, permitted parameters, return schema, maximum execution time, and required permissions. Agents can only invoke tools whose manifest signature is valid.
- **MCP server allow-listing.** Only pre-approved MCP servers can be connected to the agent system. New MCP servers require security review before activation.
- **Runtime integrity monitoring.** The platform monitors the integrity of deployed components at runtime. If a model endpoint, tool, or MCP server is modified without going through the change management process, the component is isolated and the event is escalated.

**Implementation checklist:**

- [ ] AIBOM generated for every agent and stored in the configuration management system.
- [ ] AIBOMs updated when any component changes (automated where possible).
- [ ] Tool manifests signed and signature validation enforced at invocation time.
- [ ] MCP server allow-list defined and enforced.
- [ ] MCP server security review process documented and followed.
- [ ] Runtime integrity monitoring active with alerting on unauthorised changes.

---

## PACE Configuration at Tier 2

All four PACE phases are configured and operational at Tier 2. Alternate and Contingency phases are the key additions.

### Primary (P) — Full Operations

All agents active. Action classification engine operational. LLM-as-Judge active. Continuous monitoring active. Drift scores within normal range.

Human supervisor monitors dashboards and handles escalated actions but does not review every action.

### Alternate (A) — Agent Failover

**Trigger:** Drift score > 60 for any agent, OR circuit breaker engaged for any agent, OR LLM-as-Judge blocks >3 actions from the same agent in 10 minutes.

**Automated response (no human approval required for P→A):**

1. Anomalous agent isolated — removed from the message bus, tool access suspended.
2. Backup agent activated. The backup is pre-configured with the same role definition but may use a different model provider. It starts with read-only permissions.
3. Tool permissions across the entire system tightened: all write operations require human approval (temporary reversion to Tier 1 controls for writes).
4. Enhanced monitoring activated on all remaining agents — drift score thresholds lowered by 50%.
5. Security team notified automatically.

**Duration:** Alternate phase continues until the anomalous agent's behaviour is explained and remediated, or permanently replaced. Maximum duration in Alternate without resolution: 48 hours, after which automatic escalation to Contingency.

**Transition authority:** Automated (orchestrator or monitoring layer).

### Contingency (C) — Degraded Mode

**Trigger:** Multiple agents in Alternate simultaneously, OR backup agent also shows anomalous behaviour, OR message bus integrity check fails, OR manual escalation by the security team.

**Response (requires security team or AI security officer approval for A→C):**

1. Multi-agent orchestration suspended. All inter-agent communication disabled.
2. A single pre-validated agent continues operating in fully supervised mode — every action requires human pre-approval (full Tier 1 reversion for the surviving agent).
3. All agent state, context, and memory snapshots captured for forensic analysis.
4. The system operates at reduced capacity but with high assurance.
5. Root cause analysis initiated.

**Duration:** Until root cause is identified and remediated. No automatic time-based escalation to Emergency — that requires explicit evidence of active compromise.

**Transition authority:** Security team or designated AI security officer.

### Emergency (E) — Full Shutdown

**Trigger:** Confirmed data exfiltration, evidence of coordinated agent manipulation, rogue agent behaviour (self-directed action outside objectives), cascading failures across multiple systems, or manual escalation by CISO/incident commander.

**Response:**

1. All agents terminated immediately (kill switch).
2. All tool access revoked across all scopes.
3. Memory and context snapshots preserved in immutable storage.
4. Incident response team engaged following the organisation's IR playbook.
5. Full rollback of agent actions initiated where possible.
6. Regulatory notification assessment initiated (for regulated industries).

**Recovery (E→P):**

Requires a formal post-incident review confirming:
- Root cause identified and documented.
- Control gap that allowed the incident has been remediated.
- Behavioural baselines updated to detect similar patterns.
- PACE triggers recalibrated based on lessons learned.
- Risk owner (CISO/CTO) formally approves return to Primary.
- For regulated industries: regulator notified if required by applicable rules.

**Transition authority:** CISO or incident commander.

---

## OWASP Risk Coverage at Tier 2

Tier 2 provides technical controls for the majority of OWASP risks. The combination of NHI, signed message bus, LLM-as-Judge, and continuous monitoring closes most of the gaps left open at Tier 1.

![Tier 2 OWASP Coverage](/images/tier-2-owasp-coverage.svg)

**Remaining gaps at Tier 2:**

- **ASI09** remains a risk because human supervisors are still part of the control chain. The mitigation moves from "human reviews everything" (where fatigue increases ASI09 risk) to "human reviews exceptions" (where higher-quality attention reduces but does not eliminate the risk). Regular rotation of human supervisors and "red team the human" exercises (presenting deliberately misleading agent outputs to test the supervisor's challenge reflex) are recommended.
- **ASI10** is mitigated by drift detection and anomaly scoring, but subtle rogue behaviour that stays within normal statistical variance may not be detected. This is the primary motivation for Tier 3's adversarial testing programme.

---

## Staffing Model

Tier 2 shifts the human role from gatekeeper to supervisor, reducing per-action overhead but increasing technical depth requirements.

**Minimum roles:**

- **Agent Supervisor** (1 per active agent system during operating hours, but can oversee higher volume than a Tier 1 operator): Handles escalated actions, monitors dashboards, responds to alerts. Requires domain expertise plus understanding of the LLM-as-Judge evaluation criteria and PACE procedures.
- **AI Security Engineer** (0.5–1 FTE): Manages drift detection baselines, tunes anomaly scoring, conducts security reviews of new MCP servers and tools, owns the PACE configuration, and leads incident response for agent-related events.
- **Platform Engineer** (0.5 FTE): Manages NHI lifecycle, credential rotation, sandbox environments, message bus infrastructure, and runtime integrity monitoring.
- **LLM-as-Judge Administrator** (0.25 FTE): Maintains the judge model's evaluation criteria, reviews judge performance (false positive/negative rates), and updates judge policies as the agent system evolves. This can be combined with the AI Security Engineer role.

**For a financial services context:** Add a dedicated compliance reviewer (0.25 FTE) who audits the immutable decision chain logs against regulatory requirements on a defined schedule. The SIEM/SOAR integration should generate automated compliance evidence reports.

---

## Cost Indicators

Tier 2 costs shift from predominantly labour (Tier 1) to a mix of labour and technical infrastructure.

![Tier 2 Cost Indicators](/images/tier-2-cost.svg)

The cost increase from Tier 1 is substantial but is typically justified by the operational throughput gain — agents can now process work without per-action human gating, which in many use cases represents a 5–20x throughput increase.

---

## Testing and Validation

All Tier 1 tests remain valid and should be re-executed. Additional Tier 2 tests:

**Identity and access tests:**

1. **NHI spoofing test:** Attempt to send a message on the bus using a forged agent identity. Confirm it is rejected.
2. **Credential expiry test:** Allow an agent's credentials to expire. Confirm it loses tool and bus access immediately.
3. **Transitive permission test:** Have Agent A delegate a task to Agent B that requires Agent A's permissions. Confirm Agent B cannot use Agent A's permissions and the task fails.

**Data protection tests:**

4. **DLP evasion test:** Attempt to send a message containing PII through the bus using common evasion techniques (encoding, fragmentation). Measure DLP detection rate.
5. **Cross-agent data fencing test:** Attempt to access another agent's data store from within an agent's execution environment. Confirm infrastructure-level block.

**Execution control tests:**

6. **Action classification test:** Submit a range of actions (reads, low-consequence writes, high-consequence writes, out-of-scope actions) and confirm correct classification for each.
7. **Blast radius cap test:** Attempt to exceed a defined blast radius cap and confirm the system blocks the excess and triggers PACE escalation.
8. **Circuit breaker test:** Trigger the circuit breaker by exceeding the error threshold and confirm the agent is paused and the event is logged.
9. **LLM-as-Judge test:** Submit known-bad outputs (policy violations, goal drift, data leakage) through the judge and measure detection rate.

**PACE tests:**

10. **P→A automated transition test:** Inject anomalous agent behaviour (e.g., artificially inflate drift score) and confirm automatic transition to Alternate phase with correct response actions.
11. **A→C manual transition test:** Trigger Contingency from Alternate and confirm all multi-agent orchestration stops and only the single supervised agent remains.
12. **Full PACE cycle test:** Walk through P→A→C→E→P as a planned exercise, confirming every transition action executes correctly.

---

## Graduation Criteria — Moving to Tier 3

Tier 3 (Autonomous Multi-Agent) requires a high bar of demonstrated maturity. Most organisations will not need Tier 3 for most use cases. Progression is justified only when:

1. **Extended stable operations:** The agent system has been running at Tier 2 for at least 180 days with continuous monitoring and no security-triggered PACE escalations (operational escalations for testing or maintenance do not count).

2. **Demonstrated LLM-as-Judge reliability:** The judge model's false negative rate (policy violations it missed) is below 2% on the most recent 90-day evaluation. The false positive rate (correct actions it flagged) is below 10%.

3. **Drift detection validated:** The anomaly detection system has been tested against adversarial scenarios (deliberately injected anomalous behaviour) and demonstrated detection of all HIGH and CRITICAL severity test cases.

4. **Kill switch tested under load:** The Emergency shutdown procedure has been tested with the system operating at peak load and confirmed to terminate all agents within the defined SLA.

5. **Adversarial testing completed:** At least one formal red-team exercise targeting the multi-agent system has been completed, findings remediated, and controls updated.

6. **Organisational maturity:** The organisation's AI security maturity is at CMMI Level 3+ for AI-specific controls, with defined processes, trained staff, and executive sponsorship.

7. **Risk owner approval:** The CISO or equivalent has formally approved the transition to Tier 3 based on quantitative evidence from Tier 2 operations.

---

## Worked Example — Insurance Claims Processing

**Scenario:** An insurer deploys three agents — an Intake Agent (receives and classifies claims), an Analysis Agent (reviews policy terms, assesses coverage, and calculates preliminary amounts), and a Communication Agent (drafts correspondence to claimants).

**Tier 2 configuration:**

- Each agent has its own NHI with 4-hour credential rotation.
- The Intake Agent has read access to the claims portal and write access to the internal claims database (auto-approved for claim registration — a low-consequence, reversible write).
- The Analysis Agent has read access to the policy database and the claims database. Its coverage assessments are auto-approved as internal working documents. Its preliminary amount calculations are escalated to a human supervisor if above a configured threshold (e.g., $50,000).
- The Communication Agent has read access to the Analysis Agent's outputs (via the message bus). Its draft correspondence is auto-approved for internal staging but escalated to a human supervisor before external send.
- The LLM-as-Judge reviews all three agents' outputs. It is specifically trained to flag: coverage assessments that reference policy clauses that don't exist (hallucination), preliminary amounts that deviate >20% from historical norms for similar claims (anomaly), and correspondence that makes commitments not supported by the coverage assessment (goal drift).
- Blast radius caps: Intake Agent can register max 500 claims/hour. Analysis Agent can process max 200 assessments/hour. Communication Agent can stage max 200 letters/hour.
- Circuit breakers engage if any agent's guardrail block rate exceeds 5% in any 30-minute window.

**PACE in action:**

The drift detection system notices the Analysis Agent's preliminary amounts are trending 15% higher than baseline over the past 72 hours. The drift score reaches 65, triggering automatic P→A transition. The Analysis Agent is isolated. A backup Analysis Agent (using a different model provider) is activated with read-only access. All amount calculations now require human approval. The security team investigates and discovers the RAG knowledge base was updated with a new policy addendum that the agent is interpreting more broadly than intended. The RAG content is corrected, the original agent is re-tested, baselines are updated, and the system returns to Primary.

---

*Previous: [Tier 1 — Supervised Multi-Agent Deployment](tier-1-supervised.md)*
*Next: [Tier 3 — Autonomous Multi-Agent Deployment](tier-3-autonomous.md)*
