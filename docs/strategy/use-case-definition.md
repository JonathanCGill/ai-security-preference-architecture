# Use Case Definition

*What makes a good AI use case definition from a security and governance perspective — and how to translate one into a risk profile, control set, and operating model.*

> Part of [AI Strategy](./)

---

## Why Use Case Definition Matters

Every AI system starts as a use case. "We want AI to help with customer queries." "We want AI to analyse contracts." "We want AI to detect fraud."

These are not use cases. They're aspirations. A use case — one that security, governance, and risk functions can actually work with — is something far more specific. It defines not just what the AI does, but what data it touches, who sees its outputs, what decisions it influences, what happens when it's wrong, and who is accountable.

The difference matters because the framework's entire control model flows from the use case. The [risk tier](../core/risk-tiers.md) determines which controls apply. The risk tier is determined by the use case. A vague use case produces an uncertain risk tier, which produces either too many controls (killing the business case) or too few (creating unmanaged risk).

**A well-defined use case is the single most valuable input to the entire framework.**

---

## What Makes a Good Use Case Definition

### The Minimum Viable Use Case

A use case definition that security and governance can work with answers ten questions. These aren't optional fields on a form — each one directly determines a control requirement.

![Use Case Definition Model](../images/strategy-use-case-model.svg)

| # | Question | What It Determines | Framework Connection |
|---|----------|-------------------|---------------------|
| 1 | **What does it do?** | Scope boundaries, guardrail topic rules | [Controls](../core/controls.md) — guardrail configuration |
| 2 | **What decisions does it make or influence?** | Decision authority dimension → risk tier | [Risk Tiers](../core/risk-tiers.md) — authority scoring |
| 3 | **What data does it access?** | Data sensitivity dimension → risk tier, DLP rules | [Data Protection](../infrastructure/controls/data-protection.md) |
| 4 | **Who are the users?** | Audience dimension → risk tier, access controls | [Identity & Access](../infrastructure/controls/identity-and-access.md) |
| 5 | **What happens when it's wrong?** | Reversibility dimension → risk tier, PACE plan | [PACE Resilience](../PACE-RESILIENCE.md) |
| 6 | **What's the expected volume?** | Operational sizing — HITL staffing, Judge compute, logging storage | [Governance Model](../extensions/regulatory/ai-governance-operating-model.md) |
| 7 | **What regulatory context applies?** | Regulatory dimension → risk tier, compliance controls | [Regulatory Extensions](../extensions/regulatory/) |
| 8 | **What tools or actions can it take?** | Agentic scope → agentic controls, execution boundaries | [Agentic Controls](../core/agentic.md) |
| 9 | **Where does it sit in the business process?** | Integration points, upstream/downstream dependencies | [Threat Model Template](../extensions/templates/threat-model-template.md) |
| 10 | **Who is accountable for its outputs?** | Governance ownership, HITL escalation, incident response | [Governance Operating Model](../extensions/regulatory/ai-governance-operating-model.md) |

### Why Each Question Matters

**Q1: What does it do?**

Not "what could it do" or "what might it do eventually." What does it do *in this deployment*? Scope creep is the primary reason use cases drift from one risk tier to another without anyone noticing.

| Bad | Good |
|-----|------|
| "AI customer assistant" | "Answers customer questions about account balances, transaction history, and product information via chat. Cannot transfer funds, change settings, or provide personalised financial advice." |
| "Contract analysis tool" | "Extracts key dates, parties, and obligation clauses from supplier contracts. Outputs a structured summary for procurement review. Does not recommend accept/reject decisions." |
| "Fraud detection" | "Scores incoming card transactions against fraud patterns. Transactions scoring above 0.85 are held for human review. Transactions below 0.85 proceed automatically. Does not block transactions independently." |

The good definitions include explicit **negative scope** — what the system *cannot* do. This is critical for guardrail configuration. If the use case says "does not provide financial advice," then the guardrails enforce that boundary. Without that boundary in the definition, nobody knows to configure it.

**Q2: What decisions does it make or influence?**

The most important question for risk classification. The framework distinguishes between four levels of decision authority:

