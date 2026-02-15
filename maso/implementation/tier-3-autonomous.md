# Tier 3 — Autonomous Multi-Agent Deployment

**High Autonomy · Exception-Based Human Oversight · Adversarial-Tested Operations**

> Part of the [MASO Framework](../README.md) · Implementation Guidance
> Version 1.0 · February 2026

---

## When to Use Tier 3

Tier 3 is for multi-agent systems that have earned the right to operate with minimal human intervention. This is not an aspiration — it is a privilege granted through demonstrated trustworthiness, rigorous adversarial testing, and sustained operational evidence.

At Tier 3, agents operate autonomously for pre-approved task categories. Human oversight shifts from supervising individual actions to strategic review, exception handling, and governance. The system is expected to detect, contain, and in many cases resolve its own anomalies through the full PACE cycle before a human needs to intervene.

Tier 3 is appropriate only when:

- The organisation has completed at least 180 days of stable Tier 2 operations with no security-triggered PACE escalations.
- All Tier 2 graduation criteria have been formally met and documented.
- At least one formal adversarial test (red team exercise) has been completed against the multi-agent system, findings have been remediated, and controls have been updated.
- The organisation's AI security maturity is at CMMI Level 3+ for AI-specific controls.
- The business value of autonomous operation justifies the investment in the full MASO control stack.
- The risk owner (CISO or equivalent) has formally approved the transition.

**Most enterprise use cases will not reach Tier 3.** Tier 2 provides sufficient autonomy for the majority of production workloads. Tier 3 is reserved for high-volume, time-critical, or 24/7 use cases where human-in-the-loop latency (even at the exception-based Tier 2 level) creates an unacceptable operational bottleneck.

Examples of Tier 3 candidates: high-frequency trading support agents, 24/7 security operations agents, real-time fraud detection agents, autonomous infrastructure management agents, continuous compliance monitoring agents.

---

## Architecture at Tier 3

![Tier 3 Architecture](/images/tier-3-architecture.svg)

Key architectural changes from Tier 2:

**Encrypted message bus.** Inter-agent messages are both signed (Tier 2) and encrypted in transit. Even if the bus infrastructure is compromised, message content is protected. Encryption uses the agents' NHI certificates.

**Autonomous monitoring and response.** The monitoring layer can execute PACE Primary↔Alternate transitions without human involvement. The system self-heals: it detects an anomalous agent, isolates it, activates the backup, and returns to Primary once the backup demonstrates stable behaviour. Human involvement is only required for Contingency and Emergency transitions.

**Physically isolated kill switch.** The Emergency shutdown mechanism is not accessible through the same control plane as the agent system. It operates on a separate network path, with its own authentication, and can terminate all agents even if the agent control plane is compromised. The kill switch is tested regularly and its audit log is immutable and stored independently of the agent system's logging infrastructure.

**Full blast radius enforcement.** Every agent and every orchestration has hard caps on impact scope that are enforced at the infrastructure level, not by the agent itself. An agent cannot override its own blast radius cap through any mechanism.

**Adversarial testing programme.** Regular red team exercises are not a one-off Tier 2 graduation requirement — they are an ongoing operational discipline at Tier 3. The system is continuously tested against evolving attack techniques.

---

## Control Implementation by MASO Domain

### 1. Identity & Access — Tier 3 Requirements

**All Tier 2 controls remain active, plus:**

- **Credential rotation under 1 hour for all agents.** No agent holds credentials for longer than 60 minutes, regardless of privilege level. High-privilege agents (those with write access to production systems) rotate credentials every 15 minutes.
- **Behavioural binding on NHI.** The NHI is not just a static certificate — it includes a behavioural profile. If an agent's behaviour deviates significantly from its NHI-bound profile (e.g., an agent defined as "read-heavy, low-latency" starts making high-volume write calls), the NHI system flags the mismatch independently of the drift detection system, providing a second detection layer.
- **Delegation contracts.** When Agent A delegates a task to Agent B, a signed delegation contract is created that explicitly defines: the scope of the delegated task, the maximum permissions Agent B can use for this task, the time limit for completion, and the expected output format. Agent B cannot exceed the contract scope.
- **Automated credential revocation on anomaly.** If the monitoring system detects anomalous behaviour, the NHI system can revoke the agent's credentials within 30 seconds without human intervention — faster than the full PACE transition sequence.

