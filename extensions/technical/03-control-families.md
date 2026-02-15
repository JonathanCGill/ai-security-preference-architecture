# AI Security Control Families

This document defines the control families for AI systems, organised by function and timing.

---

## Control Model Overview

![Control Layers](../../images/control-layers.svg)

AI security controls operate across three layers:

| Layer | Function | Timing | Can Block? |
|-------|----------|--------|------------|
| **Guardrails** | Block known-bad patterns | Inline, real-time | Yes |
| **LLM-as-Judge** | Detect issues, surface findings | Async, after-the-fact | No |
| **Human Oversight** | Review, decide, act | As needed | Yes |

**Key principle:** Guardrails prevent. Judge detects. Humans decide.

---

## Control Family Index

| ID | Family | Purpose |
|----|--------|---------|
| AI.1 | Governance | Policies, roles, accountability |
| AI.2 | Risk Management | Classification, assessment, monitoring |
| AI.3 | Inventory & Documentation | Registration, documentation, lineage |
| AI.4 | Development Security | Secure development, testing, deployment |
| AI.5 | Data Governance | Data quality, privacy, protection |
| AI.6 | Model Security | Model protection, validation, monitoring |
| AI.7 | Runtime Controls — Guardrails | Inline input/output validation |
| AI.8 | Runtime Controls — LLM-as-Judge | Async assurance and monitoring |
| AI.9 | Human Oversight | HITL, escalation, accountability |
| AI.10 | Agentic Controls | Agent-specific safeguards |
| AI.11 | Logging & Monitoring | Observability, alerting, audit |
| AI.12 | Incident Response | Detection, response, recovery |
| AI.13 | AI Supplier Management | Vendor assessment, agreements, monitoring |
| AI.14 | AI Security Awareness | Training for AI-specific risks |
| AI.15 | AI System Continuity | BCP for AI systems |
| AI.16 | AI Intellectual Property | Model and data IP protection |

> **ISO 27001 Alignment:** See [ISO 27001 Alignment](../regulatory/iso-27001-alignment.md) for detailed mapping.

---

## AI.1 Governance

### AI.1.1 AI Policy Framework

**Requirement:** Establish policies governing AI development, deployment, and use.

**Implementation:**
- Acceptable use policy for AI systems
- AI ethics principles
- Roles and responsibilities
- Approval workflows by risk tier

**Evidence:** Policy documents, approval records

---

### AI.1.2 Governance Structure

**Requirement:** Define governance bodies and decision rights for AI.

**Implementation:**
- AI Governance Committee (CRITICAL tier approvals)
- Risk and security sign-off (HIGH tier)
- Business owner accountability
- Clear escalation paths

**Evidence:** Committee charters, meeting minutes, approval records

---

### AI.1.3 Accountability

**Requirement:** Assign clear accountability for AI system outcomes.

**Implementation:**
- Named owner for each AI system
- Accountability cannot be delegated to AI
- Human decision-maker for consequential actions
- Documented responsibility matrix

**Evidence:** RACI matrices, system ownership records

---

## AI.2 Risk Management

### AI.2.1 Risk Classification

**Requirement:** Classify all AI systems by risk tier.

**Tiers:**

| Tier | Criteria | Examples |
|------|----------|----------|
| CRITICAL | Affects credit, employment, legal rights; high regulatory exposure | Credit decisions, hiring systems |
| HIGH | Customer-facing; accesses sensitive data; significant business impact | Customer service AI, document processing |
| MEDIUM | Internal use; limited sensitive data; moderate impact | Internal assistants, meeting summarisers |
| LOW | Experimental; no production data; minimal impact | POCs, sandboxes |

**Evidence:** Classification assessments, approval records

---

### AI.2.2 Risk Assessment

**Requirement:** Assess risks before deployment and periodically thereafter.

**Assessment factors:**
- Decision impact
- Data sensitivity
- User population
- Autonomy level
- Regulatory scope
- Reputational risk

**Evidence:** Risk assessment documents, review records

---

### AI.2.3 Ongoing Risk Monitoring

**Requirement:** Continuously monitor AI systems for emerging risks.

**Implementation:**
- Judge-based quality monitoring (async)
- Statistical drift detection
- Bias monitoring (where applicable)
- Threat landscape monitoring

**Evidence:** Monitoring dashboards, trend reports

---

## AI.3 Inventory & Documentation

### AI.3.1 AI System Inventory

**Requirement:** Maintain a complete inventory of all AI systems.

**Required fields:**
- System name and description
- Risk tier
- Owner
- Technology stack
- Data sources
- Deployment status
- Last review date

**Evidence:** Inventory records

---

### AI.3.2 System Documentation

**Requirement:** Document AI systems proportionate to risk tier.

| Tier | Documentation Required |
|------|----------------------|
| CRITICAL | Full model documentation (SR 11-7 compliant), FRIA, validation reports |
| HIGH | System architecture, data flows, control documentation |
| MEDIUM | Basic architecture, risk assessment |
| LOW | Registration in inventory |

**Evidence:** Documentation packages

---

### AI.3.3 Data Lineage

**Requirement:** Document data sources, flows, and transformations.

**Implementation:**
- Training data sources
- Runtime data inputs
- Data flow diagrams
- Retention and deletion

**Evidence:** Data flow documentation

