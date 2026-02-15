# MASO Emergent Risk Register

> Part of the [MASO Framework](../README.md) · Risk Analysis
> Version 1.0 · February 2026

---

## Review Findings

The source risk table identifies 29 emergent risks across eight categories. Comparison against the five MASO control domains and the OWASP dual mapping reveals three classes of coverage:

**MASO already strong (no new controls needed):** Cross-agent prompt injection, confused deputy, privilege escalation by delegation, tool-chain injection, role drift, goal drift, memory poisoning, provenance loss, cost blowouts. These map directly to existing MASO controls that are equal to or stronger than the source table's mitigations.

**MASO partially covers but needs amendment (8 amendments):** Secrets leakage, logging as breach vector, RAG poisoning, latency compounding, automation bias, accountability blur, role drift testing, goal drift judge criteria. The MASO controls address the attack vector but miss a specific dimension the source table identifies.

**Genuine gaps requiring new controls (15 new controls):** The entire epistemic category (8 risks), two coordination risks (deadlock/livelock, oscillation), both safety/misuse risks, both governance risks, and one operational risk (partial failure). These are emergent multi-agent failure modes with no direct OWASP equivalent. The source table's mitigations are sound starting points; MASO needs to formalise them.

The most significant finding: **the epistemic risks are the highest-priority gap.** A multi-agent system can fail catastrophically on groupthink, hallucination amplification, or uncertainty stripping with no external attacker present. Current MASO controls are oriented toward adversarial threats (OWASP) and operational resilience (PACE). They do not address information-processing failures between agents.

---

## Risk Table

### Epistemic Risks

No direct OWASP equivalent. These arise from agent interaction dynamics, not adversarial attack.