| Level | Description | Example | Typical Tier Impact |
|-------|-------------|---------|---------------------|
| **Informational** | Provides information; no decision influence | Search results, summaries, FAQ answers | LOW–MEDIUM |
| **Advisory** | Recommends a decision; human decides | "Consider reviewing this transaction" | MEDIUM–HIGH |
| **Influential** | Output significantly shapes human decision | Risk score that determines investigation priority | HIGH |
| **Autonomous** | AI makes the decision; human monitors | Auto-approved claims under £500 | HIGH–CRITICAL |

Many use cases claim to be "advisory" when they're functionally "influential." If the human reviewer accepts the AI recommendation 95% of the time without independent analysis, the system is influential regardless of what the process document says. Be honest about actual decision authority, not theoretical.

**Q3: What data does it access?**

Not just what data it *needs* — what data it *can access*. If the system has a database connection with broader permissions than the use case requires, the risk profile reflects the access, not the intent.

| Data Category | Examples | Risk Impact |
|---------------|----------|-------------|
| **Public** | Product info, published policies, public content | Minimal |
| **Internal** | Internal docs, meeting notes, project data | Low–Medium |
| **Confidential** | Strategy docs, financial forecasts, trade secrets | Medium–High |
| **PII** | Names, addresses, contact details | High (GDPR, privacy) |
| **Sensitive PII** | Health data, biometrics, financial records | High–Critical |
| **Regulated** | Credit data, medical records, legal documents | Critical (sector regulation) |

**Q4: Who are the users?**

| User Type | Risk Implication |
|-----------|-----------------|
| **Internal — technical** | Lowest risk; users understand AI limitations |
| **Internal — non-technical** | Low–medium; may overtrust AI outputs |
| **Partners/suppliers** | Medium; less control over use, contractual implications |
| **Customers — authenticated** | High; reputational risk, regulatory exposure |
| **Customers — unauthenticated** | High; abuse potential, no user accountability |
| **General public** | Highest; maximum blast radius, maximum regulatory exposure |

**Q5: What happens when it's wrong?**

| Consequence | Example | Reversibility |
|-------------|---------|---------------|
| **Inconvenience** | Wrong FAQ answer; user asks again | Fully reversible |
| **Wasted effort** | Bad document summary; human redoes it | Time lost, no harm |
| **Financial loss** | Incorrect pricing shown to customer | Recoverable with cost |
| **Reputational damage** | Offensive response to customer | Difficult to reverse |
| **Regulatory breach** | PII disclosed in response | Notification required |
| **Physical harm** | Wrong medical triage priority | Irreversible |

**Q6: Volume** determines operational cost — see [Business Alignment](business-alignment.md) for the FTE calculation. A CRITICAL-tier system at 100 interactions/day is operationally different from one at 100,000 interactions/day, even though the control requirements are identical.

**Q7: Regulatory context** is often the simplest question to answer and the one most often skipped. If the use case operates in financial services, healthcare, legal, HR, or insurance, there are sector-specific requirements that may override the framework's general tiers.

**Q8: Tools and actions** determine whether agentic controls apply. If the AI can call APIs, write to databases, send emails, or trigger workflows, it's agentic — even if nobody calls it that.

**Q9: Business process position** determines upstream and downstream dependencies. Where does the AI sit? What feeds it? What consumes its output? This shapes the [threat model](../extensions/templates/threat-model-template.md) — attack surfaces are at integration points, not inside the model.

**Q10: Accountability** is the governance anchor. Someone — named, not "TBD" — owns the outcomes of this AI system. This person is responsible for HITL decisions, incident response, and regulatory engagement. Without named accountability, the governance model has no force.

---

## Translating Use Cases to Risk Profiles

### The Six-Dimension Scoring Model

The framework's [risk tiers](../core/risk-tiers.md) use six dimensions. Each use case answer maps to a dimension score.

| Dimension | Source Question | LOW | MEDIUM | HIGH | CRITICAL |
|-----------|----------------|-----|--------|------|----------|
| **Decision Authority** | Q2 | Informational | Advisory | Influential | Autonomous |
| **Reversibility** | Q5 | Fully reversible | Recoverable with effort | Difficult to reverse | Irreversible |
| **Data Sensitivity** | Q3 | Public | Internal/confidential | PII, financial | Regulated, sensitive PII |
| **Audience** | Q4 | Internal technical | Internal non-technical | External authenticated | External unauthenticated/public |
| **Scale** | Q6 | <100/day | 100–10,000/day | 10,000–100,000/day | >100,000/day |
| **Regulatory** | Q7 | Unregulated | Light-touch regulation | Sector-regulated | High-risk under AI Act or equivalent |

