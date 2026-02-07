# AI Threat Modelling Template

A structured approach to identifying threats in AI systems, aligned with risk tiers.

---

## Why Threat Model AI Systems?

AI systems don't exist in isolation. They're part of a data flow supply chain:

- **Upstream:** User inputs, databases, APIs, documents, context systems
- **AI Core:** Model, guardrails, prompts, tools, memory
- **Downstream:** Databases, APIs, workflows, notifications, human processes

**A threat anywhere in this chain can compromise the AI system.**

Traditional threat modelling (STRIDE, PASTA, etc.) applies, but AI introduces unique considerations. This template helps you think through both.

---

## The AI Data Flow

![AI Data Flow Supply Chain](../../images/ai-data-flow-supply-chain.svg)

Every box in this diagram is an attack surface. Every arrow is a data flow that can be manipulated.

---

## Threat Modelling Process

### Step 1: Map Your System

Document every component and connection:

| Category | Questions to Answer |
|----------|---------------------|
| **Users** | Who interacts with the system? What can they input? |
| **Data sources** | What databases, APIs, documents feed the AI? |
| **AI components** | Model, guardrails, Judge, memory, tools? |
| **Outputs** | What does the AI produce? Where does it go? |
| **Actions** | What can the AI do? What systems can it affect? |
| **Humans** | Who reviews outputs? Who can intervene? |

### Step 2: Identify Trust Boundaries

Where does trust change?

| Boundary | Example |
|----------|---------|
| User → System | Untrusted input enters trusted system |
| System → Model | Trusted context meets uncertain model |
| Model → External API | AI interacts with external service |
| System → Database | AI writes to persistent storage |
| System → Human | AI output influences human decision |

**Every trust boundary is a potential attack point.**

### Step 3: Enumerate Threats

For each component and boundary, consider:

| Category | AI-Specific Threats |
|----------|---------------------|
| **Spoofing** | Impersonating users, faking context, spoofed tool responses |
| **Tampering** | Modified prompts, poisoned training data, altered memory |
| **Repudiation** | Untraceable AI actions, deleted logs, unclear accountability |
| **Information Disclosure** | Data leakage, model extraction, prompt disclosure |
| **Denial of Service** | Token exhaustion, infinite loops, resource starvation |
| **Elevation of Privilege** | Scope escape, tool abuse, capability expansion |

### Step 4: Assess Risk

For each threat, assess:

| Factor | Question |
|--------|----------|
| Likelihood | How easily could this be exploited? |
| Impact | What happens if it succeeds? |
| Detectability | Would we know if it happened? |
| Existing controls | What's already mitigating this? |

### Step 5: Document Mitigations

For each significant threat:
- Preventive controls (guardrails, infrastructure)
- Detective controls (Judge, monitoring)
- Responsive controls (human review, playbooks)

---

## Threats by Risk Tier

The same AI capability poses different threats depending on deployment context.

### LOW Tier Example: Internal FAQ Bot

**System:** Answers employee questions about HR policies using company documents.

**Upstream threats:**

| Threat | Likelihood | Impact | Example |
|--------|------------|--------|---------|
| Document poisoning | Low | Low | Attacker modifies source docs |
| Query manipulation | Medium | Low | Employee tries to get inappropriate info |

**AI Core threats:**

| Threat | Likelihood | Impact | Example |
|--------|------------|--------|---------|
| Hallucination | Medium | Low | Bot invents policies |
| Scope creep | Low | Low | Bot answers non-HR questions |

**Downstream threats:**

| Threat | Likelihood | Impact | Example |
|--------|------------|--------|---------|
| Misinformation | Medium | Low | Employee acts on wrong info |
| Log leakage | Low | Low | Queries visible inappropriately |

**Appropriate response:** Basic guardrails, periodic review, clear disclaimers.

---

### MEDIUM Tier Example: Customer Support Agent

**System:** Answers customer questions, accesses order history, can initiate refunds up to $50.

**Upstream threats:**

| Threat | Likelihood | Impact | Example |
|--------|------------|--------|---------|
| Customer impersonation | Medium | Medium | Attacker queries other accounts |
| Injection via ticket history | Medium | Medium | Malicious content in past tickets |
| Database compromise | Low | High | Poisoned order data |