| ID | Risk | Scenario | Prevent | Detect | Judge/Challenger Role | MASO Status | Control |
|----|------|----------|---------|--------|----------------------|-------------|---------|
| EP-01 | **Groupthink / premature consensus** | Agents converge quickly on a plausible narrative. Dissent disappears. Human reviewer sees unanimous agreement and has no reason to challenge it. Amplifies ASI09 (trust exploitation). | Structured disagreement protocol: at least one agent assigned adversarial role with mandatory "produce a counterexample" step. Independent retrieval sources per agent. | Track agreement rate and output novelty across agents. Alert when convergence speed exceeds baseline. | Challenger agent attacks the primary hypothesis before commit. LLM-as-Judge refuses consensus if evidence diversity is below configured minimum. | **Gap.** OB-2.3 catches pattern changes over time. EC-2.5 provides enforcement point. No diversity requirement exists. | **NEW: EP-C01** Consensus diversity gate. Unanimous agreement with shared evidence sources triggers escalation, not approval. Tier 2+. |
| EP-02 | **Correlated errors** | Multiple agents produce the same wrong answer because they share the same model, training data, and retrieval corpus. Redundancy in compute, not in reasoning. | Model heterogeneity: different models (ideally different providers) for agents contributing to the same decision. Different prompt architectures. Independent retrieval sources. | Measure cross-agent output diversity via semantic similarity scoring. Low diversity on complex tasks is a warning. | Judge refuses consensus if evidence diversity is low. Judge must itself be a different model from task agents (already required by EC-2.5). | **Gap.** SC-2.1 (AIBOM) documents model composition, making homogeneity visible. EC-3.3 provides diversity at Tier 3. No requirement at Tier 2. | **NEW: EP-C02** Model diversity policy. Single-provider architectures flagged as concentration risk in AIBOM. Tier 2+. |
| EP-03 | **Hallucination amplification** | Agent A hallucinates a claim. It enters the message bus or shared memory. Agent B treats it as fact. By Agent C, the hallucination has been cited, elaborated, and presented with high confidence. | Treat agent-generated claims as untrusted by default. Claims entering shared state must carry provenance metadata: `{source: "tool\|rag\|agent-generated", verified: bool}`. Ungrounded claims flagged as unverified. | Fact-checking step before claims propagate. Retrieval verification: if a claim references a source, verify the source supports it. | Judge scores claim-evidence consistency. Ungrounded claims receive low confidence and trigger escalation for high-consequence decisions. | **Gap.** DP-3.4 (provenance chain) provides metadata infrastructure. No rule prevents unverified claims from being treated as facts downstream. | **NEW: EP-C03** Claim provenance enforcement. Claims marked `agent-generated` with `verified: false` cannot be treated as established facts by downstream agents. Tier 2+. |
| EP-04 | **Synthetic corroboration** | Agent A produces a claim. Agent B, asked to verify, retrieves Agent A's output from shared memory and reports "confirmed." One source masquerades as two. | Prohibit agent-to-agent citations as evidence. Verification must use primary sources: document IDs, tool outputs, external data. Message bus schema distinguishes "claim from Agent X" from "evidence from Source Y." | Audit trail flags self-referential evidence chains: if the provenance chain traces back to the same agent system, it is not corroboration. | Judge penalises claims supported only by other agents' outputs. Judge requires at least one primary source per material claim. | **Gap.** DP-3.4 provides tracing infrastructure. No rule prevents agents treating each other's outputs as authoritative evidence. | **NEW: EP-C04** Self-referential evidence prohibition. No agent may cite another agent's output as primary evidence. Judge rejects claims where all supporting evidence originates within the orchestration. Tier 2+. |
| EP-05 | **Semantic drift / summarisation loss** | As information passes through agent chains, precision degrades. "Must" becomes "should." "Never exceed 5%" becomes "keep low." Qualifiers vanish. Requirements soften. Final output is plausible but unfaithful to the original input. | Structured state (JSON schemas, typed fields) for requirements and constraints instead of free-text summaries. Critical values travel as structured data, not natural language. | Diff checks between original instruction/requirements and current plan state at each handoff. Flag when constraint keywords ("must," "never," "exactly") disappear. | Judge checks constraint preservation: compare original task specification against current working state and proposed output. Flag any constraint relaxed without authorisation. | **Gap.** OB-2.1 captures the chain. EC-2.1 classifies actions. No control enforces semantic fidelity across handoffs. | **NEW: EP-C05** Constraint fidelity check. For 3+ handoff chains, judge compares final output against original task for constraint preservation. Tier 2+. |
| EP-06 | **Uncertainty stripping** | Agent reports "this might be the case (70% confidence)." Next agent summarises as "this is the case." By the human reviewer, all uncertainty has been removed. Human approves based on false certainty. | Mandatory uncertainty fields in inter-agent message schema: `{confidence: float, assumptions: [], unknowns: []}`. Downstream agents must preserve or increase (never decrease) the uncertainty signal. | Monitor for uncertainty markers in upstream outputs that disappear in downstream outputs. | Judge fails any output presenting a claim with higher confidence than upstream evidence supports. | **Gap.** Message bus schema doesn't include uncertainty fields. Judge lacks explicit confidence-checking criteria. | **NEW: EP-C06** Uncertainty preservation. Message schema must include confidence, assumptions, and unknowns. Outputs to humans must include uncertainty metadata. Tier 2+. |
| EP-07 | **Planner–executor mismatch** | Planning agent produces a sound plan. Executing agent skips steps, reorders operations, substitutes tools, or interprets instructions loosely. Plan was reviewed; execution was not. | Strong contracts: plans in structured format (JSON with step IDs, tool refs, expected outputs). Executor maps steps 1:1 and attests to each step completion. | Runtime conformance check: compare executed actions against approved plan. Flag deviations (skipped steps, reordered operations, tool substitutions). | Judge compares executed steps against approved plan. Deviations trigger escalation before result is committed. | **Gap.** OB-2.1 captures what happened. EC-2.1 validates individual actions. No plan-conformance check exists. | **NEW: EP-C07** Plan-execution conformance. Execution validated against plan at step level. Deviations from approved plan require re-approval. Tier 2+. |
| EP-08 | **Hidden assumptions becoming global** | Agent makes a local assumption to complete its task ("assume the client is in the US for tax"). Assumption propagates through bus to other agents. By Agent C, it's treated as established fact. | Agents must make assumptions explicit and scoped. Tagged in messages with scope (local/task/global). Local assumptions must not propagate without human review. | Assumption register: log of all assumptions made by all agents, with scope tags. Review flags assumptions that propagated beyond original scope. | Judge flags unstated assumptions and checks whether stated assumptions are appropriate for task context. | **Gap.** Bus captures content but doesn't distinguish assumptions from facts. No assumption tracking. | **NEW: EP-C08** Assumption isolation. Message schema tags assumptions with scope. Local-scoped assumptions cannot propagate without explicit promotion. Tier 2+. |

