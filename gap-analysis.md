# Gap Analysis: Insights vs. Proposed Solution

**Repository:** enterprise-ai-security-framework  
**Date:** 2026-02-11

---

## Methodology

This analysis cross-references each of the 13 insight articles against the proposed solution (core controls, extensions, and templates) to identify where an insight raises a problem or challenge that the solution does not adequately address.

**Caveat:** This analysis is based on the README structure, insight summaries, and solution architecture visible from the repo's main page. Individual file contents were not accessible for deep inspection, so some gaps identified here may be partially addressed in ways not visible from the structural overview.

---

## Summary of Gaps

| # | Gap | Severity | Insight Source | Solution Component |
|---|-----|----------|----------------|-------------------|
| 1 | Verification of the judge itself | High | The Verification Gap | Controls (Judge layer) |
| 2 | Multimodal controls are theoretical only | High | Multimodal Breaks Guardrails | Emerging Controls |
| 3 | Streaming validation architecture | Medium | You Can't Validate Unfinished | Emerging Controls |
| 4 | Multi-agent trust and delegation chains | High | When Agents Talk to Agents | Agentic controls |
| 5 | Reasoning trace monitoring | Medium | When AI Thinks | Emerging Controls |
| 6 | Persistent memory / context accumulation | Medium | The Memory Problem | Controls |
| 7 | Anomaly detection operationalisation | Medium | Behavioral Anomaly Detection | Technical extensions (metrics) |
| 8 | SOC integration and escalation paths | Medium | Multiple insights | Templates |
| 9 | Supply chain and model provenance | High | Not covered in insights | Not covered in solution |
| 10 | Cost/latency trade-offs at scale | Medium | Not explicit | Not covered in solution |
| 11 | Data governance for RAG pipelines | High | Not explicit | Not covered in solution |
| 12 | Non-human identity lifecycle | Medium | Agentic insights | Agentic controls |
| 13 | Infrastructure-as-code enforcement gap | Low | Infrastructure Beats Instructions | Technical extensions |

---

## Detailed Analysis

### Gap 1: Who judges the judge?

**Insight:** "The Verification Gap" states that current safety approaches can't confirm ground truth.

**Problem:** The three-layer pattern (Guardrails → Judge → Human) relies heavily on the LLM-as-Judge layer for detecting "unknown-bad" outputs. But the insight itself acknowledges there's no way to confirm ground truth. The solution doesn't appear to address:

- How do you validate judge accuracy over time?
- What's the feedback loop from human decisions back into judge calibration?
- What happens when the judge model itself drifts, hallucinates, or is manipulated?
- Judge-on-judge evaluation (meta-evaluation) as a control

**Recommendation:** Add a "Judge Assurance" section to the controls doc covering evaluation metrics (precision, recall, agreement rates), periodic human calibration exercises, and adversarial testing of the judge itself.

---

### Gap 2: Multimodal controls exist only as theory

**Insight:** "Multimodal AI Breaks Your Text-Based Guardrails" identifies that images, audio, and video create new attack surfaces.

**Problem:** The README explicitly labels the Emerging Controls doc as "*(theoretical)*". The core controls (Guardrails, Judge, Human Oversight) appear to assume text-based I/O. There's no practical guidance for:

- Image-based prompt injection (text embedded in images)
- Audio/video content moderation in enterprise contexts
- Cross-modal attacks (benign text + malicious image)
- Multimodal guardrail tooling recommendations (the current-solutions.md may partially cover this, but it sits in extensions, not core)

**Recommendation:** Either promote multimodal to a core concern with practical controls, or explicitly scope the framework as text-only and create a roadmap for multimodal coverage.

---

### Gap 3: Streaming breaks the validation model

**Insight:** "You Can't Validate What Hasn't Finished" identifies that real-time streaming challenges the entire pattern.

**Problem:** The Guardrails → Judge → Human pattern assumes you can intercept a complete output. Streaming (token-by-token delivery) means:

- Guardrails can't evaluate an incomplete response
- The Judge can't score a partial output
- Users see content before any control layer acts
- Recall/retraction mechanisms aren't addressed

The solution appears to have no streaming-specific control architecture (chunked evaluation, progressive validation, client-side buffering strategies).

**Recommendation:** Add a streaming controls section covering buffered evaluation windows, progressive guardrail checks, and post-delivery remediation patterns.

---

### Gap 4: Multi-agent accountability is identified but under-solved

**Insight:** "When Agents Talk to Agents" identifies accountability gaps in multi-agent systems.

**Problem:** The Agentic controls doc exists but the README summary suggests it covers "additional controls for AI agents" (singular framing). Multi-agent systems introduce:

