# From Idea to Production to Ongoing Control

*The end-to-end process: strategy to use case to tool selection to risk tiering to deployment to ongoing governance. One flow, no gaps.*

> Part of [From Strategy to Production](./)

---

## Why This Matters

The framework has excellent depth in each domain — risk tiers, controls, PACE resilience, governance. But there's no single document that connects the entire lifecycle from "someone has an idea" to "the system is running safely in production and being continuously governed."

Without that connected flow, organisations experience gaps:

- Ideas become deployments without passing through risk classification
- Systems launch without controls because nobody triggered the governance process
- Operating teams inherit systems without knowing what to monitor or when to escalate
- Use cases evolve without triggering reassessment
- Tools get selected before anyone asks whether AI is the right approach

This article defines the complete process. Every stage has a clear output that feeds the next stage. Every handoff has a named owner. Every decision point has criteria.

---

## The End-to-End Flow

![Idea to Production Flow](../images/strategy-idea-to-production.svg)

Eight stages. Each produces a defined output. Each has guardrails that prevent mistakes, detect gaps, and absorb failure if stages are rushed or skipped.

| Stage | Activity | Output | Guardrail |
|-------|----------|--------|-----------|
| **1. Strategic Alignment** | Is this worth doing? | Business case | Detect: systems without business cases surface in governance reviews |
| **2. Use Case Definition** | What exactly will it do? | Completed use case definition | Prevent: ten questions steer toward complete definitions |
| **3. Tool Selection** | Is AI the right approach? | Technology decision | Prevent: Use Case Filter steers to right tool early |
| **4. Risk Classification** | What tier does this sit at? | Scored risk profile + tier | Detect: unclassified systems visible in registry |
| **5. Control Design** | What controls does this tier need? | Control specification + PACE plan | Prevent: approved platforms inherit baseline controls |
| **6. Build & Test** | Implement the system and controls | Working system with controls | Detect: pre-deployment checks surface gaps |
| **7. Deploy & Operate** | Launch and run | Operating system with monitoring | Absorb: gradual rollout contains blast radius |
| **8. Ongoing Governance** | Monitor, review, evolve | Continuous assurance | Detect: continuous monitoring surfaces drift |

---

## Stage 1: Strategic Alignment

**Owner:** Business sponsor

**Purpose:** Determine whether this initiative is worth pursuing — before any technical work begins.

**Inputs:**
- Business problem or opportunity
- Strategic context (see [Business Alignment](business-alignment.md))

**Activities:**
- Define the business problem in measurable terms
- Assess whether the problem justifies investment
- Identify at least two alternative approaches (see below, Stage 3)
- Estimate the value of solving it

**Output: Business Case**

| Field | Content |
|-------|---------|
| Problem statement | What's the problem, measured in current cost/impact? |
| Proposed approach | High-level solution concept |
| Expected value | Quantified benefit (cost reduction, revenue, efficiency) |
| Strategic alignment | How does this connect to organisational strategy? |
| Initial risk sense | Gut-level: is this low, medium, or high risk? |
| Sponsor | Named executive sponsor |

**Guardrail:** Systems that reach production without a business case become visible during governance reviews — they can't demonstrate value and generate monitoring noise. The environment doesn't block teams from exploring, but it makes unjustified investment visible.

**What can go wrong here:**
- Skip this stage → technology investment without business justification
- Vague problem statement → impossible to measure success later
- No alternatives considered → commitment to AI before evaluating options

---

## Stage 2: Use Case Definition

**Owner:** Business owner + AI engineer (collaborative)

**Purpose:** Translate the business case into a specific, assessable use case definition.

**Inputs:**
- Business case from Stage 1

**Activities:**
- Complete the [ten-question use case definition](use-case-definition.md)
- Define explicit positive and negative scope
- Identify data requirements and access needs
- Determine user population and expected volume
- Identify regulatory context
- Name the accountable business owner

**Output: Completed Use Case Definition**

The full template from [Use Case Definition](use-case-definition.md). All ten questions answered. No "TBD" in critical fields.

**Guardrail:** The ten questions are the preventive control — they steer teams toward completeness. If fields are left as "TBD," downstream controls will be misconfigured and monitoring will surface the mismatch. Review by business owner, legal/compliance, and data owner improves quality but isn't a hard stop — incomplete definitions reveal themselves in operation.