---

### Coordination Risks

| ID | Risk | Scenario | Prevent | Detect | Judge/Challenger Role | MASO Status | Control |
|----|------|----------|---------|--------|----------------------|-------------|---------|
| CR-01 | **Deadlock / livelock** | Agents wait for each other indefinitely (deadlock) or negotiate endlessly without converging (livelock). System appears active but produces nothing useful. | Timeouts on all inter-agent interactions. Maximum turn caps on negotiation sequences. Deterministic arbitration: if agents cannot agree within N turns, orchestrator or judge decides. | Telemetry on message loop duration and turn count. Alert when interaction length exceeds 2× baseline. | Judge can act as arbiter in deadlocks, but this is a secondary control. The primary control is the timeout. | **Partial.** EC-3.4 (time-boxing) applies to task duration. EC-1.3 (rate limits) caps volume. No explicit deadlock/livelock detection. | **NEW: CR-C01** Interaction timeout. All negotiation sequences have a max turn count (recommended: 10). Exceeding cap triggers deterministic resolution. Tier 1+. |
| CR-02 | **Oscillation** | Decision flips repeatedly: A proposes X, B proposes Y, A switches to Y, B switches to X. Tokens and time consumed without convergence. | Decision protocol with commit points: once a decision passes LLM-as-Judge approval, it is committed. Reversal requires human authorisation (high-consequence) or documented input change (low-consequence). Tie-break rules for equal-weight alternatives. | Detect repeated reversals: same decision changes direction more than twice → flag as oscillation. | Judge can choose between options with defined criteria and enforce the decision. | **Gap.** EC-1.3 would eventually cap volume but wouldn't detect the pattern. OB-2.2 would detect unusual patterns only if in the training baseline. | **NEW: CR-C02** Decision commit protocol. Decisions passing judge review are committed. Reversal requires human authorisation or documented input change. Tier 2+. |
| CR-03 | **Role drift** | Critic starts taking actions. Executor starts approving its own work. Roles blur as natural language processing allows broad remit interpretation. | Hard role boundaries enforced through tool ACLs: critic has no write tools, executor has no approval authority. Enforcement at infrastructure layer (IA controls), not prompt layer. | Detect tool calls from the wrong role. Critic invoking a write tool is a guardrail violation regardless of reasoning. | Judge enforces "who is allowed to decide." | **Covered.** IA-1.4 (scoped permissions), EC-1.2 (tool allow-lists), IA-2.5 (orchestrator privilege separation). | **AMEND:** Add role-based testing scenario to IA/EC test suites: verify each agent can only use tools assigned to its role. |
| CR-04 | **Goal drift** | System's objective changes mid-execution without authorisation. Accumulated context shifts, agent reinterpretation, or manipulation (ASI01). | Immutable objective block: original task stored as read-only reference. Any change requires explicit human authorisation. | Detect objective mismatch: compare agent's current working goal against original immutable objective at each decision point. | Judge blocks unapproved goal changes by comparing actions against the immutable objective, not just general policy. | **Covered.** EC-2.5 (judge), OB-2.1 (decision chain), ASI01 controls. | **AMEND:** Add goal integrity to judge evaluation criteria: compare against immutable objective block, not just general policy. |

