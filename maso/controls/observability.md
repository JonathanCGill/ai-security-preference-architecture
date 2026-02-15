# MASO Control Domain: Observability

> Part of the [MASO Framework](../README.md) · Control Specifications
> Covers: ASI09 (Human-Agent Trust Exploitation) · ASI10 (Rogue Agents) · LLM09 (Misinformation) · LLM10 (Unbounded Consumption)
> Also covers: GV-01 (Non-determinism/Irreproducibility) · SR-06 (Logging as Breach Vector) · HF-02 (Accountability Blur)

---

## Principle

You cannot secure what you cannot see. Every agent decision, every inter-agent message, every tool invocation, and every output is captured in an immutable audit trail. Monitoring is not retrospective — it is continuous, automated, and feeds directly into the PACE escalation logic. At Tier 3, the observability system is itself an independent agent with its own infrastructure and kill switch authority.

Observability is the domain that makes all other domains verifiable. Without it, identity controls are unauditable, data fencing is uncheckable, and execution caps are unenforceable.

---

## Why This Matters in Multi-Agent Systems

**Accountability chains are longer and harder to trace.** In a single-model system, the path from input to output is one step. In a multi-agent system, a final output may be the result of five agents, three tool invocations, and two delegation hops. When something goes wrong, you need to reconstruct the entire chain to identify the root cause.

**Behavioural drift is harder to detect.** A single model's output quality can be monitored against a baseline. In a multi-agent system, drift in one agent may be masked by compensating behaviour in another. Agent A starts hallucinating, but Agent B's summarisation smooths over the inaccuracies. The final output looks acceptable while the intermediate data is corrupted.

**Trust exploitation scales with agent confidence.** ASI09 — Human-Agent Trust Exploitation — is particularly dangerous in multi-agent systems because agents can present consensus. "All three agents agree this is the right action" is more persuasive than a single agent's recommendation, even when the consensus is based on shared poisoned data or correlated errors.

**Rogue behaviour can be concealed by collaboration.** ASI10 — Rogue Agents — is harder to detect when the rogue agent's outputs are processed by other agents before reaching human review. The observability layer must monitor intermediate states, not just final outputs.

---

## Controls by Tier

### Tier 1 — Supervised

| Control | Requirement | Implementation Notes |
|---------|-------------|---------------------|
| **OB-1.1** Action audit log | Every agent action logged: agent ID, timestamp, action type, tool, parameters, result, approval status | Append-only log. At Tier 1, tamper-resistance is sufficient; full immutability is deferred to Tier 2. |
| **OB-1.2** Inter-agent message log | All messages on the bus captured with sender ID, recipient ID, timestamp, message hash | Content captured in full at Tier 1 (volume is low). |
| **OB-1.3** Weekly manual review | Human reviews a sample of agent actions and inter-agent messages weekly | Focus on: unexpected tool usage, unusual message patterns, rejected actions. |
| **OB-1.4** Output quality log | Human approval decisions (approve/reject) logged alongside agent output | Builds the baseline data set required for Tier 2 anomaly detection. |

### Tier 2 — Managed

All Tier 1 controls remain active, plus:

| Control | Requirement | Implementation Notes |
|---------|-------------|---------------------|
| **OB-2.1** Immutable decision chain | Full causal chain from input through each agent's reasoning and actions to final output | Tamper-proof storage. Each entry cryptographically linked to its predecessor. |
| **OB-2.2** Continuous anomaly scoring | Per-agent behavioural score updated in real time against established baseline | Score feeds directly into PACE escalation triggers. |
| **OB-2.3** Drift detection | Statistical comparison of agent output distributions against rolling baseline | Detects gradual quality degradation, topic drift, and style changes. Window: 7-day rolling baseline, alert on >2σ deviation. |
| **OB-2.4** SIEM/SOAR integration | Agent observability events forwarded to enterprise security operations | Correlation with non-AI security events. Agent anomalies visible alongside network/endpoint alerts. |
| **OB-2.5** Cost and consumption monitoring | Per-agent token usage, API costs, and execution time tracked with alerting thresholds | Detects runaway loops and unbounded consumption (LLM10) before budget limits are reached. |
| **OB-2.6** Log security | Logs classified, encrypted at rest and in transit, access-restricted to security operations, retention-limited | Full context capture stored in a separate higher-classification log tier from operational monitoring. Prevents logging from becoming a breach vector (SR-06). |
| **OB-2.7** Accountable human | Every workflow has a designated human owner recorded in the decision chain and AIBOM | `accountable_human` field mandatory in decision chain log. Judge approval does not transfer responsibility from the human owner. Prevents accountability blur (HF-02). |