---

### AI.3.4 Explainability Requirements

**Requirement:** Define and document the level of explainability required for each AI system, proportionate to risk tier.

AI models are inherently opaque — billions of parameters with no traceable decision logic. Explainability methods (attention maps, SHAP, etc.) are approximations. The required level of explainability must be defined per system and may constrain which models can be used.

**Explainability tiers:**

| Tier | Requirement | Approach |
|------|------------|----------|
| CRITICAL | Full decision audit trail; human must be able to articulate reasoning | Constrained models, rule-augmented AI, mandatory human reasoning documentation |
| HIGH | Key factors identified; output rationale documented | Feature importance, source citation, Judge evaluation of reasoning |
| MEDIUM | General approach documented; exceptions explainable | System-level documentation, output with source references |
| LOW | System purpose and approach documented | Standard documentation |

**Documentation per system:**
- What can be explained (system architecture, data sources, general approach)
- What cannot be explained (individual model decisions, parameter interactions)
- What methods are used (SHAP, attention, source citation, Judge analysis)
- What compensating controls exist (HITL review, Judge evaluation, output validation)

**Regulatory alignment:**
- GDPR Article 22: Right to explanation for automated decisions
- EU AI Act Article 13: Transparency requirements
- SR 11-7: Model risk management, model validation
- FCA/PRA: Consumer Duty, outcomes monitoring

**Evidence:** Explainability assessments per system, methodology documentation

---

## AI.4 Development Security

### AI.4.1 Secure Development

**Requirement:** Apply secure development practices to AI systems.

**Implementation:**
- Secure coding standards
- Code review requirements
- Dependency management
- Secret management

**Evidence:** Code review records, security scan results

---

### AI.4.2 Testing

**Requirement:** Test AI systems for security and quality before deployment.

**Testing types:**

| Type | Purpose | When |
|------|---------|------|
| Functional | Correct behaviour | Pre-deployment |
| Security | Vulnerability identification | Pre-deployment |
| Adversarial | Robustness against attacks | Pre-deployment, periodic |
| Bias | Fairness across protected characteristics | Pre-deployment, periodic |
| Regression | Detect degradation | Ongoing |
| Statistical | Validate output distributions (not exact outputs) | Pre-deployment, ongoing |
| Semantic | Test for meaning-based evasion of controls | Pre-deployment, periodic |

**Non-determinism requirement:** AI systems are probabilistic. Testing must evaluate output distributions and acceptable ranges, not exact expected outputs. Run each test case multiple times and validate that outputs fall within acceptance criteria.

| Tier | Statistical test runs per case | Acceptance threshold |
|------|-------------------------------|---------------------|
| CRITICAL | ≥50 | 99% within criteria |
| HIGH | ≥20 | 95% within criteria |
| MEDIUM | ≥10 | 90% within criteria |
| LOW | ≥5 | 85% within criteria |

**Evidence:** Test results, coverage reports, statistical analysis, adversarial test outcomes

---

### AI.4.3 Pre-Deployment Review

**Requirement:** Security review before production deployment.

| Tier | Review Required |
|------|-----------------|
| CRITICAL | Independent security review, governance committee approval |
| HIGH | Security team review, risk sign-off |
| MEDIUM | Streamlined security review |
| LOW | Self-assessment |

**Evidence:** Review reports, approval records

---

## AI.5 Data Governance

### AI.5.1 Data Classification

**Requirement:** Classify data used by AI systems.

**Implementation:**
- Training data classification
- Runtime input classification
- Output classification
- Apply handling rules based on classification

**Evidence:** Data classification records

---

### AI.5.2 Data Quality

**Requirement:** Ensure data quality for AI systems.

**Implementation:**
- Training data validation
- Input data validation (guardrails)
- Knowledge base quality (RAG systems)
- Data freshness monitoring

**Evidence:** Quality metrics, validation records

---

### AI.5.3 Privacy Protection

**Requirement:** Protect personal data in AI systems.

**Implementation:**
- Data minimisation
- Purpose limitation
- PII detection and handling (guardrails)
- Privacy impact assessments

**Evidence:** PIAs, data handling records

---

### AI.5.4 RAG Content Integrity

**Requirement:** Validate and protect the integrity of content retrieved for AI context.

Retrieved content (RAG) is a primary attack vector. Poisoned knowledge base content can hijack model behaviour without triggering input guardrails, because the malicious content enters through the data path, not the user input path.

**Implementation:**

| Control | Purpose |
|---------|---------|
| **Content validation** | Validate retrieved content hasn't been tampered with (checksums, signatures) |
| **Content sanitisation** | Strip potential injection payloads from retrieved content before inclusion in context |
| **Source authentication** | Verify the source of retrieved content |
| **Freshness monitoring** | Alert when knowledge base content exceeds staleness thresholds |
| **Modification tracking** | Log all changes to knowledge base content with who, what, when |
| **Anomaly detection** | Flag when retrieved content distribution shifts unexpectedly |
| **Access control** | Restrict who can modify knowledge base content |

**Freshness thresholds by tier:**

| Tier | Max staleness | Alert |
|------|--------------|-------|
| CRITICAL | 1 hour | Immediate |
| HIGH | 24 hours | Within 1 hour |
| MEDIUM | 7 days | Daily |
| LOW | 30 days | Weekly |