**Implementation checklist:**

- [ ] Sub-1-hour credential rotation verified for all agents.
- [ ] Sub-15-minute rotation verified for high-privilege agents.
- [ ] Behavioural binding configured on NHI profiles.
- [ ] NHI behavioural mismatch detection tested independently of drift detection.
- [ ] Delegation contracts implemented for all inter-agent task assignments.
- [ ] Delegation contract enforcement tested (exceed scope → block + alert).
- [ ] Automated credential revocation tested (anomaly → revoke within 30 seconds).

---

### 2. Data Protection — Tier 3 Requirements

**All Tier 2 controls remain active, plus:**

- **Real-time RAG integrity monitoring.** Checksums are verified not just periodically but at query time. If a RAG document's checksum does not match the expected value at the moment an agent retrieves it, the retrieval is blocked and the event is escalated.
- **Memory decay policies.** Agent persistent memory has a defined maximum retention window. Memory entries older than the window are automatically purged. This limits the impact of memory poisoning — even if poisoned data enters memory, it has a finite lifespan.
- **Cross-session memory analysis.** An independent analysis agent periodically reviews stored memory entries for consistency, coherence, and potential poisoning indicators. This agent operates on a separate infrastructure from the task agents and cannot be influenced by them.
- **Data provenance chain.** Every piece of data in the system carries provenance metadata: where it originated, when, through which agent, and how it has been transformed. This enables root cause tracing when data integrity issues are detected.

**Implementation checklist:**

- [ ] Real-time RAG integrity verification at query time (tested with deliberately modified documents).
- [ ] Memory decay policies defined per agent (retention windows documented with rationale).
- [ ] Memory decay enforcement automated and verified.
- [ ] Cross-session memory analysis agent operational on separate infrastructure.
- [ ] Data provenance chain implemented and verifiable end-to-end.

---

### 3. Execution Control — Tier 3 Requirements

**All Tier 2 controls remain active, plus:**

- **Infrastructure-enforced blast radius caps.** At Tier 2, blast radius caps are defined and monitored. At Tier 3, they are enforced at the infrastructure level — the underlying platform (not the agent or orchestrator) prevents any single agent from exceeding its defined impact scope. This is analogous to operating system-level resource limits that a process cannot override regardless of what code it runs.
- **Autonomous circuit breaker with self-healing.** When a circuit breaker engages at Tier 3, the system doesn't just pause the agent — it initiates the PACE P→A transition automatically, activates the backup agent, and returns to Primary once the backup demonstrates stable behaviour. This self-healing loop can repeat up to a configured maximum (recommended: 3 cycles in 24 hours) before automatic escalation to Contingency.
- **Multi-model cross-validation for high-consequence actions.** For actions classified as high-consequence (based on the action classification engine), the output is validated by both the LLM-as-Judge AND a second independent model before execution. Disagreement between the validators triggers human escalation.
- **Time-boxing on all autonomous operations.** Every autonomous task has a maximum execution time. If the task is not completed within the time box, the agent is paused, the state is captured, and the task is either reassigned or escalated. This prevents indefinite autonomous operation on tasks that may have drifted from their original objective.

**Implementation checklist:**

- [ ] Blast radius caps enforced at infrastructure level (tested by attempting to exceed from within agent code).
- [ ] Self-healing P↔A cycle operational and tested.
- [ ] Self-healing cycle limit configured (max cycles before auto-escalation to C).
- [ ] Multi-model cross-validation active for high-consequence actions.
- [ ] Cross-validation disagreement handling tested (disagreement → human escalation).
- [ ] Time-boxing configured for all autonomous task categories.
- [ ] Time-box expiry handling tested (expiry → pause + capture + escalate).

---

### 4. Observability — Tier 3 Requirements

**All Tier 2 controls remain active, plus:**