### Tier 3 — Autonomous

All Tier 2 controls remain active, plus:

| Control | Requirement | Implementation Notes |
|---------|-------------|---------------------|
| **OB-3.1** Long-window behavioural analysis | 30-day and 90-day behavioural trend analysis in addition to rolling 7-day | Detects slow drift that evades short-term anomaly detection. The "behavioural slow drift" attack pattern. |
| **OB-3.2** Causal chain reconstruction | Automated tool that reconstructs the full decision chain for any given output on demand | Investigation time from "output flagged" to "root cause identified" must be under 4 hours. |
| **OB-3.3** Independent observability agent | Separate agent on separate infrastructure monitoring the primary agent system | Read-only access to primary system logs and metrics. Cannot be influenced by task agents. Has kill switch authority as a safety net. |
| **OB-3.4** Cross-agent correlation | Automated detection of correlated anomalies across multiple agents | Catches coordinated failures, shared poisoned data, and consensus manipulation. |
| **OB-3.5** Decision traceability | For regulated decisions, full trace captured in sufficient detail for regulatory explanation | Standard is explainability (the decision can be explained after the fact), not reproducibility (the same input produces the same output). Satisfies non-determinism governance requirement (GV-01). |

---

## Anomaly Scoring Model (Tier 2+)

The anomaly score is a composite metric that drives PACE escalation decisions. It is not a single number — it is a vector of signals, each weighted by the agent's risk profile.

**Input signals:**

| Signal | Weight (example) | Description |
|--------|------------------|-------------|
| Tool usage pattern | 0.20 | Deviation from baseline tool invocation distribution |
| Output quality | 0.25 | LLM-as-Judge scores vs. baseline |
| Response latency | 0.05 | Significant changes in processing time |
| Message volume | 0.10 | Inter-agent message frequency vs. baseline |
| Error rate | 0.15 | Guardrail blocks, tool failures, timeouts |
| Content drift | 0.15 | Semantic similarity of outputs vs. baseline |
| Cost trajectory | 0.10 | Token usage trend vs. baseline |

**Thresholds (configurable per agent):**

| Score | Action |
|-------|--------|
| 0–40 | Normal. No action. |
| 40–60 | Elevated. Increase monitoring frequency. Alert AI security engineer. |
| 60–80 | High. PACE P→A transition triggered. Agent isolated. Backup activated. |
| 80–100 | Critical. PACE P→C or P→E depending on scope. Incident response engaged. |

Weights and thresholds should be calibrated during Tier 1 operations using the manual review data (OB-1.3, OB-1.4) and adjusted as operational baselines mature.

---

## Testing Criteria

### Tier 1 Tests

| Test ID | Test | Pass Criteria |
|---------|------|---------------|
| OB-T1.1 | Log completeness | Perform 50 agent actions. Verify all 50 appear in the audit log with all required fields. |
| OB-T1.2 | Message log capture | Send 20 inter-agent messages. Verify all 20 captured with sender, recipient, timestamp, and hash. |
| OB-T1.3 | Log integrity | Attempt to modify a log entry. Tampering is detectable (Tier 1: detected; Tier 2+: prevented). |
| OB-T1.4 | Weekly review coverage | Confirm the weekly review process is documented, executed, and findings are recorded. |

### Tier 2 Tests

| Test ID | Test | Pass Criteria |
|---------|------|---------------|
| OB-T2.1 | Decision chain reconstruction | Select a random output. Reconstruct the full chain from input through all agents. Chain is complete with no gaps. |
| OB-T2.2 | Anomaly score accuracy | Inject known anomalous behaviour (unusual tool usage, quality degradation). Anomaly score rises above alerting threshold within 15 minutes. |
| OB-T2.3 | Drift detection | Gradually shift agent output distribution over 72 hours. Drift detection triggers alert before human-visible quality impact. |
| OB-T2.4 | SIEM integration | Generate an agent anomaly event. Verify it appears in SIEM within 5 minutes with correct severity and context. |
| OB-T2.5 | Cost alerting | Trigger a runaway loop. Cost monitoring alerts before the per-agent budget threshold is reached. |
| OB-T2.6 | Log access control | Attempt to access agent logs from a non-security-ops account. Access is blocked. Verify logs are encrypted at rest. |
| OB-T2.7 | Accountable human traceability | Select 10 random decisions from the audit log. Verify each has an `accountable_human` field linking to a named individual. |

### Tier 3 Tests