**What can go wrong here:**
- Incomplete definition → uncertain risk tier → wrong controls
- Negative scope missing → guardrails can't enforce boundaries
- Understated decision authority → system classified too low
- "TBD" in regulatory context → compliance surprise at launch

---

## Stage 3: Tool Selection

**Owner:** Technical lead + business owner

**Purpose:** Determine whether AI is the right tool — and if so, what kind.

This stage explicitly evaluates alternatives. The framework's [first control](../insights/the-first-control.md) is choosing the right tool.

**Inputs:**
- Completed use case definition from Stage 2

**Activities:**

### The Tool Selection Decision Tree

![Tool Selection Decision Tree](../images/strategy-tool-selection.svg)

| Question | If Yes | If No |
|----------|--------|-------|
| **Can this be solved with deterministic rules?** | Use rules engine, workflow automation, or traditional code | Continue |
| **Does it require understanding unstructured input (natural language, images)?** | AI is likely appropriate | Consider RPA or structured automation |
| **Does it require pattern recognition across large datasets?** | AI/ML is likely appropriate | Consider traditional analytics |
| **Does it need to generate novel content or responses?** | Generative AI is appropriate | Consider retrieval + templating |
| **Does the use case require real-time, non-deterministic reasoning?** | LLM-based AI is appropriate | Consider traditional ML models |

### The Five Options

| Option | When To Use | Risk Profile | Framework Implication |
|--------|------------|-------------- |----------------------|
| **Traditional software** | Deterministic logic, bounded inputs, exact outputs needed | Lowest — existing SDLC applies | Outside framework scope |
| **RPA / workflow automation** | Structured, repeatable processes; UI-based integration | Low — deterministic, auditable | Outside framework scope |
| **Traditional ML** | Pattern recognition on structured data; classification, regression | Low–Medium — predictable, testable | Partial framework (monitoring, bias) |
| **LLM / Generative AI** | Unstructured input, natural language, content generation | Medium–Critical (depends on use case) | Full framework applies |
| **Multi-agent AI** | Complex workflows requiring multiple AI components collaborating | High–Critical | Full framework + [MASO](../maso/) |

### The Hybrid Reality

Most real-world solutions are hybrid. A customer service system might use:
- **Traditional code** for authentication and session management
- **Rules engine** for routing queries to the right department
- **LLM** for understanding the customer's intent and drafting responses
- **Traditional database** for account lookups
- **Deterministic logic** for executing any account actions

The framework applies to the AI components. The risk tier is determined by what the AI does, not by the entire system.

**Key principle from [The First Control](../insights/the-first-control.md):** "AI proposes. Deterministic systems dispose." Wherever possible, use AI for cognition (understanding, drafting, recommending) and deterministic systems for action (executing, committing, approving). This naturally constrains the AI's blast radius and often reduces the risk tier.

**Output: Technology Decision**

| Field | Content |
|-------|---------|
| Selected approach | AI, RPA, traditional, or hybrid (specify which components are AI) |
| Justification | Why this approach over alternatives |
| AI components | If hybrid, which parts use AI and which don't |
| AI type | LLM, traditional ML, multi-agent, or combination |
| Platform/provider | Managed service, self-hosted, vendor product |
| Risk implication | How tool selection affects risk tier |

**Guardrail:** The Use Case Filter is the preventive control — it steers teams to the right tool before investment begins. If a team skips it and builds AI where rules would suffice, the overhead becomes visible in operation: unnecessary guardrail tuning, Judge findings on deterministic tasks, governance cost that simpler tools wouldn't generate. If the decision is "not AI," the initiative exits to standard SDLC.

---

## Stage 4: Risk Classification

**Owner:** Product owner (with risk function available for support)

**Purpose:** Classify the risk tier using the framework's six-dimension scoring model.

**Inputs:**
- Completed use case definition (Stage 2)
- Technology decision (Stage 3)

**Activities:**
- Score each dimension (Decision Authority, Reversibility, Data Sensitivity, Audience, Scale, Regulatory)
- Apply scoring rules (highest dimension wins; adjacent HIGHs compound)
- Apply use case modifiers (agentic, customer-facing, regulated, batch)
- Check [Fast Lane](../FAST-LANE.md) qualification (all four criteria met → Fast Lane)
- Document the classification with justification per dimension
- For AI-assisted classification, review the AI's proposed scores (see [Use Case Definition](use-case-definition.md))