**Evidence:** Content integrity logs, freshness monitoring records, knowledge base change logs

### AI.6.1 Model Protection

**Requirement:** Protect AI models from theft and tampering.

**Implementation:**
- Access controls on model artifacts
- Secure model storage
- Model versioning
- Integrity verification

**Evidence:** Access logs, integrity checks

---

### AI.6.2 Model Validation

**Requirement:** Validate model behaviour before and during deployment.

| Tier | Validation Required |
|------|---------------------|
| CRITICAL | Independent validation (SR 11-7), annual revalidation |
| HIGH | Internal validation, periodic review |
| MEDIUM | Functional testing |
| LOW | Basic testing |

**Bias and fairness testing:** All models making decisions affecting individuals must be tested for discriminatory outputs across protected characteristics (age, gender, ethnicity, disability, etc.) before deployment and periodically in production.

**Continuous validation:** Model validation is never complete. Validation must be ongoing using statistical methods to detect performance degradation, distributional shift, and emergent bias.

| Validation Type | Frequency |
|----------------|-----------|
| Pre-deployment validation | Before each deployment |
| Periodic revalidation | Quarterly (CRITICAL/HIGH), biannually (MEDIUM/LOW) |
| Post-upgrade validation | After every model version change |
| Bias audit | Annually (CRITICAL/HIGH), biannually (MEDIUM/LOW) |

**Evidence:** Validation reports, bias test results, continuous validation metrics

---

### AI.6.3 Model Monitoring

**Requirement:** Monitor model performance and behaviour in production.

**Implementation:**
- Performance metrics (accuracy, latency, throughput)
- Drift detection (input distribution, output distribution)
- Judge-based quality assurance (async)
- Anomaly detection
- Gradual degradation detection (trend analysis, not just threshold alerts)
- Capability monitoring (track what the model is doing, not just how well)

**Invisible degradation:** AI systems can degrade silently — output quality drops with no error signal. Monitoring must include trend analysis to catch gradual decline, not just sudden failures.

| Metric Type | What It Catches |
|-------------|----------------|
| Threshold alerts | Sudden failures, outages |
| Trend analysis | Gradual quality decline over days/weeks |
| Baseline comparison | Drift from validated behaviour |
| Distribution monitoring | Shift in output patterns |

**Evidence:** Monitoring dashboards, alerts, trend reports

---

### AI.6.4 Model Capability Assessment

**Requirement:** Assess model capabilities before deployment, and reassess when models are upgraded or changed.

AI models can develop emergent capabilities that weren't explicitly programmed. A new model version may have capabilities — beneficial or dangerous — that the previous version lacked. Controls designed for the old model may be insufficient for the new one.

**Assessment triggers:**

| Trigger | Action |
|---------|--------|
| New model deployment | Full capability assessment |
| Model version upgrade | Delta assessment (what changed?) |
| Provider announces new capabilities | Evaluate relevance and risk |
| Anomalous behaviour detected | Investigate for unknown capabilities |

**Assessment scope:**

| Dimension | What to test |
|-----------|-------------|
| **Intended capabilities** | Does the model do what we need? |
| **Unintended capabilities** | Can the model do things we don't want? (code execution, data extraction, tool misuse) |
| **Capability boundaries** | Where does the model exceed or fall short of the previous version? |
| **Risk profile change** | Does the new capability change the risk tier of the use case? |

**Evidence:** Capability assessment reports, risk reclassification records

---

### AI.6.5 Baseline Comparison

**Requirement:** Maintain and periodically test against a baseline set of known-good inputs and outputs.

Invisible degradation — where AI quality drops with no error signal — is a novel risk. Baseline comparison is the primary detection method.

**Implementation:**

| Component | Purpose |
|-----------|---------|
| **Baseline dataset** | Curated set of inputs with known-good outputs, covering key scenarios |
| **Periodic testing** | Run baseline dataset against production system on schedule |
| **Comparison analysis** | Compare current outputs to baseline outputs using defined criteria |
| **Drift alerting** | Alert when baseline comparison scores fall below threshold |

**Testing frequency:**

| Tier | Frequency |
|------|-----------|
| CRITICAL | Daily |
| HIGH | Weekly |
| MEDIUM | Fortnightly |
| LOW | Monthly |

**Evidence:** Baseline datasets, comparison results, drift alerts

---

## AI.7 Runtime Controls — Guardrails

Guardrails are **inline controls** that operate in real-time on inputs and outputs.

### AI.7.1 Input Guardrails

**Requirement:** Validate and filter inputs before AI processing.

**Implementation:**

| Check | Purpose | Method |
|-------|---------|--------|
| Length limits | Prevent resource abuse | Rules |
| Format validation | Ensure valid input structure | Rules |
| Injection detection | Block prompt injection | Patterns, classifiers |
| Semantic intent analysis | Detect meaning-based evasion | ML classifiers |
| Scope enforcement | Keep requests in bounds | Patterns, classifiers |
| Rate limiting | Prevent abuse | Rules |
| Retrieved content filtering | Sanitise RAG content before inclusion in context | Patterns, classifiers |

**Limitation acknowledged:** Pattern-based and classifier-based guardrails reduce but cannot eliminate prompt injection. Instructions and data share the same channel (the context window) and there is no complete technical solution. Defence-in-depth is the only viable strategy.