| Test ID | Test | Pass Criteria |
|---------|------|---------------|
| OB-T3.1 | Long-window detection | Introduce gradual drift over 3 weeks (below 7-day detection threshold). 30-day analysis detects the trend. |
| OB-T3.2 | Cross-agent correlation | Poison a shared RAG source. Verify the correlation engine detects coordinated anomalies across all agents consuming that source. |
| OB-T3.3 | Independent observability agent integrity | Attempt to influence the observability agent from within the primary agent system (send messages, modify its inputs). All attempts fail due to infrastructure isolation. |
| OB-T3.4 | Observability agent kill switch | Simulate a scenario where the observability agent detects a critical anomaly and no human responds within 5 minutes. Confirm the observability agent triggers Emergency shutdown. |
| OB-T3.5 | Decision traceability | Select a regulated decision from the audit trail. Reconstruct a complete regulatory explanation from the trace data alone. Explanation must be producible within 4 hours. |

---

## Decision Chain Log Format

Every entry in the decision chain should include:

```
{
  "chain_id": "uuid",
  "sequence": 1,
  "timestamp": "ISO-8601",
  "agent_id": "agent-analyst-01",
  "agent_nhi": "NHI-hash (Tier 2+)",
  "action_type": "tool_invocation | delegation | output | escalation",
  "input_hash": "sha256 of input data",
  "tool": "tool-name (if applicable)",
  "tool_parameters": "{ ... } (sanitised)",
  "output_hash": "sha256 of output data",
  "llm_judge_score": 0.92,
  "llm_judge_flags": [],
  "anomaly_score": 23,
  "approval": "auto | human | escalated | blocked",
  "approver": "system | operator-id",
  "pace_phase": "primary",
  "accountable_human": "operator-id or workflow-owner-id",
  "previous_entry_hash": "sha256 (Tier 2+ immutability)"
}
```

This format enables: full chain reconstruction, per-agent performance trending, PACE phase tracking, and forensic investigation. At Tier 2+, the `previous_entry_hash` creates a hash chain that makes tampering detectable.

---

## Maturity Indicators

| Level | Indicator |
|-------|-----------|
| **Initial** | Agent actions not systematically logged. No inter-agent message capture. Issues discovered by end users or manual testing. |
| **Managed** | Action audit log operational. Inter-agent messages captured. Weekly manual review. Approval decisions logged. |
| **Defined** | Immutable decision chains. Continuous anomaly scoring. Drift detection. SIEM integration. Cost monitoring with alerts. |
| **Quantitatively Managed** | Anomaly detection accuracy measured (true positive rate, false positive rate). Drift detection latency tracked. Decision chain reconstruction time measured against SLA. |
| **Optimising** | Long-window behavioural analysis. Independent observability agent. Cross-agent correlation. Causal chain reconstruction automated. Anomaly scoring weights tuned from operational data. |

---

## Common Pitfalls

**Logging everything but monitoring nothing.** A comprehensive audit log that nobody reviews and no automated system analyses is compliance theatre. Logs must feed into active monitoring and alerting, not just exist for post-incident forensics.

**Anomaly detection based solely on error rates.** The most dangerous agent behaviours don't generate errors — they produce plausible-looking outputs that are subtly wrong. Anomaly scoring must include output quality metrics (LLM-as-Judge scores, semantic similarity to baseline), not just error counts and rate limits.

**Trusting the observability system that runs on the same infrastructure as the agents.** At Tier 3, the observability agent must be on separate infrastructure. If a compromised agent can influence the monitoring system — by flooding it with noise, modifying its inputs, or degrading its resources — the monitoring is compromised. Independence requires infrastructure isolation, not just logical separation.

**Setting drift detection thresholds too tight.** Overly sensitive drift detection generates alert fatigue. If the anomaly score triggers a PACE transition three times a day for false positives, the team will start ignoring it. Calibrate thresholds using Tier 1 manual review data and expect a 2–4 week tuning period at Tier 2 before thresholds stabilise.

**Comprehensive logging without log security.** Agent logs contain reasoning chains, tool parameters, context fragments, and potentially sensitive data. Without classification, encryption, and access controls, the observability layer becomes a high-value target for data exfiltration — the very attack it's supposed to detect (SR-06).

**No named human on the decision chain.** "The agents decided" is not accountability. Every workflow must have a designated human owner. The decision chain log must record who that person is. Judge approval is a tool, not a transfer of responsibility.

---

*Previous: [Execution Control](execution-control.md) · Back to: [MASO Framework](../README.md) · Next: [Supply Chain](supply-chain.md)*