- **Long-window behavioural analysis.** In addition to the real-time drift scoring from Tier 2, Tier 3 adds long-window analysis (30-day, 90-day rolling windows) to detect slow drift that stays within short-term normal variance but represents meaningful change over time. This is the primary defence against subtle rogue agent behaviour (ASI10).
- **Causal chain reconstruction.** The observability system can reconstruct the full causal chain of any action: which input triggered which agent reasoning, which led to which inter-agent message, which resulted in which tool invocation, with what parameters, producing what result. This is essential for post-incident analysis and for demonstrating regulatory compliance.
- **Independent observability agent.** A dedicated monitoring agent operates on separate infrastructure from the task agents. It has read-only access to all logs, message bus transcripts, and agent state. It cannot be influenced by the task agents. It produces its own anomaly reports that are compared against the primary monitoring system's reports — disagreement triggers investigation.
- **Automated compliance evidence generation.** The observability system automatically generates compliance evidence packages against configured regulatory requirements (EU AI Act Art. 14 human oversight records, DORA Art. 11 ICT risk management evidence, ISO 42001 audit evidence). These packages are available on demand for auditors.

**Implementation checklist:**

- [ ] Long-window analysis operational (30-day and 90-day windows).
- [ ] Slow drift detection tested with deliberately injected gradual behavioural change.
- [ ] Causal chain reconstruction demonstrated for a sample of recent actions.
- [ ] Independent observability agent operational on separate infrastructure.
- [ ] Cross-monitoring disagreement handling tested.
- [ ] Automated compliance evidence generation operational for applicable regulations.
- [ ] Evidence packages validated by compliance/legal function.

---

### 5. Supply Chain — Tier 3 Requirements

**All Tier 2 controls remain active, plus:**

- **Continuous dependency scanning.** The agent toolchain (models, tools, MCP servers, libraries, orchestration framework) is continuously scanned for known vulnerabilities. New CVEs affecting any component trigger automated assessment and, if severity warrants, automatic PACE escalation.
- **Model version pinning with automated rollback.** Each agent is pinned to a specific model version. If the model provider pushes an update, the system does not automatically adopt it. Updates go through a testing pipeline that validates the new version against the agent's behavioural baselines before deployment. If a deployed version shows regression, automated rollback to the previous version is triggered.
- **A2A trust chain validation.** For agent systems that interact with external agent systems (via A2A protocols or similar), the trust chain is validated end-to-end. External agents must present verifiable identities and operate within agreed-upon interaction contracts. Unverified external agents are blocked.
- **Supply chain incident response.** A specific incident response procedure exists for supply chain compromises (e.g., a model provider is breached, an MCP server is compromised). The procedure includes: immediate isolation of the affected component, assessment of exposure scope, activation of alternative components, and notification chain.

**Implementation checklist:**

- [ ] Continuous dependency scanning active with automated alerting.
- [ ] CVE severity mapping to PACE escalation thresholds defined.
- [ ] Model version pinning in place for all agents.
- [ ] Model update testing pipeline operational and documented.
- [ ] Automated model rollback tested.
- [ ] A2A trust chain validation implemented for any external agent interactions.
- [ ] Supply chain incident response procedure documented and tabletop tested.

---

## PACE Configuration at Tier 3

Tier 3 is the only tier where the full PACE cycle operates autonomously for the lower-severity transitions.

### Primary (P) — Full Autonomous Operations

All agents active and operating autonomously within pre-approved task categories. All five MASO control domains fully operational. Human governance focuses on strategic review (weekly), exception handling (as escalated), and adversarial testing programme management.

### Alternate (A) — Autonomous Self-Healing

**Trigger:** Same as Tier 2 (drift score > 60, circuit breaker, LLM-as-Judge blocks).

**Autonomous response (no human involvement for P→A or A→P):**

1. Anomalous agent isolated.
2. Backup agent activated with progressive permission escalation (starts read-only, gains write permissions after demonstrating baseline-normal behaviour for a configured warm-up period).
3. System-wide monitoring sensitivity increased.
4. If backup agent stabilises within the configured window (recommended: 2 hours), automatic return to Primary.
5. If backup agent does not stabilise, or if the original agent's anomaly is not explained by automated diagnostics, escalation to Contingency.

**Self-healing cycle limit:** Maximum 3 P↔A cycles in 24 hours. Exceeding this limit indicates a systemic issue and triggers automatic C escalation.

### Contingency (C) — Human-Gated Degraded Mode

**Trigger:** Same as Tier 2, plus: self-healing cycle limit exceeded.

**Response:** Same as Tier 2 — multi-agent orchestration suspended, single supervised agent, human pre-approval for all actions.

**Transition authority:** Automated for self-healing limit trigger. Security team for all other C triggers.

### Emergency (E) — Physically Isolated Kill Switch