**Output: Scored Risk Profile**

| Dimension | Score | Justification |
|-----------|-------|---------------|
| Decision Authority | e.g., HIGH | AI recommendations directly shape fraud investigation priority |
| Reversibility | e.g., MEDIUM | Incorrect prioritisation is recoverable but may delay detection |
| Data Sensitivity | e.g., CRITICAL | Processes transaction data including cardholder PII |
| Audience | e.g., MEDIUM | Internal fraud analysts |
| Scale | e.g., HIGH | 80,000 transactions/day |
| Regulatory | e.g., HIGH | PCI-DSS, banking regulations |
| **Overall Tier** | **CRITICAL** | Data sensitivity drives the tier |

**Guardrail:** Unclassified systems are visible in the use case registry — they stand out because they have no tier, no controls, and no monitoring baseline. Classification is self-service: teams score the dimensions, the tier follows from the scores, and the corresponding controls auto-apply from the platform. The classification process is lightweight enough that skipping it costs more than doing it.

If a team misclassifies — scores optimistically, underestimates data sensitivity, misjudges decision authority — the consequences fall on the product owner who signed off the classification. This is the accountability model described in [Security as Enablement](../insights/security-as-enablement.md): the product owner decides, accepts the risk, and accepts the consequences. The platform provides the controls; the product owner determines which ones apply.

Organisations may choose to add governance review for higher-risk classifications. That is a governance decision. The framework's default is self-service classification with detection of misalignment through runtime monitoring.

**What can go wrong here:**
- Optimistic scoring → system under-controlled → product owner accountable
- Dimension ambiguity not investigated → hidden risk
- Fast Lane self-certification when criteria aren't clearly met → under-governed system

---

## Stage 5: Control Design

**Owner:** Engineering team (with security architecture support)

**Purpose:** Translate the risk tier into a specific control specification for this system. On approved platforms, most of this is automatic — the platform inherits baseline controls per tier, and the team configures use-case-specific settings on top.

**Inputs:**
- Scored risk profile (Stage 4)
- Use case definition (Stage 2)
- Technology decision (Stage 3)

**Activities:**
- Select controls from the [control matrix](../core/risk-tiers.md) based on tier
- Apply modifiers from the [control selection guide](../extensions/technical/control-selection-guide.md)
- Design the PACE resilience plan — Primary, Alternate, Contingency, Emergency states
- Specify guardrail configuration (what to block, what to allow)
- Define Judge evaluation criteria (what "good" and "bad" look like for this use case)
- Specify HITL requirements (who reviews, SLA, escalation path)
- Size operational requirements (HITL staff, Judge compute, log storage)
- If agentic: specify tool access controls, sandbox boundaries, delegation limits
- If multi-agent: apply [MASO](../maso/) controls at the appropriate tier

**Output: Control Specification**

| Control Area | Specification |
|--------------|---------------|
| **Guardrails — Input** | Topic rules, injection detection, PII detection, rate limiting (specific config) |
| **Guardrails — Output** | Content filtering, PII handling, confidence thresholds (specific config) |
| **Judge** | Evaluation criteria, sampling rate, escalation rules, Judge model selection |
| **HITL** | Reviewer role, SLA, escalation path, review criteria |
| **PACE** | P/A/C/E states with transition triggers, fallback process, kill switch |
| **Logging** | Content scope, retention period, access controls |
| **Monitoring** | Dashboards, alerts, anomaly thresholds |
| **Incident response** | Playbook reference, severity mapping, notification requirements |

**Guardrail:** Teams building on approved platforms inherit baseline controls automatically — logging, monitoring, and standard guardrails come with the platform. The control specification adds use-case-specific configuration on top. The platform defaults mean even a rushed deployment starts with basic protection. Security architecture support is available to strengthen the design, particularly for novel use cases — but the platform does the heavy lifting, not a governance function.

---

## Stage 6: Build and Test

**Owner:** Engineering team

**Purpose:** Implement the system and its controls, and verify they work.

**Inputs:**
- Control specification (Stage 5)
- Technology decision (Stage 3)