---

### Security Risks

These overlap significantly with the OWASP Agentic Top 10. MASO controls are generally stronger than the source table's mitigations.

| ID | Risk | Scenario | Prevent | Detect | Judge/Challenger Role | MASO Status | Control |
|----|------|----------|---------|--------|----------------------|-------------|---------|
| SR-01 | **Cross-agent prompt injection** | Injected document, email, or tool output contaminates one agent's context. Output carrying the injection propagates to other agents via bus or shared memory. | Treat all tool outputs and retrieved docs as tainted. Input sanitisation at every agent boundary. No direct instruction-following from retrieved text. Layer 1 guardrails per agent. | Injection detection patterns on bus (DP-2.1). Anomaly rules for unexpected instruction-like content in data fields. | Judge detects when agent output appears to follow instructions from input data rather than system prompt. | **Covered.** DP-2.1, EC-1.2, EC-2.5, DP-2.4, Layer 1 guardrails provide layered defence. | None required. |
| SR-02 | **Confused deputy** | Low-privilege agent causes high-privilege action indirectly by crafting a request that a higher-privilege agent executes without validating the original requester's authorisation. | End-to-end authorisation at action boundary: validate not just that the executor has permission, but that the original request was authorised to trigger this action class. Signed intents carry requester identity. | Monitor privilege graph: alert when low-privilege agent requests result in high-privilege actions. | Judge validates intent + authorisation before action. | **Covered.** IA-2.4 (no transitive permissions), IA-3.3 (delegation contracts), IA-2.5 (orchestrator privilege separation). | **AMEND:** Add Tier 2 test: low-privilege delegation request resulting in high-privilege action must be blocked. |
| SR-03 | **Privilege escalation by delegation** | Orchestrator chains delegations that in combination achieve a privilege level no single agent holds. Each delegation is within policy; the aggregate exceeds authorisation. | Capability-based security: least privilege per agent. Separate "request," "approve," and "execute" roles. No delegation chain can accumulate permissions. | Monitor privilege graph for unsafe chains: transitive closure of delegations exceeding any individual agent's scope triggers alert. | Judge helps, but core control is capability partitioning. | **Covered.** IA-2.4 (no transitive permissions), IA-3.3 (delegation contracts). | None required. |
| SR-04 | **Tool-chain injection** | Agent generates a prompt-to-tool payload that becomes an exploit: SQL injection via database tool, command injection via shell, code injection via sandbox. | Strict tool input validation: parameterised queries, argument schema validation, sandboxed execution with allow-lists. | Detect unusual tool arguments: pattern matching for injection signatures, WAF-style rules on tool parameters. | Judge performs "static review" of tool call parameters before execution. | **Covered.** EC-2.2 (sandbox), EC-1.2 (tool allow-lists), SC-2.2 (signed manifests with parameter schemas). | None required. |
| SR-05 | **Secrets leakage between agents** | Tokens, API keys, credentials copied into shared scratchpads, logs, inter-agent messages, or context windows. Secrets in context tend to leak. | Secret redaction at all boundaries. No secrets in prompts — vault references only. Agents access secrets through infrastructure (environment injection, vault APIs), never through context. | DLP on logs and inter-agent messages (DP-2.1). Automated secret pattern detection (regex for API keys, tokens, connection strings). | Judge is not sufficient. This is prevention-first. | **Partial.** DP-2.1 (DLP on bus), IA-2.2 (short-lived credentials). Missing: explicit prohibition of secrets in agent context. | **AMEND:** Add IA-domain policy: secrets must never appear in agent context windows, inter-agent messages, or logs. Platform-injected vault access only. |
| SR-06 | **Logging as a breach vector** | Comprehensive observability logging (OB domain) creates a rich target. Agent logs contain context, reasoning, tool parameters, potentially sensitive data. | Minimise sensitive data in logs: log actions and decisions, not full context windows. Classify, encrypt at rest and transit, access-restrict to security ops, set retention policies. | Audit access to log storage. Detect anomalous log access patterns. | Judge irrelevant. This is governance + platform controls. | **Partial.** OB controls focus on completeness and integrity, not data minimisation or access control. | **AMEND:** Add to OB domain: logs classified, encrypted, access-restricted, retention-limited. Full context capture stored in separate higher-classification log tier from operational monitoring. |