**Semantic attacks:** Attackers exploit meaning, not syntax. Keyword filters miss rephrased harmful requests. Input guardrails should include semantic intent analysis where feasible, but the Judge (AI.8) is better positioned for deep semantic analysis due to the latency budget.

**RAG content filtering:** Retrieved context is an injection vector. Apply input guardrail checks to retrieved content, not just user input.

**Performance requirement:** <50ms latency budget

**Evidence:** Guardrail configuration, block logs, false positive rates, semantic classifier metrics

---

### AI.7.2 Output Guardrails

**Requirement:** Filter outputs before delivery to users or downstream systems.

**Implementation:**

| Check | Purpose | Method |
|-------|---------|--------|
| PII detection | Prevent data leakage | Patterns, NER |
| Content filtering | Block policy violations | Patterns, classifiers |
| Format validation | Ensure valid output structure | Rules |
| Cross-reference check | Prevent cross-user leakage | Data lookups |
| Factual grounding check | Verify claims against retrieved source data | Comparison logic |
| Uncertainty markers | Inject appropriate hedging for low-confidence outputs | Rules, classifiers |

**Grounding verification:** For CRITICAL and HIGH tier systems, output guardrails should cross-reference AI claims against the source data that was retrieved. Unsupported claims should be flagged or blocked.

**Uncertainty markers:** For high-risk use cases, outputs should include appropriate hedging ("Based on available data..." rather than presenting as absolute fact). The AI must be able to say "I don't know" rather than fabricate.

**Performance requirement:** <50ms latency budget

**Evidence:** Guardrail configuration, block logs, false positive rates, grounding check results

---

### AI.7.3 Guardrail Maintenance

**Requirement:** Maintain and improve guardrails over time.