**Activities:**
- Build the AI system (model integration, data pipelines, UI)
- Implement guardrails per specification
- Configure Judge evaluation (prompts, sampling, routing)
- Set up HITL workflows and queues
- Configure logging and monitoring
- Implement PACE transitions (feature flag, fallback activation)
- Test against the [testing guidance](../extensions/templates/testing-guidance.md)
- Run pre-deployment checklist

**Pre-Deployment Checklist:**

| Check | Verified By | Status |
|-------|------------|--------|
| Use case definition matches implementation | Engineering | |
| Risk tier is current (no scope changes during build) | Product owner | |
| Input guardrails active and tested | Engineering | |
| Output guardrails active and tested | Engineering | |
| Judge evaluation configured and tested (shadow mode) | Engineering | |
| HITL workflow functional; reviewers trained | Operations | |
| PACE transitions tested (feature flag, fallback) | Engineering | |
| Logging captures required data at required retention | Engineering | |
| Monitoring dashboards and alerts configured | Operations | |
| Incident response playbook exists and is known | Operations | |
| Manual fallback process documented and tested | Product owner | |
| Kill switch operational | Engineering | |
| Regulatory/compliance requirements addressed | Product owner | |

**Guardrail:** The pre-deployment checklist is a detective control — it surfaces gaps before they reach production. Ideally it is automated as part of the deployment pipeline, not performed as a manual review ceremony. Items that aren't verified generate findings, not blockers. The feature flag and PACE plan mean a deployment that discovers problems can be rolled back quickly. Organisations may choose to add governance review before go-live for higher-risk systems — that is a governance decision, not a framework requirement.

**What can go wrong here:**
- Controls implemented but not tested → false confidence
- Judge in shadow mode never switches to active → no detection
- HITL reviewers assigned but not trained → [Human Factors](human-factors.md) failure
- PACE plan documented but transitions never tested → plan doesn't work under pressure
- Scope changed during build, risk tier not reassessed → running at wrong tier

---

## Stage 7: Deploy and Operate

**Owner:** Technical operations + business owner

**Purpose:** Launch the system and transition to steady-state operations.

**Inputs:**
- Tested system with verified controls (Stage 6)

**Activities:**
- Deploy to production (gradual rollout for HIGH/CRITICAL)
- Activate Judge evaluation (move from shadow to active)
- Begin HITL operations
- Monitor control effectiveness
- Tune guardrails based on initial false positive/negative data
- Calibrate Judge accuracy against HITL decisions
- Verify logging and alerting in production

**Deployment Pattern by Tier:**

| Tier | Deployment Approach | Rationale |
|------|---------------------|-----------|
| Fast Lane | Ship it | Low risk; feature flag is the safety net |
| LOW | Standard release | Basic monitoring sufficient |
| MEDIUM | Canary or staged rollout | Monitor Judge findings before full traffic |
| HIGH | Gradual rollout with enhanced monitoring | Watch for unexpected patterns at scale |
| CRITICAL | Phased rollout with monitoring checkpoints | Each phase verified stable before expansion |

**First 30 Days:**

| Activity | When | Owner |
|----------|------|-------|
| Daily guardrail effectiveness review | Day 1–14 | Security |
| Daily Judge finding review | Day 1–30 | Operations |
| HITL SLA compliance check | Daily | Governance |
| False positive rate assessment | Day 7, 14, 30 | Security |
| Judge accuracy calibration | Day 14, 30 | Operations |
| Operational review with business owner | Day 7, 14, 30 | All |
| First PACE transition test | Day 30 | Engineering |

**Guardrail:** Gradual rollout is the absorb control — it contains the blast radius of unexpected behaviour. The first 30 days of monitoring generate the baseline that ongoing governance uses. If calibration reveals problems, the deployment can be paused or rolled back without affecting the full user population. Operational handover to the steady-state team happens when monitoring confirms stability, not on a fixed schedule.

---

## Stage 8: Ongoing Governance

**Owner:** AI governance function (2nd line) + business owner (1st line)

**Purpose:** Continuously assure that the system operates within its defined risk profile and that the risk profile remains current.

**Inputs:**
- Operating system from Stage 7
- Use case definition (maintained as a living document)

**Activities — Continuous:**