### Scoring Rules

**Rule 1: Highest dimension wins.**
If a system scores MEDIUM on five dimensions but CRITICAL on one (e.g., it processes regulated health data), the system is CRITICAL. Risk tiers are pessimistic by design.

**Rule 2: Adjacent dimensions compound.**
If a system scores HIGH on three or more dimensions, consider upgrading to CRITICAL even if no individual dimension reaches CRITICAL. Three HIGH scores suggest systemic risk that a single CRITICAL would capture.

**Rule 3: Modifiers can push up, not down.**
Use case modifiers (agentic, customer-facing, regulated) can increase the effective tier but cannot decrease it. A system that's HIGH based on data sensitivity doesn't become MEDIUM because it's internal-only.

### Can AI Score Risk Tiers?

Yes — with caveats. The six-dimension model is structured enough to be machine-evaluable. An LLM can extract the relevant information from a well-written use case definition and propose a risk tier.

**What AI can reliably do:**

| Capability | Reliability | Why |
|------------|-------------|-----|
| Extract data categories from use case description | High | Pattern matching against known categories |
| Identify user types | High | Explicit in most use case definitions |
| Classify decision authority level | Medium–High | Requires understanding of "influence" vs. "inform" |
| Identify regulatory context | Medium–High | Can match domains to known regulatory frameworks |
| Propose a risk tier based on scoring | Medium | Mechanical scoring is straightforward |
| Identify missing information in the definition | High | Can check against the ten-question template |

**What AI cannot reliably do:**

| Capability | Reliability | Why |
|------------|-------------|-----|
| Assess actual reversibility of consequences | Low | Requires domain knowledge and organisational context |
| Determine real decision authority (vs. claimed) | Low | Requires understanding of how humans actually use the system |
| Evaluate data access scope (vs. stated need) | Low | Requires infrastructure knowledge |
| Make the final classification decision | Not appropriate | Accountability must rest with a human |

**Practical approach: AI as classification assistant.**

Use an LLM to:
1. Parse the use case definition against the ten-question template
2. Flag missing or ambiguous answers
3. Score each dimension based on available information
4. Propose a risk tier with reasoning
5. Identify questions the human classifier should investigate

Then a human risk analyst reviews the proposal, investigates the flagged areas, and makes the final classification decision.

This is an example of the framework's own principle in action: **AI proposes. Humans decide.** The classification itself is a [MEDIUM-tier use case](../insights/risk-tier-is-use-case.md) — it influences risk decisions, should be reviewed by a human, and the consequences of misclassification are significant but recoverable.

**Example AI classification prompt:**

```
You are an AI risk classification assistant. Given the following use case
definition, score each dimension and propose a risk tier.

USE CASE:
{use_case_definition}

DIMENSIONS TO SCORE (LOW / MEDIUM / HIGH / CRITICAL):
1. Decision Authority: Does the AI decide, influence, advise, or inform?
2. Reversibility: What happens if the AI is wrong? How easily is it fixed?
3. Data Sensitivity: What data categories does it access?
4. Audience: Who are the users?
5. Scale: How many interactions per day?
6. Regulatory: What regulations apply?

For each dimension:
- State the score with a one-line justification
- Flag if the use case definition is ambiguous or missing information

Then:
- Propose an overall risk tier (highest dimension wins)
- List any questions that need human investigation before finalising
```

The Judge layer should evaluate the quality of AI-generated classifications just as it evaluates any other AI output. Use the same sampling rates — 100% review for the first 50 classifications, then reduce as accuracy data accumulates.

---

## What Security and Governance Can Extract From a Use Case

A well-defined use case produces specific, actionable outputs for every governance function:

### Security Outputs

| Use Case Input | Security Output | How |
|----------------|-----------------|-----|
| Scope boundaries (Q1) | Guardrail topic rules | Negative scope → deny rules; positive scope → allow rules |
| Data categories (Q3) | DLP rules, PII detection config | Data types → specific detection patterns |
| User types (Q4) | Authentication requirements, access controls | Internal → SSO; external → MFA; public → rate limiting |
| Tools/actions (Q8) | Agentic controls, sandbox configuration | API list → scoped permissions; write actions → approval gates |
| Integration points (Q9) | Attack surface map, trust boundaries | Upstream/downstream → threat model |
| Error consequences (Q5) | PACE plan severity | Irreversible → fail-closed; reversible → fail-open acceptable |

