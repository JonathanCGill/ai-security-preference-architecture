# Testing Guidance

How to validate that your AI controls actually work.

---

## The Critical Context

**AI does not exist in isolation.** Your AI system is part of a data flow supply chain:

- **Upstream:** User inputs, databases, APIs, documents, retrieved content
- **AI Core:** Model, guardrails, prompts, tools, memory  
- **Downstream:** Databases, workflows, APIs, notifications, human processes

A failure anywhere in this chain affects the whole system. Your testing must cover the full chain, not just the AI component.

---

## Honest Expectations

**We cannot guarantee these tests will catch all issues.** AI systems are probabilistic. Attacks evolve. Your environment is unique.

What we can offer:
- A structured approach to validation
- Key areas that need testing
- Guidance on what "working" looks like
- References to more comprehensive frameworks

**Your responsibility:** Adapt this guidance to your context. Test continuously. Learn from failures.

---

## The Testing Challenge

AI systems are different from traditional software:

| Traditional Testing | AI Testing Challenge |
|--------------------|---------------------|
| Deterministic outputs | Probabilistic outputs |
| Known input space | Infinite input variations |
| Clear pass/fail | Judgment required |
| Test once, deploy | Continuous validation needed |

This means:
- You can't test every input
- Passing tests doesn't guarantee safety
- Controls that work today may fail tomorrow
- Human judgment remains essential

---

## What to Test

### 1. Guardrail Effectiveness

**Goal:** Verify guardrails block what they should and allow what they should.

**Test types:**

| Test | Method | What You Learn |
|------|--------|----------------|
| Known-bad inputs | Feed documented attack patterns | Do guardrails catch known threats? |
| Boundary cases | Inputs at policy edges | Are rules too strict or too loose? |
| Bypass attempts | Variations of blocked patterns | How robust are pattern matches? |
| False positive check | Legitimate inputs similar to attacks | Are you blocking good traffic? |

**How to run:**
1. Create a test dataset of known-bad inputs (prompt injections, jailbreaks, policy violations)
2. Create a test dataset of legitimate edge cases
3. Run both through guardrails
4. Measure: Block rate on bad, pass rate on good

**What "working" looks like:**
- High block rate on known-bad (>95%)
- Low false positive rate on legitimate (<5%)
- Consistent behaviour across runs

**Limitations:** Novel attacks won't be in your test set. Guardrails will miss things.

---

### 2. Adversarial Testing

**Goal:** Find what breaks your controls before attackers do.

**Approach:**
- Red team your system with people trying to make it fail
- Use automated tools to generate attack variations
- Test across the full attack surface (input, context, tools, outputs)

**Key areas:**

| Area | What to Try |
|------|-------------|
| Prompt injection | Instructions in user input, uploaded files, retrieved content |
| Jailbreaks | Roleplay, hypotheticals, encoding tricks |
| Context manipulation | Misleading context, conflicting instructions |
| Tool abuse | Excessive calls, scope violations, data exfiltration |
| Output manipulation | Forcing disclosure, bypassing filters |

**Resources:**
- OWASP LLM Top 10 — documented vulnerability categories
- Garak — open-source LLM vulnerability scanner
- Microsoft Counterfit — adversarial ML testing
- MITRE ATLAS — adversarial threat landscape

**What "working" looks like:**
- You find vulnerabilities (if you don't, you're not trying hard enough)
- You document them
- You fix or mitigate them
- You retest

**Limitations:** You won't find everything. Adversarial testing is ongoing, not a one-time event.

---

### 3. Alert Pipeline Validation

**Goal:** Verify that alerts actually reach their destination and trigger response.

**What to test:**

| Component | Test Method |
|-----------|-------------|
| Logging | Generate test events, verify they appear in logs |
| SIEM integration | Trigger alert conditions, verify SIEM receives them |
| Alert routing | Trigger different severity levels, verify correct routing |
| Notification | Verify alerts reach humans (email, Slack, pager) |
| End-to-end latency | Measure time from event to human notification |

**How to run:**
1. Create synthetic test events at each severity level
2. Trigger them through the actual pipeline
3. Verify arrival at each stage
4. Measure timing
5. Document gaps