| Activity | Frequency | Owner | Output |
|----------|-----------|-------|--------|
| Guardrail effectiveness monitoring | Real-time | Technical ops | Block rates, false positive rates |
| Judge finding triage | Daily | Operations | Escalations, patterns |
| HITL SLA monitoring | Daily | Governance | Compliance reports |
| Anomaly detection | Continuous | Security/SOC | Alerts on drift |
| Usage monitoring | Weekly | Operations | Volume trends, user patterns |

**Activities — Periodic:**

| Activity | Frequency | Owner | Output |
|----------|-----------|-------|--------|
| Judge accuracy calibration | Weekly (HIGH/CRITICAL), Monthly (MEDIUM) | Technical ops | Calibration adjustments |
| Control effectiveness review | Quarterly | Governance | Effectiveness report |
| Use case reassessment | Annual minimum; triggered by changes | Risk analyst | Updated risk profile |
| PACE transition test | Quarterly (CRITICAL), Bi-annual (HIGH), Annual (MEDIUM/LOW) | Engineering | Test results |
| Manual fallback exercise | Bi-annual | Business owner | Fallback verified |
| Regulatory alignment check | Annual + on regulatory change | Legal/Compliance | Compliance status |
| Human factors assessment | Annual | Governance | Reviewer competence, deskilling check |

**Activities — Event-Driven:**

| Trigger | Activity | Owner |
|---------|----------|-------|
| AI incident | [Incident playbook](../extensions/templates/ai-incident-playbook.md) activation | Incident team |
| Scope change request | Use case reassessment → possible reclassification | Business owner + risk |
| Model change | Control configuration review | Security |
| Data access change | Data sensitivity reassessment | Risk + data owner |
| Regulatory change | Compliance impact assessment | Legal + governance |
| Volume threshold breach | Operational sizing review | Operations |
| Judge accuracy drop | Recalibration or investigation | Technical ops |
| HITL SLA breach | Root cause analysis | Governance |

### The Operational Dashboard

What the team and any governance function needs to see (this data should be visible to the owning team first, not compiled into a governance report about them):

| Metric | Source | Frequency | Target |
|--------|--------|-----------|--------|
| Systems by tier | Use case registry | Monthly | Complete coverage |
| Control implementation % | Control tracking | Monthly | 100% |
| HITL SLA compliance | Queue metrics | Monthly | >95% |
| Judge accuracy | Calibration data | Monthly | >80% agreement with HITL |
| Open escalations | Escalation log | Monthly | Trending down |
| Incidents by severity | Incident log | Monthly | Trending down |
| False positive rate | Guardrail metrics | Monthly | <5% |
| Use cases overdue for review | Registry | Monthly | 0 |
| Shadow AI discovered | Discovery tools | Monthly | Trending down |

### When to Stop

Systems should be retired when:
- The business case no longer holds
- The risk exceeds the organisation's appetite and can't be reduced
- A better solution exists (AI or otherwise)
- Regulatory changes make the use case non-viable
- The organisation can no longer safely operate the controls

**Retirement process:**
1. Governance approves retirement
2. Users notified with timeline
3. Manual fallback activated permanently
4. Data retention obligations confirmed
5. System decommissioned
6. Use case moved to "Retired" in registry
7. Post-retirement review documented (lessons learned)

---

## The Complete Lifecycle — Summary

```
STAGE 1: STRATEGIC ALIGNMENT
  Input:     Business problem
  Output:    Business case
  Guardrail: Detect — unjustified systems visible in governance reviews
     │
STAGE 2: USE CASE DEFINITION
  Input:     Business case
  Output:    Ten-question use case definition
  Guardrail: Prevent — ten questions steer toward completeness
     │
STAGE 3: TOOL SELECTION
  Input:     Use case definition
  Output:    Technology decision (AI / RPA / traditional / hybrid)
  Guardrail: Prevent — Use Case Filter steers to right tool
  Exit:      If not AI → standard SDLC
     │
STAGE 4: RISK CLASSIFICATION
  Input:     Use case definition + technology decision
  Output:    Six-dimension scored risk profile + tier
  Guardrail: Detect — unclassified systems visible in registry
     │
STAGE 5: CONTROL DESIGN
  Input:     Risk profile + use case + technology
  Output:    Control specification + PACE plan
  Guardrail: Prevent — approved platforms inherit baseline controls
     │
STAGE 6: BUILD & TEST
  Input:     Control specification
  Output:    Working system with verified controls
  Guardrail: Detect — checklist surfaces gaps before production
     │
STAGE 7: DEPLOY & OPERATE
  Input:     Tested system
  Output:    Production system with active monitoring
  Guardrail: Absorb — gradual rollout contains blast radius
     │
STAGE 8: ONGOING GOVERNANCE
  Input:     Production system
  Output:    Continuous assurance
  Guardrail: Detect — continuous monitoring surfaces drift
  Loop:      Periodic review → reassessment → control adjustment
  Exit:      Retirement when appropriate
```