---

### Safety & Misuse Risks

| ID | Risk | Scenario | Prevent | Detect | Judge/Challenger Role | MASO Status | Control |
|----|------|----------|---------|--------|----------------------|-------------|---------|
| SM-01 | **Cumulative harm via decomposition** | Each agent's individual output is benign. Combined outputs enable a harmful outcome. Planning agent breaks a harmful task into harmless-looking subtasks that individually pass guardrails. | Policy enforcement at each step in context of the broader orchestration goal, not just the individual subtask. Orchestrator's original task specification available to each agent's guardrails. | Detect risky step sequences: pattern matching for known harmful decomposition patterns. | Judge evaluates the aggregate plan, not just individual steps. Can refuse based on cumulative risk even when each step passes individually. | **Partial.** Layer 1 guardrails are per-agent. EC-2.5 evaluates outputs but may lack visibility into the aggregate plan. | **NEW: SM-C01** Aggregate harm assessment. Judge evaluation includes full task plan context, not just individual output. For multi-step plans, judge evaluates whole plan before execution. Tier 2+. |
| SM-02 | **Persuasion optimisation / social engineering** | Agents iterating on messaging to maximise user compliance: A/B testing persuasion techniques, escalating emotional appeal, manufacturing urgency. Can occur without adversarial intent if agents optimise for "task completion." | Explicit guardrail limits: no coercion, no manipulation, no manufactured urgency. Purpose constraints: agents must not optimise for persuasion unless explicitly approved. | Monitor for persuasion patterns: escalating emotional language, repeated prompts, urgency signals not correlating with actual deadlines. | Judge scores outputs for manipulativeness and coercion. Outputs exceeding threshold are blocked. | **Gap.** ASI09 addresses trust dynamics but not iterative persuasion optimisation. | **NEW: SM-C02** Anti-manipulation guardrail. Outputs directed at humans must not employ escalating persuasion, manufactured urgency, or emotional manipulation. Judge criteria include manipulativeness score. Tier 1+. |

---

### Data Risks

| ID | Risk | Scenario | Prevent | Detect | Judge/Challenger Role | MASO Status | Control |
|----|------|----------|---------|--------|----------------------|-------------|---------|
| DR-01 | **Memory poisoning** | Malicious or incorrect content persists in agent memory and is reused across sessions, reshaping behaviour. | Memory write controls: only verified facts written. TTL on all entries. Provenance metadata on stored content. | Periodic memory audits. Rollback capability. Cross-session memory analysis (DP-3.3). | Judge gates memory writes: "only store if verified." | **Covered.** DP-2.4 (memory isolation), DP-3.2 (memory decay), DP-3.3 (cross-session analysis). Source table adds judge-gated writes — already implied by EC-2.5. | None required. |
| DR-02 | **RAG poisoning / corpus drift** | Compromised documents or outdated policies retrieved from knowledge base. Content may be tampered with or simply superseded. | Secure ingestion pipeline. Content signing. Source allow-lists. Freshness checks: validate content is current and hasn't been superseded. | Alerts for new or untrusted sources. Integrity verification on schedule (DP-2.2) or at query time (DP-3.1). | Judge penalises weak provenance. | **Partial.** DP-2.2, DP-3.1, SC-2.2, SC-2.3 cover integrity. Missing: freshness validation (has the document been superseded?). | **AMEND:** Add freshness metadata to DP-2.2: integrity checks include content currency. Documents past defined freshness window flagged for review. |
| DR-03 | **Provenance loss** | "Where did this come from?" cannot be answered. Decision trail breaks, preventing forensic investigation and regulatory accountability. | Mandatory provenance metadata on claims. Trace IDs across all agent interactions. | Forensic tooling for chain reconstruction. Decision chain (OB-2.1) with hash-linked entries. | Judge requires provenance to approve. | **Covered.** DP-3.4 (data provenance chain), OB-2.1 (immutable decision chain). | None required. |