### Governance Outputs

| Use Case Input | Governance Output | How |
|----------------|-------------------|-----|
| Risk tier (scored) | Control requirements | Tier → [control matrix](../core/risk-tiers.md) |
| Decision authority (Q2) | HITL requirements | Autonomous → mandatory review; advisory → sampling |
| Volume (Q6) | HITL staffing model | `FTE = Volume × Sample Rate × Review Time / Working Hours` |
| Regulatory context (Q7) | Compliance controls, reporting | Sector → specific regulatory requirements |
| Accountability (Q10) | Governance assignments | Named owner → 1st line responsibility |
| Error consequences (Q5) | Incident severity classification | Consequence type → incident response tier |

### Operational Outputs

| Use Case Input | Operational Output | How |
|----------------|--------------------| -----|
| Volume (Q6) | Infrastructure sizing | Interactions/day → compute, storage, logging capacity |
| Risk tier | Judge sampling rate | LOW: 1–5%; MEDIUM: 5–10%; HIGH: 20–50%; CRITICAL: 100% |
| Risk tier | Log retention period | LOW: 90 days; MEDIUM: 1 year; HIGH: 3 years; CRITICAL: 7 years |
| Risk tier | Review SLA | LOW: best effort; MEDIUM: 24hr; HIGH: 4hr; CRITICAL: 1hr |
| Scope boundaries (Q1) | Monitoring baselines | Expected behaviour → anomaly detection thresholds |

---

## Who Needs to See Use Case Definitions

### The RACI Model

| Role | Responsibility | What They Need From the Use Case |
|------|---------------|----------------------------------|
| **Business Owner** | Accountable for outcomes | Clear scope, success criteria, business value |
| **Risk Analyst** | Classifies risk tier | All ten questions answered; enough detail to score dimensions |
| **Security Architect** | Designs controls | Data flows, integration points, tool access, trust boundaries |
| **AI Engineer** | Builds the system | Technical scope, constraints, data access requirements |
| **Governance (AGO)** | Sets policy, oversees | Risk tier, control requirements, staffing needs |
| **HITL Reviewers** | Review AI outputs | What "good" looks like, what "wrong" looks like, escalation criteria |
| **Legal/Compliance** | Validates regulatory alignment | Regulatory context, data categories, decision types |
| **Internal Audit** | Independent assurance | Use case as documented vs. use case as deployed |

### Format by Audience

Different stakeholders need different views of the same use case:

| Audience | Format | Content | Length |
|----------|--------|---------|--------|
| **Board / governance committee** | Executive summary | Business problem, risk tier, key controls, cost | 1 page |
| **Risk function** | Risk assessment | All ten questions, dimension scores, tier justification | 2–3 pages |
| **Security / engineering** | Technical specification | Data flows, integration points, control configuration | 3–5 pages + diagrams |
| **Operating team** | Operating playbook | What to monitor, when to escalate, PACE procedures | 2–3 pages |
| **Audit** | Control evidence pack | Use case definition + control implementation + testing evidence | Full documentation set |

---

## Who Should Manage Use Cases

### The Use Case Registry

Use cases should be managed in a central registry — an inventory that tracks every AI use case from definition through deployment to retirement.

| Registry Field | Purpose | Populated By |
|----------------|---------|-------------|
| Use case ID | Unique identifier | System-generated |
| Name | Human-readable label | Business owner |
| Status | Draft / Under review / Approved / In production / Retired | Governance |
| Risk tier | Classified tier | Risk analyst (approved by governance) |
| Business owner | Accountable person | Business unit |
| Technical owner | Engineering lead | Engineering |
| Last review date | When it was last assessed | Governance |
| Next review date | When reassessment is due | System-scheduled |
| Change log | What's changed since last review | All contributors |

### Ownership Model