**AI Core threats:**

| Threat | Likelihood | Impact | Example |
|--------|------------|--------|---------|
| Prompt injection | High | Medium | Customer injects instructions |
| Jailbreak | Medium | Medium | Customer bypasses policies |
| Scope violation | Medium | Medium | AI accesses unauthorized data |

**Downstream threats:**

| Threat | Likelihood | Impact | Example |
|--------|------------|--------|---------|
| Unauthorized refund | Medium | Medium | AI issues refund to wrong person |
| PII disclosure | Medium | High | AI reveals other customer data |
| Workflow abuse | Low | Medium | AI triggers unintended processes |

**Appropriate response:** Strong input validation, action limits, comprehensive logging, regular Judge review, escalation paths.

---

### HIGH Tier Example: Credit Decision Support

**System:** Analyzes loan applications, provides recommendations to human underwriters, explains reasoning.

**Upstream threats:**

| Threat | Likelihood | Impact | Example |
|--------|------------|--------|---------|
| Application fraud | High | High | Manipulated application data |
| Data feed compromise | Low | Critical | Poisoned credit data |
| Adversarial inputs | Medium | High | Inputs designed to game model |

**AI Core threats:**

| Threat | Likelihood | Impact | Example |
|--------|------------|--------|---------|
| Biased reasoning | Medium | Critical | Discriminatory recommendations |
| Explanation manipulation | Medium | High | Misleading reasoning for humans |
| Model extraction | Low | High | Competitor learns decision logic |
| Prompt disclosure | Medium | Medium | Applicant learns system prompts |

**Downstream threats:**

| Threat | Likelihood | Impact | Example |
|--------|------------|--------|---------|
| Human over-reliance | High | Critical | Underwriter rubber-stamps AI |
| Regulatory violation | Medium | Critical | Unexplainable decisions |
| Audit failure | Medium | High | Insufficient documentation |
| Disparate impact | Medium | Critical | Protected class discrimination |

**Appropriate response:** Comprehensive controls at every layer, mandatory human review, full audit trail, bias testing, regulatory monitoring.

---

### CRITICAL Tier Example: Medical Triage Assistant

**System:** Reviews patient symptoms, suggests triage priority, accessed by emergency staff.

**Upstream threats:**

| Threat | Likelihood | Impact | Example |
|--------|------------|--------|---------|
| Patient data manipulation | Low | Critical | Altered medical history |
| EHR compromise | Low | Critical | Poisoned medical records |
| Input manipulation | Medium | Critical | Patient/attacker games symptoms |

**AI Core threats:**

| Threat | Likelihood | Impact | Example |
|--------|------------|--------|---------|
| Misclassification | Medium | Critical | Wrong triage priority |
| Hallucination | Medium | Critical | Invented contraindications |
| Overconfidence | High | Critical | AI doesn't express uncertainty |
| Adversarial attack | Low | Critical | Inputs designed to cause harm |

**Downstream threats:**

| Threat | Likelihood | Impact | Example |
|--------|------------|--------|---------|
| Treatment delay | Medium | Critical | Under-triaged patient deteriorates |
| Resource misallocation | Medium | High | Over-triaged patients consume resources |
| Clinician de-skilling | High | High | Staff lose independent judgment |
| Malpractice liability | Medium | Critical | AI contributes to adverse outcome |

**Appropriate response:** Maximum controls, mandatory clinician verification, real-time monitoring, immediate escalation capability, extensive testing, regulatory compliance, continuous validation.

---

## Including Upstream and Downstream Systems

**Critical principle:** Threats don't stop at your system boundary.

### Upstream Analysis

For each data source:

| Question | Why It Matters |
|----------|---------------|
| Who controls this source? | Insider threat potential |
| How is it secured? | Compromise cascades to AI |
| Can it be manipulated? | Poisoned inputs affect outputs |
| Is it validated before use? | Garbage in, garbage out |
| How fresh is the data? | Stale data = wrong decisions |

### Downstream Analysis

For each output destination:

| Question | Why It Matters |
|----------|---------------|
| What systems receive AI output? | Determines blast radius |
| Can AI output break downstream? | Integration failures |
| Is output validated before use? | Catch AI errors |
| Can actions be reversed? | Determines risk tolerance |
| Who's accountable for actions? | Defines oversight needs |

### Human Process Analysis

| Question | Why It Matters |
|----------|---------------|
| How do humans interact with outputs? | Determines reliance risk |
| Can humans override AI? | Essential for accountability |
| Are humans incentivized to verify? | Or rubber-stamp? |
| What training do humans have? | Determines effective oversight |
| How are human decisions logged? | Audit trail completeness |

---

## AI-Specific Threat Modelling Techniques

### STRIDE-AI (Extended STRIDE)

Add AI-specific considerations to each category:

| Category | Traditional | AI Extension |
|----------|-------------|--------------|
| **S**poofing | Identity faking | Context spoofing, tool response faking |
| **T**ampering | Data modification | Prompt injection, memory manipulation |
| **R**epudiation | Denying actions | Unclear AI vs human decisions |
| **I**nfo Disclosure | Data leakage | Training data extraction, prompt leakage |
| **D**enial of Service | Availability | Token exhaustion, infinite loops |
| **E**levation | Privilege gain | Scope escape, capability expansion |

### Attack Trees for AI

Build hierarchical attack paths showing how threats branch into specific techniques, and map controls to each:

![Attack Tree Example](../../images/attack-tree-pii.svg)

Attack trees help you:
- Identify all paths to a threat goal
- Map existing controls to attack paths
- Find gaps where controls are missing
- Prioritise mitigations by coverage

### MITRE ATLAS

Use the ATLAS framework for adversarial ML threats:
- Reconnaissance (learning about the system)
- Resource Development (building attacks)
- Initial Access (entering the system)
- Execution (running attacks)
- Persistence (maintaining access)
- Privilege Escalation (expanding capability)
- Defense Evasion (avoiding detection)
- Discovery (learning more)
- Collection (gathering data)
- Exfiltration (extracting data)
- Impact (causing harm)

→ See atlas.mitre.org for detailed techniques

---

## Threat Model Documentation Template

```markdown
# AI System Threat Model

## System Overview
- **Name:** 
- **Risk Tier:**
- **Purpose:**
- **Users:**
- **Data Sources:**
- **Outputs/Actions:**

## Architecture Diagram
[Include data flow diagram]

## Trust Boundaries
| Boundary | Description | Key Risks |
|----------|-------------|-----------|
| | | |

## Upstream Threats
| Source | Threat | Likelihood | Impact | Controls |
|--------|--------|------------|--------|----------|
| | | | | |

## AI Core Threats
| Component | Threat | Likelihood | Impact | Controls |
|-----------|--------|------------|--------|----------|
| | | | | |

## Downstream Threats
| Destination | Threat | Likelihood | Impact | Controls |
|-------------|--------|------------|--------|----------|
| | | | | |

## Human Process Threats
| Process | Threat | Likelihood | Impact | Controls |
|---------|--------|------------|--------|----------|
| | | | | |

## Residual Risks
| Risk | Acceptance Rationale |
|------|---------------------|
| | |

## Review Schedule
- **Last Review:**
- **Next Review:**
- **Trigger for Ad-hoc Review:**
```

---

## Key Takeaways

1. **AI doesn't exist in isolation** — threat model the full supply chain
2. **Risk tier determines depth** — LOW needs basic analysis, CRITICAL needs comprehensive
3. **Include upstream systems** — compromised inputs compromise AI
4. **Include downstream systems** — AI failures cascade
5. **Include humans** — over-reliance is a threat
6. **Use established frameworks** — STRIDE-AI, ATLAS, attack trees
7. **Document and review** — threat models age; update them

---

## Adapting This Template

This template is a starting point. Your threat model needs to reflect:

- Your specific architecture
- Your regulatory environment
- Your risk appetite
- Your operational context
- Your existing controls

**The framework provides principles. You provide the specifics.**

---

*AI Security Blueprint — Threat Modelling Template*