---

### Governance Risks

| ID | Risk | Scenario | Prevent | Detect | Judge/Challenger Role | MASO Status | Control |
|----|------|----------|---------|--------|----------------------|-------------|---------|
| GV-01 | **Non-determinism / irreproducibility** | Same input produces different decisions on different runs. Inherent to LLMs (temperature > 0, provider changes, retrieval variability). In regulated environments, inability to reproduce a decision is a compliance risk. | Deterministic orchestration where the decision matters: fixed seeds where supported, pinned model versions (SC-3.1), fixed retrieval order. Accept non-determinism and design for it: record the full trace so decisions can be *explained* even if not *reproduced*. | Replay harness: re-run decisions with same inputs, compare outputs. Divergence beyond threshold triggers investigation. | Judge output must be traceable and replayable. | **Partial.** OB-2.1 (decision chain) provides the trace. SC-3.1 (model version pinning) reduces variation. No explicit reproducibility or explainability requirement. | **NEW: GV-C01** Decision traceability. For regulated decisions, full trace captured in sufficient detail for regulatory explanation. Standard is explainability, not reproducibility. Tier 2+. |
| GV-02 | **Metric gaming / reward hacking** | Agents optimise for "task completion" rather than correctness. Agent tasked with "answer the customer's question" optimises for satisfaction, not accuracy. Agent tasked with "complete all items" rushes with shallow analysis to report 100%. | Better objectives: task specifications emphasise truthfulness, calibration, and constraint adherence alongside completion. Avoid reward signals incentivising speed or volume over quality. | Detect suspiciously clean outputs: 100% completion, zero uncertainty, no exceptions. These are warnings, not success. | Judge scores faithfulness and penalises shortcutting. Evaluation criteria include depth of analysis and evidence quality, not just format compliance. | **Gap.** EC-2.5 provides enforcement point but lacks anti-gaming criteria. OB-2.2 detects pattern changes but not optimisation-driven shortcuts. | **NEW: GV-C02** Quality-over-completion evaluation. Judge criteria include faithfulness, analytical depth, evidence quality. Suspiciously high completion rates trigger additional scrutiny. Tier 2+. |

---

### Operational Risks