**Implementation:**
- Regular pattern updates based on new threats
- False positive monitoring and tuning
- Feedback loop from Judge findings
- Adversarial testing (including semantic/meaning-based evasion, not just known patterns)
- Periodic effectiveness verification (don't assume guardrails still work — test them)

**Guardrail effectiveness testing:** Guardrails degrade over time as attackers adapt. Periodic red-team testing must include semantic evasion techniques — rephrased requests, multi-turn manipulation, and context-based attacks.

| Tier | Adversarial testing frequency |
|------|------------------------------|
| CRITICAL | Monthly |
| HIGH | Quarterly |
| MEDIUM | Biannually |
| LOW | Annually |

**Evidence:** Update logs, tuning records, test results, effectiveness test reports

---

### AI.7.4 Context Isolation

**Requirement:** Prevent cross-user and cross-session context contamination.

In multi-user AI systems, information from one user's session must not leak into another user's session. Shared context, cached responses, or persistent model memory can create cross-user data leakage.

**Implementation:**

| Control | Purpose |
|---------|---------|
| **Stateless sessions** | Each session starts with clean context; no carry-over between users |
| **Session boundary enforcement** | Hard isolation between user sessions at infrastructure level |
| **No shared memory** | Disable any persistent memory or context sharing between users |
| **Cache isolation** | If response caching is used, scope caches to individual users |
| **Context window clearing** | Ensure context window is fully cleared between sessions |
| **Multi-tenant isolation** | In SaaS deployments, isolate between organisational tenants |

**Tier requirements:**

| Tier | Isolation Level |
|------|----------------|
| CRITICAL | Dedicated model instances per user/session; no shared infrastructure |
| HIGH | Strict session isolation; no caching across users |
| MEDIUM | Session isolation; user-scoped caching permitted |
| LOW | Standard session management |

**Evidence:** Isolation architecture documentation, session management configuration, penetration test results

---

## AI.8 Runtime Controls — LLM-as-Judge

The Judge is an **async assurance mechanism** that evaluates AI interactions after the fact.

### AI.8.1 Judge Evaluation

**Requirement:** Evaluate AI interactions for quality, policy compliance, and issues.

**Evaluation areas:**

| Area | What It Assesses |
|------|------------------|
| Quality | Accuracy, helpfulness, appropriateness |
| Policy compliance | Adherence to system rules and constraints |
| Conduct risk | Potential for customer or business harm |
| Anomalies | Unusual patterns suggesting attacks or failures |
| Bias indicators | Potential unfair treatment (where applicable) |
| Hallucination detection | Unsupported claims — compare output against retrieved context |
| Instruction override detection | Signs that the model followed injected instructions rather than system prompt |
| Confidence calibration | Cases where model expresses high confidence on topics where it's likely unreliable |

**Hallucination detection:** Judge compares AI output against the source data that was retrieved. Claims not supported by retrieved context should be flagged. This is the primary async defence against hallucination.

**Instruction override detection:** Judge evaluates whether the model's behaviour in an interaction is consistent with its system prompt. Behavioural anomalies — sudden topic changes, policy deviations, unusual output formats — may indicate the model followed injected instructions.

**Criteria-based evaluation:** Because AI is non-deterministic, Judge evaluates outputs against acceptance criteria, not expected exact outputs. "Was this response helpful, accurate, and within policy?" — not "Did this response match the expected answer?"

**Evidence:** Judge evaluation logs, finding summaries, hallucination detection rates, override detection rates

---

### AI.8.2 Sampling Strategy

**Requirement:** Sample interactions for Judge evaluation based on risk tier.

| Tier | Sampling Rate | Rationale |
|------|---------------|-----------|
| CRITICAL | 100% | Full audit trail required |
| HIGH | 20-50% | Statistically significant coverage |
| MEDIUM | 5-10% | Trend detection |
| LOW | 1-5% or triggered | Spot checks |

**Additional triggers for 100% evaluation:**
- Guardrail near-misses
- Customer complaints
- Unusual patterns
- New feature areas
- Post model upgrade (first 48 hours)
- Baseline comparison drift detected

**Baseline integration:** Sampling should include periodic baseline queries (known-good inputs with expected outcomes) to detect invisible degradation. If baseline comparison shows drift, temporarily increase sampling to 100% until root cause identified.

**Evidence:** Sampling configuration, coverage metrics, baseline comparison results

---

### AI.8.3 Finding Management

**Requirement:** Route Judge findings appropriately for human review.

**Routing:**

| Finding Severity | Routing | SLA |
|------------------|---------|-----|
| Critical (bias, data leakage) | Immediate escalation | 1 hour |
| High (policy violation, quality failure) | Priority queue | 24 hours |
| Medium (minor issues) | Standard review | 1 week |
| Low (observations) | Batch review | Monthly |

**Evidence:** Finding logs, routing records, SLA compliance

> **Note:** These are Judge finding management SLAs — the time to triage and route findings from automated evaluation. They are distinct from incident response SLAs in the [AI Incident Playbook](../../extensions/templates/ai-incident-playbook.md), which govern response to confirmed security incidents.

---

### AI.8.4 Judge Governance

**Requirement:** Govern the Judge as an AI system subject to controls.

**Implementation:**
- Validate Judge accuracy
- Test Judge against known cases
- Monitor Judge for drift
- Human oversight of Judge findings

**Evidence:** Judge validation records, accuracy metrics

---

### AI.8.5 Confidence Calibration

**Requirement:** Detect and flag cases where AI expresses inappropriate confidence.

AI presents every output with equal confidence — correct or incorrect. Users cannot distinguish between a confident correct answer and a confident wrong answer. This leads to over-reliance, automation bias, and cascading errors when confident-but-wrong outputs feed downstream systems.

**Implementation:**

| Control | Purpose |
|---------|---------|
| **Topic confidence mapping** | Identify topics/domains where the AI is reliably accurate vs. unreliable |
| **Uncertainty injection** | For known-unreliable domains, inject hedging language into outputs |
| **Source citation** | Require AI to cite sources; flag outputs with no supporting source |
| **Multi-model cross-check** | For CRITICAL decisions, compare outputs from multiple models; flag disagreements |
| **Confidence scoring** | Where model provides confidence scores, calibrate and surface to users |

**Judge integration:** Judge should flag cases where:
- AI makes definitive claims on topics outside its reliable domain
- AI provides specific numbers or dates without source data
- AI contradicts information in its retrieved context
- AI's output would be treated as authoritative by the downstream consumer

**Evidence:** Confidence calibration records, uncertainty injection logs, cross-check results

---

## AI.9 Human Oversight

### AI.9.1 Human-in-the-Loop

**Requirement:** Maintain human oversight proportionate to risk.

| Tier | HITL Requirement |
|------|------------------|
| CRITICAL | Human decides all consequential actions |
| HIGH | Human reviews all Judge escalations; sampling of routine |
| MEDIUM | Periodic batch review; escalation path |
| LOW | Spot checks; standard IT escalation |

**Automation bias mitigation:** HITL reviewers must be trained to challenge AI outputs, not just confirm them. Humans tend to defer to AI even when their own judgement is better (automation bias) and anchor on the first AI recommendation (anchoring bias).

**Design requirements for HITL interfaces:**
- Present relevant source data alongside AI output so reviewers can verify
- For CRITICAL decisions, require reviewer to form independent judgement before seeing AI recommendation
- Randomise presentation order where possible to reduce anchoring
- Include clear "I disagree" pathways with no friction penalty

**Evidence:** Review records, decision logs, reviewer training records

---

### AI.9.2 Escalation Procedures

**Requirement:** Define clear escalation paths for AI issues.

**Implementation:**
- Escalation triggers defined
- Escalation paths documented
- Escalation SLAs established
- On-call coverage (for HIGH/CRITICAL)
- Escalation trigger when HITL reviewers consistently agree with AI (may indicate rubber-stamping)

**Evidence:** Escalation procedures, escalation logs

---

### AI.9.3 Human Override

**Requirement:** Humans can override AI recommendations.

**Implementation:**
- Override capability in all workflows
- Override reasoning documented
- Override patterns monitored
- No penalty for appropriate overrides

**Evidence:** Override logs, pattern analysis

---

### AI.9.4 Accountability

**Requirement:** Humans remain accountable for outcomes.

**Implementation:**
- AI is advisory; humans decide
- Decision authority clearly assigned
- Audit trail of who decided what
- No "the AI did it" defence
- AI recommendation does not transfer accountability to the system

**Evidence:** Decision logs with human attribution

---

### AI.9.5 HITL Effectiveness Measurement

**Requirement:** Measure whether human oversight is genuinely effective, not just present.

Human oversight is a known failure mode in every industry that uses it (aviation, nuclear, financial services). Simply having a human "in the loop" does not guarantee effective oversight. Measure to verify.

**Metrics:**

| Metric | What It Indicates | Concern Trigger |
|--------|------------------|-----------------|
| **Override rate** | How often reviewers disagree with AI | Very low rate may indicate automation bias, not AI perfection |
| **Decision time** | How long reviewers spend per review | Very fast times suggest rubber-stamping |
| **Finding detection rate** | How often reviewers catch known-bad items | Low rate indicates ineffective review |
| **Inter-reviewer agreement** | Whether different reviewers reach same conclusions | Low agreement suggests unclear criteria |
| **Canary detection rate** | How often reviewers catch deliberately inserted test cases | Direct measure of attention |

**Canary reviews:** Periodically inject known findings (canary cases) into the HITL review queue. If reviewers don't catch them, the process is not working.

| Tier | Canary frequency | Expected detection |
|------|-----------------|-------------------|
| CRITICAL | Weekly | 100% |
| HIGH | Monthly | 95% |
| MEDIUM | Quarterly | 90% |
| LOW | Biannually | 80% |

**Evidence:** HITL effectiveness metrics, canary detection results, reviewer performance data

---

## AI.10 Agentic Controls

Additional controls for autonomous AI agents (systems that take actions, not just generate content).

> **See [Agentic Controls](04-agentic-controls.md) for comprehensive coverage.**
>
> **Control ID note:** Agentic controls use two complementary schemes. **AG.x** (AG.1–AG.4) provides structural decomposition by phase (planning, execution, assurance, multi-agent). **AI.10.x** provides implementation control IDs within the main control family numbering. See the control selection guide for the mapping: AI.10.1–10.6 implement AG.1–AG.4.

Agentic AI requires controls at three phases:

| Phase | Controls |
|-------|----------|
| **Planning** | Plan disclosure, plan guardrails, plan approval |
| **Execution** | Action guardrails, circuit breakers, scope enforcement |
| **Assurance** | Trajectory logging, trajectory evaluation, HITL review |

### AG.1 Plan-Level Controls

| Control | Purpose |
|---------|---------|
| AG.1.1 Plan disclosure | Agent discloses intended actions before execution |
| AG.1.2 Plan guardrails | Validate plans against policy |
| AG.1.3 Plan approval | Human approves plans above threshold |

### AG.2 Execution-Level Controls

| Control | Purpose |
|---------|---------|
| AG.2.1 Action guardrails | Validate each action at runtime |
| AG.2.2 Circuit breakers | Hard limits that halt execution |
| AG.2.3 Scope enforcement | Enforce boundaries on access and actions |
| AG.2.4 Tool controls | Govern which tools agents can use |
| AG.2.5 Tool protocol security | Secure MCP, function calling, etc. |

### AG.3 Assurance-Level Controls

| Control | Purpose |
|---------|---------|
| AG.3.1 Trajectory logging | Log complete execution path |
| AG.3.2 Trajectory evaluation | Judge evaluates full trajectory |
| AG.3.3 HITL for agentic | Human oversight at plan, execution, and review stages |

### AG.4 Multi-Agent Controls

| Control | Purpose |
|---------|---------|
| AG.4.1 Agent inventory | Track all agents and relationships |
| AG.4.2 Orchestration controls | Govern delegation between agents |
| AG.4.3 Trace correlation | End-to-end trace across agents |

### AI.10.1 Scope Boundaries

**Requirement:** Define and enforce what agents can and cannot do.

**Implementation:**
- Explicit action allowlist
- Parameter constraints on actions
- Scope enforcement in code
- Boundary monitoring

**Evidence:** Scope definitions, boundary violation logs

---

### AI.10.2 Approval Workflows

**Requirement:** Require human approval for high-impact agent actions.

**Implementation:**
- Define which actions require approval
- Implement approval workflows
- Timeout if approval not received
- Audit trail of approvals

**Evidence:** Approval workflow configuration, approval logs

---

### AI.10.3 Action Logging

**Requirement:** Log all agent actions comprehensively.

**Log content:**
- Action requested
- Parameters
- Context/reasoning
- Outcome
- Timestamp
- Correlation ID

**Evidence:** Action logs

---

### AI.10.4 Checkpoints

**Requirement:** Validate intermediate results in multi-step agent workflows.

**Implementation:**
- Define checkpoint locations
- Validation criteria at each checkpoint
- Halt on validation failure
- Human review option at checkpoints

**Evidence:** Checkpoint configuration, validation logs

---

### AI.10.5 Rollback Capability

**Requirement:** Ability to undo agent actions where possible.

**Implementation:**
- Identify reversible vs irreversible actions
- Implement rollback for reversible actions
- Extra scrutiny for irreversible actions
- Rollback testing

**Evidence:** Rollback capability documentation, test records

---

### AI.10.6 Outcome Validation

**Requirement:** After an agent completes a task, independently validate that the outcome matches the intended goal and has no unintended side effects.

Agentic AI pursues goals across multiple steps, choosing its own actions. Validating individual actions (AG.2.1) is necessary but insufficient — an agent can take a series of individually valid actions that produce an unintended aggregate outcome.

**Implementation:**

| Control | Purpose |
|---------|---------|
| **Goal-outcome comparison** | Compare completed task outcome against the original goal/instruction |
| **Side effect detection** | Check for unintended changes to systems, data, or state |
| **Boundary verification** | Confirm agent stayed within its authorised scope |
| **Resource accounting** | Verify resources consumed are within expected bounds |
| **Downstream impact check** | Assess impact on systems that depend on modified data/state |

**Validation by tier:**

| Tier | Validation |
|------|-----------|
| CRITICAL | Automated outcome validation + human verification before results are committed |
| HIGH | Automated outcome validation; human review of exceptions |
| MEDIUM | Automated validation; spot-check human review |
| LOW | Automated validation |

**Evidence:** Outcome validation logs, exception reports, human verification records

---

## AI.11 Logging & Monitoring

### AI.11.1 Comprehensive Logging

**Requirement:** Log AI interactions for audit, investigation, and improvement.

**Log content by tier:**

| Tier | Logging Requirement |
|------|---------------------|
| CRITICAL | Full content, all metadata, tamper-evident, 7-year retention |
| HIGH | Full content, all metadata, 3-year retention |
| MEDIUM | Metadata, sampled content, 1-year retention |
| LOW | Basic metadata, 90-day retention |

**Full context capture:** Because AI is non-deterministic, reproducing an interaction requires capturing the complete context. Logs must include:

| Field | Purpose |
|-------|---------|
| Model version and provider | Know exactly which model produced the output |
| Temperature and parameters | Reproduce generation conditions |
| System prompt version | Know which instructions the model was following |
| Retrieved context (RAG) | Know what data the model had access to |
| User identity | Know who initiated the interaction |
| Timestamp | Know when the interaction occurred |
| Guardrail results | Know what was filtered or flagged |
| Full input and output | The actual interaction content |

Without full context capture, incident investigation is impossible — you cannot determine why the model produced a specific output.

**Evidence:** Log samples, retention compliance, context capture verification

---

### AI.11.2 Real-Time Monitoring

**Requirement:** Monitor AI systems for operational and security issues.

**Metrics:**

| Category | Metrics |
|----------|---------|
| Operational | Latency, throughput, error rate, availability |
| Security | Block rate, escalation rate, anomaly indicators |
| Quality | Judge scores, HITL findings, customer feedback |
| Cost | Inference spend, HITL hours |

**Evidence:** Monitoring dashboards

---

### AI.11.3 Alerting

**Requirement:** Alert on significant events and threshold breaches.

**Alert categories:**

| Category | Examples | Response |
|----------|----------|----------|
| Security | Injection spike, data leakage | Immediate |
| Quality | Judge escalation spike, quality drop | Same day |
| Operational | Latency increase, error spike | Per SLA |
| Cost | Budget threshold breach | Same day |

**Evidence:** Alert configuration, alert logs

---

## AI.12 Incident Response

### AI.12.1 AI-Specific Playbooks

**Requirement:** Develop incident response playbooks for AI-specific scenarios.

**Playbook scenarios:**
- Prompt injection campaign
- Data leakage detection
- Bias/fair lending alert
- Model manipulation suspected
- Judge failure
- Agent runaway

**Evidence:** Playbooks, tabletop exercise records

---

### AI.12.2 Investigation Capability

**Requirement:** Ability to investigate AI incidents effectively.

**Implementation:**
- Access to logs and Judge evaluations
- Ability to replay conversations
- Root cause analysis methodology
- Forensic preservation procedures

**Evidence:** Investigation reports

---

### AI.12.3 Remediation

**Requirement:** Remediate issues and prevent recurrence.

**Implementation:**
- Immediate containment options
- Guardrail updates
- Judge updates
- Process improvements
- Customer remediation (if harmed)

**Evidence:** Remediation records

---

### AI.12.4 Notification

**Requirement:** Notify stakeholders and regulators as required.

**Implementation:**
- Internal notification matrix
- Regulatory notification triggers
- Customer notification criteria
- Communication templates

**Evidence:** Notification records

---

## AI.13 AI Supplier Management

> **See [ISO 27001 Alignment](../regulatory/iso-27001-alignment.md) for detailed requirements.**

### AI.13.1 AI Vendor Assessment

**Requirement:** Assess AI vendors and foundation model providers for security.

**Implementation:**
- Security questionnaire for AI vendors
- Review of certifications (SOC 2, ISO 27001)
- Assessment of data handling practices
- Understanding of model provenance
- **Training data practices assessment** (what data was used, how was bias mitigated, what content filtering was applied)
- **Data retention policy** (does the provider retain your data? For how long? For what purpose?)
- **Model update notification** (how does the provider communicate changes to model behaviour?)

**Evidence:** Vendor assessment records, training data practice assessments

---

### AI.13.2 AI Vendor Agreements

**Requirement:** Include AI-specific terms in vendor agreements.

**Key terms:**
- Data processing and residency
- Model use restrictions (training on your data)
- Security requirements
- Incident notification
- Audit rights
- Zero-retention options for sensitive data
- Model deprecation notice periods
- Behavioural change notification requirements

**Evidence:** Contract terms

---

### AI.13.3 Model Provenance

**Requirement:** Document provenance of AI models used.

**Documentation:**
- Model identity and version
- Known training data sources (where disclosed)
- Known limitations and biases
- License terms
- Training data lineage where available; documented gap and compensating controls where unavailable

**Evidence:** Model documentation, provenance gap analysis

---

### AI.13.4 Training Data Risk Assessment

**Requirement:** Assess the risks associated with foundation model training data for each use case.

The behaviour of AI systems is shaped by training data you don't control and likely can't fully audit. Training data risks include inherited bias, embedded misinformation, copyright issues, and cultural assumptions.

**Assessment per model per use case:**

| Factor | Assessment |
|--------|-----------|
| **Bias risk** | Could training data bias affect this use case? (e.g., lending, hiring) |
| **Misinformation risk** | Could incorrect training data lead to harmful outputs in this domain? |
| **Copyright risk** | Could the model reproduce copyrighted content relevant to this use case? |
| **Cultural risk** | Is the use case sensitive to cultural context the training data may not represent? |
| **Recency risk** | Does the use case require current information the training data may lack? |

**Decision framework:**

| Risk Level | Action |
|------------|--------|
| Training data risk is low for this use case | Accept — document rationale |
| Training data risk is moderate | Mitigate — RAG grounding, output validation, bias testing |
| Training data risk is high | Avoid — use a different model, fine-tune on curated data, or don't use AI for this use case |

**Evidence:** Training data risk assessments per model per use case

---

## AI.14 AI Security Awareness

### AI.14.1 AI Security Training

**Requirement:** Train relevant personnel on AI security risks.

**Training by audience:**

| Audience | Content |
|----------|---------|
| All staff | AI acceptable use, recognising AI outputs, **confidence-competence gap** ("The AI sounds sure — that doesn't mean it's right") |
| AI developers | Secure AI development, prompt injection, adversarial testing |
| AI operators | Guardrails, HITL processes |
| HITL reviewers | **Cognitive bias training** (automation bias, anchoring bias, authority bias), how to challenge AI outputs, canary exercise participation |
| Security team | AI threat landscape, monitoring, novel AI risks |
| Executives | AI risk literacy, accountability for AI decisions |

**HITL-specific training:** Automation bias — the tendency to defer to AI even when human judgement is better — is the primary failure mode of human oversight. HITL reviewers must be specifically trained to recognise and counter this bias.

**Evidence:** Training records, cognitive bias assessment results

---

## AI.15 AI System Continuity

### AI.15.1 AI Continuity Planning

**Requirement:** Include AI systems in business continuity planning.

**Implementation:**
- AI system criticality classification
- Fallback procedures when AI unavailable
- Recovery time objectives for AI systems
- Vendor dependency planning

**Evidence:** BCP documentation

---

### AI.15.2 AI System Resilience

**Requirement:** Design AI systems for resilience.

**Implementation:**
- Graceful degradation
- Fallback models
- Circuit breakers (see AG.2.2)
- Timeout handling

**Evidence:** Architecture documentation

---

## AI.16 AI Intellectual Property

### AI.16.1 Model IP Protection

**Requirement:** Protect intellectual property in AI models.

**Implementation:**
- Access controls for custom models
- Encryption of model weights
- Protection of system prompts
- Licensing for model use

**Evidence:** IP inventory

---

### AI.16.2 Third-Party IP Compliance

**Requirement:** Ensure AI use complies with third-party IP rights.

**Implementation:**
- Foundation model license compliance
- Training data rights verification
- Guardrails for copyright compliance

**Evidence:** License compliance records

---

## Control Selection by Risk Tier

### Summary Matrix

| Control Family | CRITICAL | HIGH | MEDIUM | LOW |
|----------------|----------|------|--------|-----|
| AI.1 Governance | Full | Full | Standard | Basic |
| AI.2 Risk Management | Full | Full | Standard | Basic |
| AI.3 Inventory & Documentation | Full | Full | Standard | Registration |
| AI.4 Development Security | Full | Full | Standard | Basic |
| AI.5 Data Governance | Full | Full | Standard | Basic |
| AI.6 Model Security | Full | Full | Standard | Basic |
| AI.7 Guardrails | Full | Full | Standard | Basic |
| AI.8 LLM-as-Judge | 100% | 20-50% | 5-10% | Optional |
| AI.9 Human Oversight | All decisions | Escalations + sampling | Periodic | Spot checks |
| AI.10 Agentic Controls | Full (if applicable) | Full | Standard | Basic |
| AI.11 Logging & Monitoring | Full | Full | Standard | Basic |
| AI.12 Incident Response | Full | Full | Standard IT process | Standard IT process |

---

## Standards Mapping

| Control Family | ISO 42001 | NIST AI RMF | EU AI Act |
|----------------|-----------|-------------|-----------|
| AI.1 Governance | 5.1, 5.2 | GOVERN | Art. 9 |
| AI.2 Risk Management | 6.1 | MAP, MEASURE | Art. 9 |
| AI.3 Inventory | 7.1 | MAP | Art. 11 |
| AI.7 Guardrails | 8.2 | MANAGE | Art. 9, 15 |
| AI.8 Judge | 8.2 | MEASURE | Art. 9 |
| AI.9 Human Oversight | 8.4 | GOVERN | Art. 14 |
| AI.11 Logging | 9.1 | MEASURE | Art. 12 |
| AI.12 Incident Response | 10.1 | MANAGE | Art. 9 |

---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