| Aspect | Owner | Responsibility |
|--------|-------|---------------|
| **Use case definition** | Business owner | Defines what the AI does and why; updates when scope changes |
| **Risk classification** | Risk analyst (2nd line) | Scores dimensions, proposes tier, documents justification |
| **Classification approval** | AI governance body | Approves or challenges the proposed tier |
| **Control implementation** | Engineering team | Implements controls specified by the tier |
| **Control verification** | Security / governance | Confirms controls are implemented correctly |
| **Ongoing monitoring** | Technical operations | Operates guardrails, Judge, logging; escalates issues |
| **Periodic review** | Governance | Reassesses use case, confirms tier still appropriate |
| **Change management** | Business owner + governance | When the use case changes, triggers reassessment |

### Review Triggers

Use cases don't stay static. Review is triggered by:

| Trigger | Why | Action |
|---------|-----|--------|
| **Scheduled review** | Annual minimum; quarterly for CRITICAL | Full reassessment against ten questions |
| **Scope change** | Use case does something new | Reassess affected dimensions; may change tier |
| **Data access change** | New data sources added | Reassess data sensitivity dimension |
| **User population change** | Internal → external, or new user groups | Reassess audience dimension |
| **Incident** | AI-related incident on this system | Post-incident review; reassess controls |
| **Regulatory change** | New regulation affects this domain | Reassess regulatory dimension |
| **Model change** | Underlying model swapped or upgraded | Reassess; model change doesn't change tier but may affect control configuration |
| **Volume change** | Significant increase in usage | Reassess operational sizing; may need more HITL staff |

---

## Automating Use Case Discovery and Definition

### When Can We Automate?

Automation of use case definition exists on a spectrum:

| Automation Level | What It Does | Maturity Required | Accuracy |
|------------------|-------------|-------------------|----------|
| **Template-assisted** | Structured form with guidance; human fills in | Low | Depends on human |
| **AI-assisted drafting** | LLM helps complete the template from a brief description | Low–Medium | Medium (needs human review) |
| **AI-assisted classification** | LLM scores dimensions and proposes risk tier | Medium | Medium–High (needs human approval) |
| **Semi-automated discovery** | Scanning tools detect AI usage and auto-populate basic fields | Medium–High | Medium (misses context) |
| **Fully automated classification** | System auto-classifies and auto-assigns controls | High | Not recommended for HIGH/CRITICAL |

### AI-Assisted Drafting

Given a brief description — even a single sentence — an LLM can draft a structured use case definition by asking the right questions.

**Input:** "We want to use AI to help our support team respond to customer emails faster."

**AI-generated draft:**

| Question | AI-Populated Answer | Confidence | Needs Verification |
|----------|---------------------|------------|-------------------|
| What does it do? | Drafts email responses for support team review before sending | High | Confirm: does it send directly or draft only? |
| Decision authority? | Advisory — suggests response; human sends | Medium | Verify: will humans always review? Or auto-send for certain categories? |
| Data accessed? | Customer email content (may contain PII), customer account data, support knowledge base | High | Confirm: what account data specifically? |
| Users? | Internal support team | High | Confirm: any partner/outsourced support? |
| What if wrong? | Incorrect draft; human catches before sending (if reviewing) | Medium | Verify: what if human doesn't catch it? What's sent on company letterhead |
| Volume? | Unknown | Low | Requires: current email volume data |
| Regulatory? | Depends on sector and data types | Low | Requires: sector identification |
| Tools/actions? | Email draft generation; possibly CRM lookup | Medium | Confirm: what CRM access? Read-only or write? |
| Business process? | Sits between incoming customer email and outbound response | High | Confirm: any other systems in the chain? |
| Accountability? | Unknown | Low | Requires: named business owner |

The AI identifies what it can populate (6 of 10 questions), what needs verification (3), and what it can't answer (1). The human effort shifts from writing to reviewing and completing.

### Semi-Automated Discovery

For organisations with mature observability, use case discovery can be partially automated:

| Data Source | What It Reveals | Use Case Field Populated |
|-------------|----------------|--------------------------|
| **API gateway logs** | Which AI APIs are being called, by whom, at what volume | Users (Q4), Volume (Q6), Tools (Q8) |
| **Cloud service inventory** | Which AI services are provisioned | Scope (Q1) — at a high level |
| **IAM policies** | What data the AI service can access | Data access (Q3) |
| **Network flow data** | What the AI connects to upstream and downstream | Business process position (Q9) |
| **Billing data** | Usage patterns, cost allocation | Volume (Q6) |
| **Feature flag platforms** | What AI features exist and their status | Scope (Q1), Status |