| ID | Risk | Scenario | Prevent | Detect | Judge/Challenger Role | MASO Status | Control |
|----|------|----------|---------|--------|----------------------|-------------|---------|
| OP-01 | **Cost blowouts** | Runaway tool calls, retries, exponential token spend from agent loops. | Hard budgets per task. Circuit breakers (EC-2.4). Bounded retries. Per-agent rate limits (EC-1.3). Blast radius caps (EC-2.3). | Cost monitoring with alerting (OB-2.5). Auto-stop on budget threshold. | Judge doesn't solve this. Orchestration constraints do. | **Covered.** EC-2.3, EC-2.4, EC-1.3, OB-2.5. | None required. |
| OP-02 | **Latency compounding** | Multi-step agent chains accumulate latency. 5-agent chain × 3s/step = 15s minimum. Adding judge evaluation adds 2–5s per step. | Parallelise independent steps. Reduce unnecessary handoffs. Cache retrieval results. Degrade gracefully: for low-risk flows, judge evaluates asynchronously (after commit) rather than synchronously (before commit). | SLO monitoring per orchestration. Alert when end-to-end latency exceeds threshold. | Judge can be optional (async post-commit audit) for low-risk flows, reducing latency for the common case. | **Gap.** EC-3.4 (time-boxing) sets max duration but doesn't address latency optimisation or SLOs. | **AMEND:** Add per-orchestration latency SLOs. Document which control layers operate synchronously (blocking) vs asynchronously (post-commit). Judge async for auto-approved actions at Tier 2+. |
| OP-03 | **Partial failure masquerading as success** | Tool fails silently, returns incomplete data, or times out — but agent continues with plausible output. System reports success. Human reviewer sees confident, well-formatted result and approves. | Fail closed on critical tools: if a required tool fails, agent reports failure explicitly rather than continuing with partial data. Step attestation: each agent attests to input completeness before producing output. | Detect missing step attestations in decision chain. Flag outputs where required tool calls are absent or returned errors. | Judge blocks outputs lacking required evidence. If plan says "retrieve financial data" and no financial tool call appears in the trace, output rejected. | **Partial.** OB-2.1 captures trace. EC-2.5 could catch this if criteria include completeness. No explicit "fail closed" requirement. | **NEW: OP-C01** Tool completion attestation. Required tool calls defined per task. Tool failure or skip → explicit incomplete status. Judge verifies all required tools completed before approving. Tier 2+. |

---

### Human Factors Risks

| ID | Risk | Scenario | Prevent | Detect | Judge/Challenger Role | MASO Status | Control |
|----|------|----------|---------|--------|----------------------|-------------|---------|
| HF-01 | **Automation bias** | Humans over-trust agent outputs, particularly when multiple agents agree. "The system says so" replaces independent judgment. Direct manifestation of ASI09 at the human-system interface. | UI design: always show evidence, dissent, confidence levels, and uncertainty metadata alongside the recommendation. Never present multi-agent consensus as a single authoritative answer. Operator training to challenge outputs. | Post-incident reviews of approval decisions. Sampling programme: periodically present operators with deliberately flawed outputs to measure challenge rates. | Judge provides calibrated risk rating, not certainty score. Output explicitly states what it doesn't know, not just what it recommends. | **Partial.** ASI09 controls address trust dynamics. EP-C06 ensures human sees uncertainty metadata. Missing: systematic testing of human override behaviour. | **AMEND:** Add periodic challenge rate test to Tier 1 suite: present operators with outputs containing deliberate errors. Target challenge rate > 80%. |
| HF-02 | **Accountability blur** | "The agents decided" becomes an accountability crumple zone. No individual human is responsible because the decision emerged from agent collaboration. Governance failure, not technical. | RACI matrix: every workflow has a named human owner responsible for design, model selection, data sources, and approval. Decision chain (OB-2.1) records the accountable human, not just agent IDs. | Audit decisions to accountable humans. If a decision cannot be traced to a responsible human, governance has failed. | Judge is a tool, not an accountable party. Judge approval does not transfer responsibility from the human owner. | **Partial.** OB-2.1 captures chain but doesn't require a named human owner. ASI09 addresses trust but not accountability assignment. | **AMEND:** Add `accountable_human` field to decision chain log. Every workflow must have a designated human owner recorded in the AIBOM (SC-2.1). |

---

## Consolidated Amendment Summary

### New Controls (15)