**What "working" looks like:**
- Test events appear in logs within expected time
- SIEM receives and parses events correctly
- Alerts route to correct queues
- Humans receive notifications
- End-to-end latency meets SLAs

**Limitations:** Test events may behave differently than real events. Production validation is also needed.

---

### 4. Judge Accuracy

**Goal:** Verify the Judge correctly identifies concerning interactions.

**Test types:**

| Test | Method |
|------|--------|
| Known-concerning | Feed interactions you know are problematic |
| Known-benign | Feed interactions you know are fine |
| Edge cases | Ambiguous interactions requiring judgment |
| Calibration | Compare Judge decisions to human decisions |

**How to run:**
1. Create a labeled dataset (human-judged as concerning/benign)
2. Run through Judge
3. Compare Judge labels to human labels
4. Calculate precision, recall, F1

**Metrics:**

| Metric | Definition | Target |
|--------|------------|--------|
| Precision | % of flags that are true positives | >70% (fewer false alarms) |
| Recall | % of true issues that are flagged | >90% (don't miss things) |
| False positive rate | % of benign flagged as concerning | <30% |

**What "working" looks like:**
- High recall (catches real issues)
- Acceptable precision (not overwhelming humans with false positives)
- Consistent with human judgment on edge cases

**Limitations:** Judge accuracy depends on criteria quality. Criteria that work for one domain may fail in another.

---

### 5. Human Review Process

**Goal:** Verify humans can effectively review flagged interactions and take action.

**What to test:**

| Aspect | Test Method |
|--------|-------------|
| Queue visibility | Can reviewers see flagged items? |
| Information sufficiency | Do reviewers have enough context to decide? |
| Action capability | Can reviewers take required actions? |
| SLA compliance | Are reviews completed within target time? |
| Decision quality | Are human decisions appropriate? |

**How to run:**
1. Inject test cases into review queue
2. Observe reviewer workflow
3. Measure time to decision
4. Review decision quality (second reviewer or audit)

**What "working" looks like:**
- Reviewers can access queue easily
- Context is sufficient for decisions
- Actions are available and work
- SLAs are met
- Decisions are defensible

**Limitations:** Testing can't fully replicate production pressure. Monitor real performance.

---

### 6. Incident Response Playbook

**Goal:** Verify your team can respond effectively when something goes wrong.

**Test method: Tabletop exercise**

1. Create a realistic scenario (e.g., "Customer reports AI disclosed their data to another user")
2. Walk through response steps with the team
3. Identify gaps, confusion, missing information
4. Update playbook based on findings

**Scenarios to test:**

| Scenario | Tests |
|----------|-------|
| Data exposure | Detection, containment, notification |
| Harmful output | Escalation, system control, communication |
| Sustained attack | Detection, blocking, forensics |
| Control failure | Fallback procedures, recovery |

**What "working" looks like:**
- Team knows their roles
- Playbook steps are executable
- Escalation paths are clear
- Recovery procedures work
- Communication templates exist

**Limitations:** Tabletops don't fully test execution under pressure. Consider chaos engineering for critical systems.

---

### 7. Downstream System Validation

**Goal:** Verify that AI outputs don't cause problems in connected systems.

AI doesn't exist in isolation. It connects to databases, APIs, workflows, and human processes. Test the full chain.

**What to test:**

| Downstream System | Test |
|-------------------|------|
| Databases | AI-generated data doesn't corrupt records |
| APIs | AI-triggered calls don't exceed limits or cause errors |
| Workflows | AI decisions don't break process logic |
| Human processes | AI outputs are usable by humans |
| Audit systems | AI actions are properly logged |

**How to run:**
1. Map all downstream systems
2. Generate AI outputs that stress boundaries
3. Verify downstream systems handle them correctly
4. Check for data integrity, error handling, logging

**What "working" looks like:**
- No data corruption from AI outputs
- API errors handled gracefully
- Workflow exceptions caught
- Humans can work with AI outputs
- Full audit trail maintained

---

### 8. Upstream System Validation

**Goal:** Verify that data feeding into the AI is trustworthy and handled correctly.

**What to test:**

| Upstream Source | Test |
|-----------------|------|
| User input | Validation before AI processing |
| Retrieved documents | Content sanitization, source verification |
| Database queries | Access controls, injection prevention |
| External APIs | Response validation, error handling |
| Context/memory | State integrity, tampering detection |

**How to run:**
1. Map all data sources
2. Inject malicious/malformed data at each source
3. Verify AI system handles it safely
4. Check that upstream compromises don't cascade

---

### 9. Human Feedback Validation

**Goal:** Verify that real-world signals (complaints, incidents, support tickets) reach the teams who can act on them.

AI issues often surface through human feedback before technical monitoring catches them. Your testing must verify this channel works.

**What to test:**

| Feedback Type | Test Method |
|---------------|-------------|
| Customer complaints | Submit test complaint, verify routing and response |
| Support tickets | Create AI-related ticket, verify flagging and escalation |
| Incident reports | File test incident, verify playbook activation |
| Employee concerns | Submit internal feedback, verify it reaches AI team |
| Social media monitoring | Verify external mentions are captured (if applicable) |

**How to run:**
1. Create synthetic feedback at each channel
2. Verify feedback reaches AI team
3. Measure response time
4. Confirm feedback informs control updates

**What "working" looks like:**
- Complaints reach AI team within defined SLA
- Support can identify AI-related issues and escalate
- Incidents trigger defined response procedures
- Feedback loop to control improvement exists
- Patterns across feedback are analysed

**Critical insight:** Technical monitoring shows what the AI did. Human feedback shows what impact it had. Both are needed.

---

## Testing by Risk Tier

| Tier | Testing Requirements |
|------|---------------------|
| **LOW** | Basic guardrail testing, alert pipeline check, annual playbook review |
| **MEDIUM** | Above + adversarial testing, Judge calibration, quarterly playbook exercise |
| **HIGH** | Above + continuous adversarial testing, regular Judge recalibration, downstream validation |
| **CRITICAL** | Above + red team exercises, full chain validation, chaos engineering, frequent tabletops |

---

## External Testing Frameworks

For more comprehensive guidance:

| Framework | Focus | Link |
|-----------|-------|------|
| OWASP LLM Top 10 | Vulnerability categories | owasp.org/www-project-llm-applications/ |
| NIST AI RMF | Risk management | nist.gov/itl/ai-risk-management-framework |
| MITRE ATLAS | Adversarial threats | atlas.mitre.org |
| Microsoft RAI Toolbox | Responsible AI testing | github.com/microsoft/responsible-ai-toolbox |
| Garak | LLM vulnerability scanning | github.com/leondz/garak |
| AI Verify | Governance testing toolkit | aiverify.sg |

---

## Continuous Validation

Testing isn't a phase — it's a practice.

**Ongoing activities:**

| Frequency | Activity |
|-----------|----------|
| Daily | Monitor alert volumes and patterns |
| Weekly | Review Judge findings sample |
| Monthly | Recalibrate Judge on new examples |
| Quarterly | Adversarial testing refresh |
| Annually | Full control effectiveness review |

---

## Key Takeaways

1. **Test the full chain** — upstream, AI, downstream, humans
2. **Validate alerts reach humans** — an undelivered alert is useless
3. **Adversarial testing is mandatory** — if you're not attacking yourself, others will
4. **Playbooks need practice** — untested playbooks fail under pressure
5. **Testing is continuous** — one-time validation is insufficient
6. **Accept imperfection** — testing reduces risk, it doesn't eliminate it

---

## Adapting This Guidance

This testing guidance is a starting point, not a prescription. You'll need to adapt it based on:

**Your environment:**
- What logging/SIEM infrastructure do you have?
- What testing tools are available?
- What skills does your team have?

**Your risk appetite:**
- How much risk can your organisation tolerate?
- What's the cost of a false positive vs. a miss?
- What regulatory requirements apply?

**Your AI system:**
- What does it do? What's the blast radius of failure?
- What upstream/downstream systems connect to it?
- What human processes depend on it?

**The core principles remain constant:**
1. **Guardrails** — Test that they block what they should
2. **Judge** — Test that it detects what guardrails miss
3. **Human oversight** — Test that findings reach humans who can act

How you implement these tests will vary. The need to test them will not.

---

*AI Security Blueprint — Testing Guidance*