**What automated discovery cannot populate:**
- Decision authority (Q2) — requires understanding how humans use the output
- What happens when wrong (Q5) — requires business context
- Regulatory context (Q7) — requires legal/compliance knowledge
- Accountability (Q10) — requires organisational knowledge

**The shadow AI problem:** Semi-automated discovery is most valuable for finding AI use cases that exist but aren't formally defined. The framework's [Visibility Problem](../insights/the-visibility-problem.md) article notes that over 50% of enterprise AI adoption may be shadow AI. Automated discovery tools that scan API calls, cloud service provisioning, and browser traffic can identify undocumented AI usage — which can then be formally defined, classified, and governed.

### Automated Classification — With Guardrails

For organisations processing many use cases (large enterprises may have hundreds), AI-assisted classification can accelerate the pipeline:

```
Use Case Submitted → AI Drafts Definition → AI Scores Dimensions
→ AI Proposes Tier → Human Reviews
```

**Safeguards for automated classification:**

| Safeguard | Purpose |
|-----------|---------|
| **AI proposes, human approves** | Final classification always requires human sign-off |
| **Conservative bias** | When uncertain, AI should propose the higher tier, not the lower |
| **Mandatory escalation** | Any dimension scoring HIGH or CRITICAL triggers mandatory human review |
| **Confidence thresholds** | AI reports confidence per dimension; low confidence triggers investigation |
| **Audit trail** | AI reasoning is logged; human can see why the tier was proposed |
| **Periodic accuracy review** | Compare AI classifications to human decisions; calibrate over time |

**When full automation is appropriate:**
- **Fast Lane qualification** can be fully automated. The four criteria (internal, read-only, no regulated data, human-reviewed) are binary and verifiable from system configuration. An automated check against these criteria is more reliable than human self-certification.
- **Tier downgrades** can be monitored automatically. The criteria (6+ months stable, no incidents, no scope change) are measurable from operational data.

**When automation is not appropriate:**
- Initial classification of HIGH or CRITICAL systems. The consequences of misclassification are too significant.
- Classification where dimension scores are borderline. Human judgement is needed for edge cases.
- Any classification that would result in reduced controls. Reducing controls should always require human approval.

---

## Use Case Anti-Patterns

| Anti-Pattern | What Goes Wrong | How to Fix |
|--------------|----------------|------------|
| **Vague scope** | "AI assistant for the team" — no boundaries, no guardrails, no risk tier | Require explicit positive and negative scope |
| **Missing negative scope** | Use case says what AI does, not what it doesn't | Require "the system cannot/does not..." statements |
| **Understated decision authority** | Claimed "advisory" but functionally autonomous | Ask: "What percentage of AI recommendations are changed by humans?" |
| **Data access vs. data need** | System has broad database access but "only uses" a subset | Assess risk based on access, not stated intent; apply least-privilege |
| **No named owner** | "The team" is accountable | Require a named individual as business owner |
| **Static definition** | Use case defined at launch, never updated | Scheduled reviews + change triggers |
| **Copy-paste from vendor** | Use case definition is the vendor's marketing description | Require internal definition in your context |
| **Scope that grows silently** | Started as FAQ bot, now handles account changes | Change detection via monitoring + periodic review |
| **Volume unknown** | "We're not sure how many interactions" | Require estimation before classification; measure after launch |
| **Regulatory context ignored** | "That's legal's problem" | Require legal/compliance input before classification |

---

## Use Case Definition Template

For practical use. Complete each section before submitting for risk classification.

> **Instructions:** Every field is required unless marked optional. "TBD" is not an acceptable answer for sections 1–10. If you don't know yet, that's your next action — not submitting the form.

### Section 1 — System Identity

| Field | Value |
|-------|-------|
| **System name** | |
| **Business owner** (named individual) | |
| **Technical owner** (named individual) | |
| **Date** | |
| **Version** | |
| **Status** | Draft / Under Review / Approved / In Production / Retired |

### Section 2 — Scope

| | Description |
|---|-------------|
| **What it does** | *Specific, bounded description of system behaviour in this deployment* |
| **What it does NOT do** | *Explicit negative scope — list capabilities the system is prevented from exercising* |

### Section 3 — Decision Authority