- Delegation chains: Agent A asks Agent B to do something Agent A isn't authorised to do
- Responsibility attribution: Which agent is accountable when a chain of agents produces a harmful outcome?
- Inter-agent trust boundaries: Should Agent A trust Agent B's output without its own guardrails?
- Cascading failures: One compromised agent corrupting downstream agents
- Protocol-level risks (MCP, A2A) that enable lateral movement between agents

**Recommendation:** Expand agentic controls to explicitly address multi-agent topologies, delegation trust policies, and chain-of-custody logging across agent interactions.

---

### Gap 5: Reasoning trace monitoring is acknowledged but not operationalised

**Insight:** "When AI Thinks Before It Answers" identifies that reasoning models need reasoning-aware controls.

**Problem:** Models like o1/o3 and Claude with extended thinking produce internal chain-of-thought that may contain problematic content even when the final output is clean. The solution doesn't appear to provide:

- Guidance on whether/how to monitor reasoning traces
- Controls for "thinking" that contradicts system instructions
- Detection of reasoning-level jailbreaks that don't surface in outputs
- Practical tooling for reasoning trace analysis (most providers don't expose full traces)

**Recommendation:** Address the practical reality that reasoning traces are often opaque, and provide guidance on what to do when you can't monitor them (compensating controls, output-focused evaluation heuristics).

---

### Gap 6: Memory and context accumulation risks lack controls

**Insight:** "The Memory Problem" identifies risks from long context and persistent memory.

**Problem:** The core controls appear to operate on a per-request basis. But persistent memory and long-context systems create:

- Gradual context poisoning across conversation turns
- Information leakage from accumulated context (user A's data surfacing for user B)
- Memory manipulation attacks (injecting false "memories" early in a conversation)
- No clear guidance on context window hygiene, memory sanitisation, or session isolation

**Recommendation:** Add context/memory controls covering session isolation, context window limits, memory sanitisation policies, and cross-session information leakage prevention.

---

### Gap 7: Behavioral anomaly detection lacks operational depth

**Insight:** "Behavioral Anomaly Detection" describes aggregating signals to detect drift from normal.

**Problem:** The technical extensions include "metrics" but the insight describes a capability (anomaly detection) that requires:

- Baseline establishment methodology (what is "normal" for a given AI system?)
- Statistical approaches for drift detection (not just metrics collection)
- Alert threshold calibration to avoid alert fatigue
- Integration with existing SIEM/SOC tooling
- Worked examples of anomaly detection in practice

The solution appears to stop at "collect metrics" rather than "detect and respond to anomalies."

**Recommendation:** Bridge the gap between metrics collection and actionable anomaly detection with baseline methodology, statistical models, and SIEM integration patterns.

---

### Gap 8: SOC integration is implicit, not explicit

**Insight:** Multiple insights (Infrastructure Beats Instructions, Behavioral Anomaly Detection, Humans Remain Accountable) imply the need for operational security integration.

**Problem:** The solution includes incident playbook templates but doesn't appear to address:

- How AI security alerts feed into existing SOC workflows
- Escalation triggers specific to AI systems (vs. generic security alerts)
- Identity correlation across AI telemetry sources (API gateway, LLM provider logs, application logs)
- Runbook integration with tools like PagerDuty, ServiceNow, Splunk
- Triage criteria: how does a SOC analyst decide if an AI alert is a true positive?

**Recommendation:** Add a SOC integration guide covering alert taxonomy, escalation matrices, identity correlation patterns, and triage procedures for AI-specific incidents. (Note: this aligns with your Bedrock/Databricks monitoring work — that content could feed directly into this gap.)

---

### Gap 9: Supply chain and model provenance — not even in the insights

**Insight:** None of the 13 insights explicitly address AI supply chain risk.

**Problem:** This is a significant blind spot in both the insights and the solution. Enterprise AI deployments depend on:

- Third-party model providers (OpenAI, Anthropic, Google, Meta)
- Open-source model downloads (Hugging Face, etc.)
- Model integrity verification (has the model been tampered with?)
- Training data provenance and contamination risks
- Dependency management for AI toolchains (LangChain, LlamaIndex, etc.)
- SBOM/AI-BOM requirements emerging from NDAA and EU AI Act

The OWASP LLM Top 10 covers supply chain risks, and the framework references OWASP, but the framework itself doesn't synthesise this into actionable controls.

**Recommendation:** Add an insight article on supply chain risk and a corresponding controls section or extension covering model provenance verification, dependency management, and AI-BOM generation.

---

### Gap 10: Cost and latency trade-offs are ignored

**Insight:** Not explicitly covered.

**Problem:** The three-layer pattern implies running a Judge (LLM-as-Judge) on potentially every interaction. At enterprise scale, this creates:

- Significant API costs (second LLM call per request)
- Latency budget consumption (~500ms–5s per the README)
- Sampling vs. exhaustive evaluation trade-offs
- Risk-proportionate evaluation (do you judge every internal chatbot response the same as a financial decision?)

The risk tiers framework partially addresses this (different controls for different tiers), but the operational cost/performance implications aren't surfaced.

**Recommendation:** Add practical guidance on evaluation sampling strategies, cost modelling for judge deployment, and risk-proportionate evaluation frequency.

---

### Gap 11: Data governance for RAG pipelines is absent

**Insight:** Not explicitly covered.

**Problem:** RAG (retrieval-augmented generation) is the dominant enterprise AI pattern, but the framework doesn't appear to address:

- Data poisoning through corrupted retrieval sources
- Access control for retrieved documents (user sees data they shouldn't via RAG)
- Data lineage and attribution in RAG outputs
- Chunk-level access control vs. document-level access control
- Embedding store security and integrity

This is arguably the #1 practical risk in enterprise AI deployments today and it falls between the framework's scope (runtime monitoring) and what it excludes (model training).

**Recommendation:** Add RAG-specific controls covering retrieval access control, document-level authorisation enforcement, embedding store integrity, and data poisoning detection.

---

### Gap 12: Non-human identity lifecycle is thin

**Insight:** "When Agents Talk to Agents" and the agentic controls touch on this.

**Problem:** AI agents operate as non-human identities (NHIs) that need:

- Identity provisioning and deprovisioning lifecycle
- Credential management (API keys, tokens, certificates)
- Session management and timeout policies
- Privilege escalation detection
- MCP server trust boundaries and tool authorisation
- Audit trail linking agent identity to human principal

The agentic controls doc likely covers permissions, but the full NHI lifecycle management may be underweight given how central this is to regulated environments.

**Recommendation:** Expand agentic controls to cover the full NHI lifecycle, or add an extension mapping to existing IAM frameworks (NIST SP 800-63, enterprise IAM patterns).

---

### Gap 13: Infrastructure-as-code enforcement

**Insight:** "Infrastructure Beats Instructions" argues you can't secure systems with prompts alone.

**Problem:** The insight is strong, but the solution may not fully operationalise it. Questions:

- Does the controls doc demonstrate infrastructure-level enforcement (network policies, API gateway rules, platform guardrails) or does it lean on application-level controls?
- Are the technical extensions prescriptive about infrastructure patterns (Terraform modules, CloudFormation templates, Kubernetes policies)?
- Is there a clear distinction between controls that are enforced at infrastructure level vs. application level?

**Recommendation:** Ensure the controls explicitly tag each control as infrastructure-enforced vs. application-enforced, and provide IaC examples where possible.

---

## Cross-cutting Observations

1. **The "theoretical" label on Emerging Controls is a liability.** Three insights (multimodal, reasoning, streaming) point to real, current risks but the corresponding solution content is explicitly labelled as theoretical. This undermines the framework's positioning as a "practical guide."

2. **The insights are stronger than the solution.** The insight articles identify sharp, well-articulated problems. The solution architecture (three-layer pattern) is elegant but may be under-specified for the harder problems the insights raise.

3. **Scope exclusion of RAG and supply chain creates a false sense of completeness.** The framework excludes model training and vendor products, which is reasonable. But RAG data governance and supply chain integrity sit squarely within "custom LLM applications" (which is in scope) and are missing.

4. **The framework is control-centric but light on detection and response.** The pattern is Prevent (Guardrails) → Detect (Judge) → Decide (Human). But the operational bridge between detection and response (SOC integration, alert triage, incident response) is handled only via templates, not as a core concern.

5. **Regulatory mapping may not be enough.** The extensions map to ISO 42001 and EU AI Act. But gaps 9 (supply chain) and 11 (RAG data governance) are both areas where regulators are actively developing requirements. The framework should anticipate these.

---

## Prioritised Recommendations

| Priority | Gap | Action |
|----------|-----|--------|
| **P1** | Supply chain / model provenance (#9) | New insight + new extension |
| **P1** | RAG data governance (#11) | New extension or core section |
| **P1** | Multi-agent accountability (#4) | Expand agentic controls |
| **P2** | Judge assurance / meta-evaluation (#1) | Add to controls doc |
| **P2** | SOC integration (#8) | New extension (leverage Bedrock/Databricks work) |
| **P2** | Multimodal — practical not theoretical (#2) | Promote from emerging to actionable |
| **P3** | Memory/context controls (#6) | Add to controls doc |
| **P3** | Anomaly detection operationalisation (#7) | Expand technical extensions |
| **P3** | Streaming validation (#3) | Add to controls or emerging |
| **P3** | Cost/latency guidance (#10) | Add to implementation guide |
| **P4** | NHI lifecycle (#12) | Expand agentic or add extension |
| **P4** | Reasoning trace monitoring (#5) | Update emerging controls |
| **P4** | IaC enforcement patterns (#13) | Add examples to technical extensions |