---

## How the Framework Maps to This Flow

| Stage | Primary Framework Documents |
|-------|-----------------------------|
| 1. Strategic Alignment | [Business Alignment](business-alignment.md), [The First Control](../insights/the-first-control.md) |
| 2. Use Case Definition | [Use Case Definition](use-case-definition.md), [Model Card Template](../extensions/templates/model-card-template.md) |
| 3. Tool Selection | [The First Control](../insights/the-first-control.md), [Risk Tier Is Use Case](../insights/risk-tier-is-use-case.md) |
| 4. Risk Classification | [Risk Tiers](../core/risk-tiers.md), [Control Selection Guide](../extensions/technical/control-selection-guide.md), [Fast Lane](../FAST-LANE.md) |
| 5. Control Design | [Controls](../core/controls.md), [PACE Resilience](../PACE-RESILIENCE.md), [Threat Model Template](../extensions/templates/threat-model-template.md) |
| 6. Build & Test | [Quick Start](../QUICK_START.md), [Implementation Guide](../IMPLEMENTATION_GUIDE.md), [Testing Guidance](../extensions/templates/testing-guidance.md) |
| 7. Deploy & Operate | [Governance Operating Model](../extensions/regulatory/ai-governance-operating-model.md), [SOC Integration](../extensions/technical/soc-integration.md) |
| 8. Ongoing Governance | [Governance Operating Model](../extensions/regulatory/ai-governance-operating-model.md), [Anomaly Detection](../extensions/technical/anomaly-detection-ops.md) |

---

## Where the Process Shortens

Not every initiative needs all eight stages at full depth.

| Scenario | Shortened Process |
|----------|-------------------|
| **Fast Lane deployment** | Stage 1 (brief) → Stage 2 (ten questions) → Stage 3 (confirm AI) → Stage 4 (self-certify Fast Lane) → Stage 6 (basic guardrails + logging + feature flag) → Stage 7 (deploy) → Stage 8 (annual review) |
| **Vendor SaaS product** | Stages 1–4 as normal → Stage 5 (map vendor controls to framework; identify gaps) → Stage 6 (configure, don't build) → Stages 7–8 as normal |
| **Upgrading existing system** | Skip Stage 1 (already justified) → Stage 2 (update definition with changes) → Stage 3 (already decided) → Stage 4 (reclassify) → Stages 5–7 (implement new controls) → Stage 8 (continue) |
| **POC / Experiment** | Stage 1 (brief) → Stage 2 (minimal) → Stage 3 (confirm AI) → Stage 4 (classify as LOW + time-bound) → Stage 6 (basic controls) → Stage 7 (limited deployment) → Fixed end date (no Stage 8 — either promote to full process or retire) |

---

## Where the Framework Doesn't Cover This Flow

| Gap | What's Missing | Impact |
|-----|----------------|--------|
| **No formal Stage 1 guidance** | The framework doesn't help evaluate business cases | Organisations commit to AI without evaluating alternatives |
| **No use case definition template** | Risk tiers assume a defined use case but don't provide the definition format | Classification happens on incomplete information |
| **No tool selection methodology** | "AI or not AI?" is addressed in one insight article but not as a formal decision point | AI gets selected by default |
| **No deployment guidance** | Implementation guide covers tools, not deployment patterns | Organisations deploy CRITICAL systems without gradual rollout |
| **No retirement process** | The framework covers the system lifecycle but not end-of-life | Systems run indefinitely without reassessment |

This article and [Use Case Definition](use-case-definition.md) fill these gaps. The flow defined here can be used as the operational process that connects the framework's components into a coherent lifecycle.

---

*AI Runtime Behaviour Security, 2026 (Jonathan Gill).*