| ID | Name | Domain | Tier | Source Risk |
|----|------|--------|------|-------------|
| EP-C01 | Consensus diversity gate | Execution Control | 2+ | EP-01 Groupthink |
| EP-C02 | Model diversity policy | Supply Chain | 2+ | EP-02 Correlated errors |
| EP-C03 | Claim provenance enforcement | Data Protection | 2+ | EP-03 Hallucination amplification |
| EP-C04 | Self-referential evidence prohibition | Data Protection | 2+ | EP-04 Synthetic corroboration |
| EP-C05 | Constraint fidelity check | Execution Control | 2+ | EP-05 Semantic drift |
| EP-C06 | Uncertainty preservation | Data Protection | 2+ | EP-06 Uncertainty stripping |
| EP-C07 | Plan-execution conformance | Execution Control | 2+ | EP-07 Planner-executor mismatch |
| EP-C08 | Assumption isolation | Data Protection | 2+ | EP-08 Hidden assumptions |
| CR-C01 | Interaction timeout | Execution Control | 1+ | CR-01 Deadlock/livelock |
| CR-C02 | Decision commit protocol | Execution Control | 2+ | CR-02 Oscillation |
| SM-C01 | Aggregate harm assessment | Execution Control | 2+ | SM-01 Cumulative harm |
| SM-C02 | Anti-manipulation guardrail | Execution Control | 1+ | SM-02 Persuasion optimisation |
| GV-C01 | Decision traceability | Observability | 2+ | GV-01 Non-determinism |
| GV-C02 | Quality-over-completion evaluation | Execution Control | 2+ | GV-02 Metric gaming |
| OP-C01 | Tool completion attestation | Execution Control | 2+ | OP-03 Partial failure |

### Amendments to Existing Controls (8)

| Existing Control | Amendment | Source Risk |
|-----------------|-----------|-------------|
| IA/EC test suites | Add role-based testing: verify each agent can only use tools assigned to its role | CR-03 Role drift |
| EC-2.5 LLM-as-Judge criteria | Add goal integrity check: compare against immutable objective block | CR-04 Goal drift |
| IA domain policy | Add: secrets must never appear in agent context windows, messages, or logs | SR-05 Secrets leakage |
| OB domain | Add log classification, encryption, access control, retention. Separate operational and forensic log tiers | SR-06 Logging as breach vector |
| DP-2.2 RAG integrity | Add freshness metadata: content currency check alongside integrity verification | DR-02 RAG poisoning |
| EC domain | Add per-orchestration latency SLOs. Classify control layers as sync/async | OP-02 Latency |
| Tier 1 test suite | Add periodic challenge rate test (deliberate errors, target > 80% detection) | HF-01 Automation bias |
| OB-2.1 decision chain + SC-2.1 AIBOM | Add `accountable_human` field to decision chain. Designated human owner in AIBOM | HF-02 Accountability blur |

---

### Coverage Assessment

| Category | Risks | Already covered | Partial / amended | New control needed |
|----------|-------|----------------|-------------------|-------------------|
| Epistemic | 8 | 0 | 0 | 8 |
| Coordination | 4 | 2 | 2 | 2 |
| Security | 6 | 4 | 2 | 0 |
| Safety/Misuse | 2 | 0 | 0 | 2 |
| Data | 3 | 2 | 1 | 0 |
| Governance | 2 | 0 | 0 | 2 |
| Operational | 3 | 1 | 1 | 1 |
| Human Factors | 2 | 0 | 2 | 0 |
| **Total** | **30** | **9** | **8** | **15** |

---

### Key Observation

The source table's mitigations align well with MASO's approach in the security and data categories — these are the domains where OWASP coverage is strongest. The critical gap is epistemic: all eight epistemic risks require new controls. These risks are the most dangerous because they produce failures that look like success — confident, well-formatted, unanimously agreed outputs that are wrong.

The source table correctly identifies that the Judge/Challenger is not the solution for every risk. Specifically: secrets leakage (SR-05), logging as breach vector (SR-06), cost blowouts (OP-01), and accountability blur (HF-02) require platform governance and infrastructure controls, not evaluation-layer interventions. MASO's tiered approach (prevention at Layer 1, detection at Layer 2, governance at Layer 3) is the right architecture for this — but the existing control specs need the amendments listed above to close the gaps.

---

*Back to: [MASO Framework](../README.md)*