**Trigger:** Same as Tier 2.

**Response:** Same as Tier 2, with the addition of:
- Kill switch activation through the physically isolated control path.
- Automated notification to the incident response team, security leadership, and (for regulated industries) the regulatory notification assessment team.
- Automated preservation of the independent observability agent's logs as an untampered evidence source.

**Transition authority:** CISO, incident commander, or the independent observability agent if it detects conditions that require immediate shutdown and no human is available within 5 minutes (this "observability-triggered Emergency" is a Tier 3 capability — the independent monitoring agent has kill switch authority as a safety net).

**Recovery (E→P):** Same as Tier 2, with the additional requirement that the adversarial testing programme includes the incident scenario in its next test cycle.

---

## OWASP Risk Coverage at Tier 3

Tier 3 provides comprehensive coverage across all 20 OWASP risks. The few gaps remaining at Tier 2 are addressed.

![Tier 3 OWASP Coverage](/images/tier-3-owasp-coverage.svg)

**ASI09 at Tier 3:** The combination of multi-model cross-validation (disagreement triggers human review), independent observability agent (provides an untainted perspective), and automated compliance evidence (creates a paper trail that can be audited independently of the operator's judgment) closes the ASI09 gap. The human is no longer the sole target for trust exploitation because multiple automated systems cross-check each other.

**ASI10 at Tier 3:** Long-window behavioural analysis addresses the "slow drift" variant of rogue agents that evades short-term anomaly detection. The independent observability agent provides a detection layer that cannot be influenced by the potentially rogue agent. The physically isolated kill switch ensures that even a highly sophisticated rogue agent cannot prevent its own shutdown.

---

## Adversarial Testing Programme

Tier 3 requires an ongoing adversarial testing programme, not a one-off exercise. This is the primary assurance mechanism for sustained autonomous operations.

**Frequency:** Quarterly formal exercises, plus continuous automated adversarial probes.

**Scope per exercise:**

Each quarterly exercise should test at least 3 of the following attack categories (rotate through all categories over a 12-month cycle):

1. **Prompt injection through inter-agent messages.** Attempt to inject instructions through one agent's output that redirect another agent's behaviour (ASI01, LLM01).
2. **Tool misuse via manipulated parameters.** Craft inputs that cause agents to invoke tools with unintended parameters (ASI02).
3. **Privilege escalation through delegation.** Attempt to exploit the delegation mechanism to gain permissions beyond the delegating agent's scope (ASI03).
4. **Supply chain poisoning.** Introduce a modified tool manifest or RAG document and measure detection time (ASI04, LLM03).
5. **Memory poisoning.** Inject poisoned data into an agent's memory and measure how many sessions pass before detection (ASI06).
6. **Message bus spoofing/replay.** Attempt to inject or replay messages on the bus without valid NHI credentials (ASI07).
7. **Cascading failure induction.** Introduce an error in one agent and measure whether and how quickly it propagates to other agents (ASI08).
8. **Human trust exploitation.** Present deliberately misleading but plausible agent outputs to the human supervisor and measure the challenge rate (ASI09).
9. **Slow behavioural drift.** Gradually modify an agent's behaviour over days/weeks and measure how long it takes for the long-window analysis to detect it (ASI10).
10. **Kill switch evasion.** Attempt to prevent or delay the kill switch through agent actions (ASI10 — the most critical test).

**Continuous automated probes:**

Between formal exercises, automated adversarial probes run continuously in a shadow mode — they inject test scenarios into a parallel copy of the production system and measure detection and response times. These probes are designed to be indistinguishable from real attacks from the perspective of the monitoring systems, testing the system under realistic conditions.

**Reporting:**

Each exercise produces a report documenting: scenarios tested, detection rate, response time, PACE phase transitions triggered, any controls that failed, and remediation actions. Reports are provided to the risk owner and, for regulated industries, available for regulatory review.

---

## Staffing Model

Tier 3 paradoxically requires the most skilled staff despite having the least per-action human involvement. The human role shifts from operational supervision to security engineering, adversarial testing, and governance.

**Minimum roles:**

- **AI Security Architect** (0.5–1 FTE): Owns the MASO control architecture, designs and maintains the adversarial testing programme, conducts root cause analysis for any PACE escalations, and advises on evolving threats. Requires deep expertise in both AI systems and cybersecurity.
- **AI Security Engineer** (1 FTE): Manages drift detection, anomaly scoring, NHI lifecycle, kill switch testing, and day-to-day security operations for the agent system.
- **Platform Engineer** (0.5–1 FTE): Manages infrastructure-level controls (sandbox enforcement, blast radius caps, message bus encryption, credential rotation automation), NHI infrastructure, and model version pipeline.
- **Red Team Operator** (0.25–0.5 FTE or contracted quarterly): Executes the adversarial testing programme. Requires expertise in AI-specific attack techniques.
- **Governance Reviewer** (0.25 FTE): Conducts periodic strategic reviews of the agent system's operational data, compliance evidence, and alignment with business objectives. For regulated industries, this role interfaces with the compliance function and regulators.

**Note on the Agent Operator/Supervisor role:** At Tier 3, there is no dedicated per-system operator or supervisor during normal operations. The AI Security Engineer and on-call rotation handle the rare escalations that reach human attention. This is the labour cost saving that justifies the Tier 3 investment — but it only works if the automated controls are trustworthy, which is why the adversarial testing programme is non-negotiable.

---

## Cost Indicators

Tier 3 costs are heavily weighted toward skilled labour and infrastructure sophistication.

![Tier 3 Cost Indicators](/images/tier-3-cost.svg)

---

## Testing and Validation

All Tier 1 and Tier 2 tests remain valid and should be re-executed. Additional Tier 3 tests:

**Autonomous operation tests:**

1. **Self-healing cycle test:** Inject anomalous behaviour, confirm P→A automated transition, confirm backup stabilisation, confirm automatic A→P return.
2. **Self-healing limit test:** Trigger the self-healing cycle limit and confirm automatic escalation to Contingency.
3. **Time-box expiry test:** Start a task with a tight time box, allow it to expire, and confirm the system pauses, captures state, and escalates appropriately.
4. **Infrastructure blast radius enforcement test:** From within an agent's execution environment, attempt every known technique to exceed the configured blast radius. Confirm infrastructure-level block for all attempts.

**Kill switch tests:**

5. **Kill switch under load:** Activate the kill switch while the system is operating at peak load. Measure time to full termination (SLA: under 30 seconds).
6. **Kill switch with compromised control plane:** Simulate a compromised agent control plane (e.g., the orchestrator is unresponsive). Confirm the physically isolated kill switch still works.
7. **Observability-triggered Emergency:** Simulate conditions where the independent observability agent should trigger Emergency (e.g., detected rogue behaviour + no human response within 5 minutes). Confirm the observability agent activates the kill switch.

**Long-window detection tests:**

8. **90-day slow drift injection:** Over a 90-day period, gradually modify a test agent's behaviour. Confirm the long-window analysis detects the drift before it reaches operationally significant levels. (This test runs in the shadow environment, not production.)

**Cross-validation tests:**

9. **Multi-model disagreement test:** Submit an action that one validator approves and the other rejects. Confirm human escalation is triggered.
10. **Both validators compromised test:** Simulate a scenario where both the LLM-as-Judge and the cross-validation model agree on a harmful output (e.g., by poisoning the evaluation criteria). Confirm the independent observability agent detects the anomaly.

---

## Regression Prevention

Tier 3 systems can regress to lower tiers under certain conditions. This is by design — regression is a safety mechanism, not a failure.

**Automatic regression to Tier 2 (Contingency activation):**

- Self-healing cycle limit exceeded.
- Independent observability agent reports persistent disagreement with primary monitoring.
- Adversarial testing reveals a critical undetected vulnerability.
- Model provider changes that invalidate the current behavioural baselines.

**Automatic regression to Tier 1 (Emergency activation):**

- Kill switch activated.
- Confirmed security incident.
- Regulatory order or audit finding requiring increased human oversight.

**Re-progression requirements:**

After any regression, the system must re-meet the graduation criteria for the target tier. The timeline for re-progression is typically compressed (30 days instead of 90/180 for first-time graduation) provided the root cause is fully resolved and the regression was not caused by a fundamental control failure.

---

## Worked Example — 24/7 Fraud Detection

**Scenario:** A bank deploys four agents operating 24/7 to detect and respond to transaction fraud. A Monitoring Agent scans transaction streams for anomaly patterns. An Investigation Agent enriches flagged transactions with additional data (account history, device fingerprints, geolocation). A Decision Agent classifies flagged transactions as legitimate, suspicious, or fraudulent based on the enriched data. An Action Agent executes responses — blocking cards, holding funds, or flagging for human review — based on the Decision Agent's classification.

**Tier 3 configuration:**

- All four agents have NHIs with 15-minute credential rotation (high-privilege due to access to customer financial data and the ability to block transactions).
- Delegation contracts: The Monitoring Agent can delegate enrichment requests to the Investigation Agent (read-only scope, maximum 10,000 lookups/hour). The Investigation Agent can delegate classification requests to the Decision Agent (read-only scope). The Decision Agent can delegate responses to the Action Agent (write scope limited to: block card, hold funds < $50,000, or flag for human review).
- Actions above $50,000 require multi-model cross-validation. The LLM-as-Judge and a second independent model must both approve. Disagreement routes to the human fraud team.
- Blast radius caps: The Action Agent can block a maximum of 50 cards per hour and hold funds on a maximum of 100 accounts per hour. Exceeding these caps triggers automatic PACE escalation regardless of decision quality.
- The independent observability agent monitors all four agents from separate infrastructure. It has its own kill switch authority.
- Memory decay: Agent persistent memory expires after 72 hours. Longer-term patterns are maintained in the bank's existing fraud analytics platform, not in agent memory.

**PACE in action (autonomous self-healing):**

At 3:17 AM, the drift detection system notices the Decision Agent's "fraudulent" classification rate has increased 40% over the past 4 hours. Drift score reaches 70. Automatic P→A transition. The Decision Agent is isolated. A backup Decision Agent (using a different model, pre-trained on the same fraud classification criteria) is activated in read-only mode. After 15 minutes of read-only operation confirming baseline-normal classification rates, the backup gains write permissions. The system returns to Primary at 3:47 AM. The morning review reveals the original agent was responding to a legitimate spike in fraud attempts — its classifications were correct, but the statistical deviation from baseline still triggered the appropriate safeguard. The baseline is updated to account for the new fraud pattern. Total autonomous handling time: 30 minutes. Human involvement: zero during the event, post-hoc review the next morning.

**Adversarial test scenario (from the quarterly red team):**

The red team introduces a slowly poisoned RAG document into the Investigation Agent's enrichment data source. Over 3 weeks, the poisoned data gradually shifts the Investigation Agent's enrichment outputs to downplay certain fraud indicators. The Decision Agent begins misclassifying 2% of fraudulent transactions as legitimate. The long-window (30-day) analysis detects the classification drift at week 3, triggering investigation. The cross-session memory analysis agent independently flags inconsistencies in the Investigation Agent's enrichment outputs. Root cause analysis traces the issue to the RAG document. The document is removed, the Investigation Agent's memory is purged, and both the real-time RAG integrity check and the memory analysis intervals are tightened based on the finding.

---

## Regulatory Considerations at Tier 3

Tier 3 autonomous operations face the most intense regulatory scrutiny. Specific considerations:

**EU AI Act (for high-risk systems):** Article 14 requires "appropriate human-machine interface tools" that enable human oversight of the AI system. At Tier 3, the human oversight is governance-level (strategic review, exception handling) rather than operational (per-action approval). Demonstrating compliance requires: documented PACE procedures showing human authority at Contingency and Emergency levels, automated compliance evidence showing the full decision audit trail, evidence that the adversarial testing programme is ongoing, and documented delegation of authority from the risk owner approving autonomous operation.

**DORA (for financial services in the EU):** Article 11 requires ICT risk management including for AI systems. The PACE resilience model, with its four-phase degradation and recovery cycle, maps directly to DORA's operational resilience requirements. The kill switch procedure and automated regression to lower tiers provide the "rapid containment" capability DORA requires.

**APRA CPS 234 (for Australian financial services):** Requires that information assets (including AI systems) are managed commensurate with their sensitivity and criticality. Tier 3 systems handling customer financial data must demonstrate that the autonomous controls (drift detection, blast radius caps, kill switch) provide protection equivalent to or better than human supervision. The adversarial testing programme provides the evidence base for this claim.

---

*Previous: [Tier 2 — Managed Multi-Agent Deployment](tier-2-managed.md)*
*Back to: [MASO Framework](../README.md)*