| Field | Value |
|-------|-------|
| **Authority level** | Informational / Advisory / Influential / Autonomous |
| **Specific decisions made or influenced** | |
| **Human modification rate** | *What percentage of AI outputs are changed by humans before acting on them?* |

> **Honesty check:** If the human modification rate is below 10%, the system is functionally autonomous regardless of what the process document says. Score accordingly.

### Section 4 — Data Access

| Data Source | Data Categories | Sensitivity (Public / Internal / Confidential / PII / Sensitive PII / Regulated) | Access Type (Read / Write) |
|-------------|----------------|-------------|--------------------------|
| | | | |
| | | | |
| | | | |

> **Assess based on access, not intent.** If the system *can* reach sensitive data through its database connection, that's the risk profile — even if the use case only *needs* a subset.

### Section 5 — Users

| User Type | Description | Estimated Count |
|-----------|-------------|-----------------|
| | | |
| | | |

### Section 6 — Error Consequences

| Question | Answer |
|----------|--------|
| **If the AI produces incorrect output, what happens?** | |
| **What is the worst realistic outcome?** | |
| **How is an error detected?** | |
| **How is an error corrected?** | |
| **Is the error reversible?** | Fully / With effort / With difficulty / Irreversible |

### Section 7 — Volume

| Metric | Value |
|--------|-------|
| **Estimated interactions per day** | |
| **Peak volume** | |
| **Growth trajectory** (6–12 month projection) | |

### Section 8 — Regulatory Context

| Field | Value |
|-------|-------|
| **Sector** | |
| **Applicable regulations** | |
| **Regulatory notification required for AI use?** | Yes / No / Unknown |
| **EU AI Act risk category** (if applicable) | Unacceptable / High-Risk / Limited / Minimal / Not Applicable |

### Section 9 — Tools and Actions

| Tool / API | What It Does | Access Level (Read / Write / Execute) | Human Approval Required? |
|------------|-------------|---------------------------------------|--------------------------|
| | | | |
| | | | |

> If the AI can call APIs, write to databases, send emails, or trigger workflows, it is **agentic** — even if nobody calls it that. [Agentic controls](../core/agentic.md) apply.

### Section 10 — Business Process Position

| Field | Value |
|-------|-------|
| **Upstream inputs** (what feeds the AI) | |
| **Downstream consumers** (what acts on AI output) | |
| **Manual fallback** (if the AI is unavailable) | |
| **Integration dependencies** | |

### Section 11 — Accountability

| Role | Named Individual | Contact |
|------|-----------------|---------|
| **Business owner** (outcomes) | | |
| **Technical owner** (operations) | | |
| **HITL reviewers** (quality) | | |
| **Escalation path** | | |

### Section 12 — Risk Classification

*Completed by risk function. Not self-assessed by the project team.*

| Dimension | Score (LOW / MEDIUM / HIGH / CRITICAL) | Justification |
|-----------|-------|---------------|
| Decision Authority | | |
| Reversibility | | |
| Data Sensitivity | | |
| Audience | | |
| Scale | | |
| Regulatory | | |
| **Overall Tier** | | *Highest dimension wins; three or more HIGH scores may escalate to CRITICAL* |

| Field | Value |
|-------|-------|
| **Classified by** | |
| **Approved by** | |
| **Classification date** | |
| **Next scheduled review** | |

---

## The Framework Gap

The framework defines risk tiers and controls but doesn't provide a structured use case definition methodology. The [control selection guide](../extensions/technical/control-selection-guide.md) has a decision tree, and the [model card template](../extensions/templates/model-card-template.md) captures system metadata, but neither bridges the gap between "we have an idea for AI" and "here's the risk profile and required controls."

This article fills that gap. The ten-question template, the six-dimension scoring model, and the RACI model for use case management connect strategy (what we want to build) to the framework (how we secure it).

**Where the framework should evolve:**
- A formal use case definition template should be added to [extensions/templates/](../extensions/templates/)
- Risk tier classification should reference this structured input, not assume it exists
- AI-assisted classification guidance should be incorporated into the [control selection guide](../extensions/technical/control-selection-guide.md)
- The [model card template](../extensions/templates/model-card-template.md) should be extended to include use case definition fields

---

*AI Runtime Behaviour Security, 2026 (Jonathan Gill).*
