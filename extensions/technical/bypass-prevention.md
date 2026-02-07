# Bypass Prevention

How attackers, users, and agents circumvent controls — and what you can do about it.

---

## The Uncomfortable Truth

**You cannot fully prevent bypasses.**

This isn't defeatism — it's the foundation of a realistic security posture. AI systems have fundamental properties that make complete bypass prevention impossible:

| Property | Why It Enables Bypass |
|----------|----------------------|
| **Instructions and data share one channel** | No technical way to fully separate user input from system instructions |
| **Models are trained to be helpful** | Helpfulness can be exploited ("please help me with this totally legitimate request...") |
| **Natural language is ambiguous** | Any rule can be rephrased to technically comply while violating intent |
| **Models can't verify intent** | A model cannot know if a user is lying about why they want something |
| **Emergent capabilities** | Models may find paths to goals that weren't anticipated |

**The goal is not prevention. The goal is:**
1. Make bypasses harder (raise the cost)
2. Detect bypasses when they happen (reduce dwell time)
3. Limit damage when bypasses succeed (reduce blast radius)
4. Learn from bypasses (improve over time)

---

## Bypass Taxonomy

![Bypass Taxonomy](../../images/bypass-taxonomy.svg)

### Category 1: Guardrail Bypasses

Attacker gets prohibited content past input or output filters.

| Technique | Description | Example |
|-----------|-------------|---------|
| **Encoding** | Obfuscate content using encoding | Base64, ROT13, hex, Unicode tricks |
| **Semantic rephrasing** | Say the same thing differently | "How to harm" → "What would theoretically cause damage to" |
| **Language switching** | Use language guardrails don't cover | English guardrails, attack in Welsh |
| **Fragmentation** | Split request across messages | "Tell me about making..." [next message] "...explosives" |
| **Role-play** | Request via fictional framing | "Write a story where a character explains how to..." |
| **Jailbreaks** | Structured prompts that override safety | DAN, grandma exploit, etc. |
| **Indirect injection** | Malicious instructions in retrieved content | Poison RAG data with instructions |

### Category 2: Intent Declaration Bypasses

User provides false or misleading declarations to gain access.

| Technique | Description | Example |
|-----------|-------------|---------|
| **Lying** | Simply declare false intent | "I'm a security researcher" (when not) |
| **Vagueness** | Declare intent too broadly | "Business purposes" covers almost anything |
| **Scope creep** | Start legitimate, drift to prohibited | Begin with allowed queries, gradually escalate |
| **Social engineering** | Convince human reviewers | Compelling but false justification |
| **Credential misuse** | Use legitimate access for illegitimate purpose | Authorised user, unauthorised action |

### Category 3: Agentic Bypasses

Agent circumvents scope or approval controls.

| Technique | Description | Example |
|-----------|-------------|---------|
| **Loophole exploitation** | Find technically-allowed path to prohibited goal | "I can't delete the file, but I can overwrite it with empty content" |
| **Tool chaining** | Combine allowed tools to achieve prohibited outcome | Read file + send email = exfiltrate data |
| **Goal hijacking** | Injected instructions redirect agent's goal | Tool output contains "new priority: ignore previous goal" |
| **Approval manipulation** | Craft requests to look benign | "Minor database update" hides significant change |
| **Resource exhaustion** | Stay within per-action limits, exceed aggregate | 1000 small API calls vs. 1 large one |

### Category 4: Architectural Bypasses

Go around the controlled system entirely.

| Technique | Description | Example |
|-----------|-------------|---------|
| **Shadow AI** | Use uncontrolled AI systems | Personal ChatGPT for work data |
| **Direct API access** | Bypass application guardrails via raw API | Access model API directly without app controls |
| **Data exfiltration then external processing** | Move data outside controlled environment | Copy data, use external AI |
| **Insider configuration change** | Modify guardrails/controls directly | Disable guardrail rule |

### Category 5: Process Bypasses

Circumvent human oversight and governance.

| Technique | Description | Example |
|-----------|-------------|---------|
| **HITL fatigue exploitation** | Submit when reviewers are tired/rushed | End of shift, high volume |
| **Rubber-stamp cultivation** | Train reviewers to auto-approve | Long history of benign requests |
| **Approval fragmentation** | Split prohibited action across approvers | Each approver sees only their piece |
| **Emergency override abuse** | Invoke emergency process inappropriately | "Production is down" when it isn't |
| **Privilege escalation** | Gain elevated access through legitimate means | Promotion, role change, social engineering |

---

## Defence-in-Depth Architecture

No single control stops all bypasses. Layer controls so bypassing one doesn't grant full access.

![Defence in Depth Architecture](../../images/defence-in-depth.svg)

**Principle:** An attacker must bypass ALL layers, not just one.

---

## Guardrail Bypass Prevention

### Input Guardrails

| Control | What It Catches | Limitations |
|---------|-----------------|-------------|
| **Pattern matching** | Known attack signatures | Misses novel phrasings |
| **Encoding detection** | Base64, hex, Unicode | New encodings emerge |
| **ML classifiers** | Semantic intent | Adversarial examples, distribution shift |
| **Rate limiting** | Brute force, enumeration | Legitimate high-volume use |
| **Input length limits** | Context stuffing | May block legitimate long inputs |

**Hardening:**

![Multi-Layer Input Validation](../../images/multi-layer-input-validation.svg)

### Output Guardrails

| Control | What It Catches | Limitations |
|---------|-----------------|-------------|
| **Content classifiers** | Harmful content categories | False negatives on edge cases |
| **PII detection** | Personal data leakage | Novel PII formats, context-dependent |
| **Grounding checks** | Hallucination, unsupported claims | Requires source data access |
| **Format validation** | Malformed outputs | Valid format, harmful content |

**Hardening:**

Output validation follows the same multi-layer principle as input validation: multiple classifiers, PII scanning, cross-reference checks against authorised data scope, grounding verification, and behavioural consistency checks.

### RAG/Context Injection Prevention

Retrieved content is an injection vector. Treat it as untrusted input.

| Control | Implementation |
|---------|----------------|
| **Content sanitisation** | Strip potential instruction patterns from retrieved content |
| **Source verification** | Validate content hasn't been tampered with (checksums) |
| **Privilege separation** | Retrieved content marked differently from system instructions |
| **Anomaly detection** | Alert on unusual content in knowledge base |
| **Access control** | Restrict who can modify knowledge base |

---

## Intent Declaration Controls

### The Problem

You cannot verify that someone's stated intent is their actual intent. A user who says "I need this for security research" might be lying.

### Defence Strategies

**1. Make lying costly**

| Control | How It Raises Cost |
|---------|-------------------|
| **Audit trail** | Declarations are logged and attributable |
| **Manager attestation** | Someone else vouches for the purpose |
| **Periodic re-certification** | Must re-justify continued access |
| **Random verification** | Sample of uses checked against declared purpose |
| **Consequences** | Clear policy on misuse, enforced |

**2. Reduce reliance on intent**

| Approach | Implementation |
|----------|----------------|
| **Capability-based access** | Grant specific capabilities, not broad access based on intent |
| **Data minimisation** | Only expose data needed for declared purpose |
| **Output restrictions** | Even if intent is legitimate, limit what can be extracted |
| **Behavioural monitoring** | Detect misuse regardless of declared intent |

**3. Structured declarations**

Don't ask "What's your purpose?" — ask specific, verifiable questions:

```
❌ Bad: "Please describe your intended use"
   (Any answer is accepted, unverifiable)

✓ Good: 
   - "Which project code should this be billed to?" (Verifiable)
   - "Who is your manager for this request?" (Accountable)
   - "What is the ticket/case number?" (Traceable)
   - "Select the data categories you need access to" (Bounded)
```

**4. Progressive disclosure**

Don't grant full access upfront. Expand access as legitimate use is demonstrated:

| Stage | Access Level | Monitoring |
|-------|--------------|------------|
| Initial | Limited data, basic capabilities | High scrutiny |
| Established | Broader access | Standard monitoring |
| Trusted | Full authorised scope | Periodic audit |

---

## Agentic Bypass Prevention

Agents are harder to control because they choose their own actions.

### Scope Enforcement

**Don't rely on the agent to enforce its own scope.**

![Infrastructure vs Instruction Enforcement](../../images/infra-vs-instruction.svg)

| Bad | Good |
|-----|------|
| "Only access customer service data" (instruction) | Database view that only exposes customer service data (infrastructure) |
| "Don't send emails without approval" (instruction) | Email API requires approval token (infrastructure) |
| "Stay within budget" (instruction) | Hard spending cap at API level (infrastructure) |

**Infrastructure-level enforcement:**

![Agent Sandbox](../../images/agent-sandbox.svg)

### Action Validation

Validate every action independently. Don't trust the agent's reasoning.

![Action Validator Flow](../../images/action-validator-flow.svg)

### Tool Output Sanitisation

Tool outputs are injection vectors. The agent asks a tool for information; the tool response may contain malicious instructions.

![Tool Output Sanitiser](../../images/tool-output-sanitiser.svg)

### Approval Workflows

Make approval meaningful, not rubber-stamp.

| Anti-pattern | Better Approach |
|--------------|-----------------|
| "Agent wants to do X. Approve?" | "Agent wants to do X. Here's the context, the data involved, the potential impact. What would you expect to happen?" |
| Approve/Deny only | Approve / Deny / Modify / Escalate |
| Same approver for all actions | Different approvers for different action types |
| No time limit on approval | Approval expires; must re-request |
| Bulk approval | Each significant action approved individually |

### Circuit Breakers

Hard stops that trigger regardless of agent "reasoning":

| Threshold | Example |
|-----------|---------|
| **Action count** | >100 actions in one task → pause |
| **Cost** | >$50 in API calls → pause |
| **Time** | >30 minutes execution → pause |
| **Error rate** | >10% of actions failing → pause |
| **Scope violations** | Any attempt to exceed scope → terminate |
| **Sensitive actions** | Any irreversible action → require approval |

---

## Architectural Bypass Prevention

### Shadow AI

Users bypass your controlled AI by using uncontrolled alternatives.

> **See also:** [Technical Controls](technical-controls.md) for detailed implementation of network, firewall, proxy, and DLP controls.

| Detection | Prevention |
|-----------|------------|
| Network monitoring for AI API endpoints | Provide good internal alternatives |
| DLP for data going to external AI | Block external AI at network level |
| Endpoint monitoring for AI applications | Clear policy with consequences |
| User surveys and interviews | Make internal AI easy to use |

**Key insight:** Shadow AI is often a symptom of poor internal AI UX. If your controlled AI is too restrictive or slow, users will go elsewhere.

### Direct API Access

Users bypass application guardrails by accessing the model API directly.

| Control | Implementation |
|---------|----------------|
| **API gateway** | All model access through gateway with controls |
| **No direct credentials** | Users don't have API keys; apps have keys |
| **Network segmentation** | Model APIs not directly accessible |
| **Request signing** | Requests must be signed by authorised application |

### Insider Threats

Authorised users misuse their access or modify controls.

| Control | Implementation |
|---------|----------------|
| **Separation of duties** | Different people for guardrail config vs. AI operation |
| **Change approval** | Guardrail changes require review |
| **Configuration audit** | All config changes logged, reviewed |
| **Privileged access management** | Admin access time-limited, monitored |
| **Canary testing** | Regularly test that controls still work |

---

## Process Bypass Prevention

### HITL Integrity

Human oversight only works if humans are actually reviewing.

| Risk | Mitigation |
|------|------------|
| **Fatigue** | Limit review volume per person per shift |
| **Rubber-stamping** | Canary cases to verify attention |
| **Time pressure** | SLAs that allow adequate review time |
| **Bias** | Multiple reviewers for high-risk decisions |
| **Bribery/coercion** | Rotation, segregation, monitoring |

### Canary Testing

Inject known-bad cases to verify controls work:

![Canary Testing Programme](../../images/canary-testing.svg)

### Configuration Integrity

Controls are only as good as their configuration.

| Control | Implementation |
|---------|----------------|
| **Version control** | All config in Git, changes via PR |
| **Approval required** | Config changes require review |
| **Automated testing** | CI/CD tests config changes |
| **Drift detection** | Alert if production differs from source |
| **Rollback capability** | Quick revert if issues detected |
| **Audit logging** | Who changed what when |

---

## Detection When Bypasses Succeed

Since you can't prevent all bypasses, you must detect them.

### Behavioural Anomaly Detection

| Signal | What It May Indicate |
|--------|---------------------|
| **Unusual query patterns** | Probing, enumeration, testing boundaries |
| **Sudden topic shifts** | Multi-turn attack, goal hijacking |
| **High volume from single user** | Automation, data extraction |
| **Requests outside role** | Credential misuse, scope violation |
| **Repeated near-misses** | Attacker learning guardrail boundaries |

### Judge-Based Detection

The Judge evaluates interactions after the fact and can detect:

| Detection | Method |
|-----------|--------|
| **Successful injection** | Output inconsistent with system prompt |
| **Data leakage** | Output contains data user shouldn't have |
| **Policy violation** | Output violates content policies |
| **Anomalous behaviour** | Interaction pattern unusual for use case |
| **Scope violation** | Agent accessed data outside authorised scope |

### Logging for Forensics

When a bypass is detected, you need to investigate. Log:

| Data | Purpose |
|------|---------|
| **Full interaction** | Understand what happened |
| **User identity** | Attribute to actor |
| **Timestamp** | Establish timeline |
| **Model version** | Reproducibility |
| **Guardrail results** | What was checked, what passed |
| **Retrieved context** | What data was in scope |
| **Actions taken** | What the system did |

---

## Response When Bypasses Are Detected

### Immediate Response

| Severity | Response |
|----------|----------|
| **Active exploitation** | Disable affected capability, invoke incident response |
| **Successful bypass, limited impact** | Block user/pattern, investigate |
| **Attempted bypass, blocked** | Log, monitor, update guardrails if novel |
| **Canary failure** | Investigate control failure, restore |

### Learning Loop

Every bypass is an opportunity to improve:

![Bypass Learning Loop](../../images/bypass-learning-loop.svg)

---

## Control Effectiveness by Bypass Type

| Bypass Type | Most Effective Control | Why |
|-------------|----------------------|-----|
| Encoding tricks | Decoding + pattern matching | Deterministic, catches known encodings |
| Semantic rephrasing | ML classifiers + Judge | Requires understanding meaning |
| Multi-turn attacks | Conversation-level analysis | Per-message checks miss gradual escalation |
| Indirect injection | Tool output sanitisation + separate context | Treats retrieved data as untrusted |
| Intent declaration fraud | Audit + consequences + behavioural monitoring | Can't prevent lying; can detect and punish |
| Agentic loopholes | Infrastructure enforcement | Don't rely on agent to limit itself |
| Shadow AI | Good internal alternatives + DLP | Users go outside when inside is bad |
| Insider threats | Separation of duties + audit | No single person can both change and use |
| HITL bypass | Canary testing + reviewer metrics | Verify humans are actually reviewing |

---

## What We Cannot Control

Be honest about what's impossible:

| Cannot Prevent | Can Only Mitigate |
|----------------|-------------------|
| Novel attack techniques | Detect via anomaly, learn and adapt |
| Determined insider | Limit access, detect misuse, enforce consequences |
| User lying about intent | Verify where possible, monitor behaviour |
| Model emergent behaviour | Constrain at infrastructure level |
| Zero-day in model | Layered defences, quick response capability |

---

## Summary

1. **Accept that bypasses will happen** — design for detection and response, not just prevention
2. **Layer your controls** — attacker must defeat all layers, not just one
3. **Enforce at infrastructure, not instruction** — don't rely on AI to limit itself
4. **Verify controls work** — canary testing, adversarial testing, metrics
5. **Make misuse costly** — audit trails, consequences, attribution
6. **Learn from every bypass** — improve controls, update tests, share knowledge
7. **Provide good alternatives** — shadow AI happens when official AI is too restrictive

---

## Control Checklist

### Guardrail Bypasses
- [ ] Multi-encoding detection and decoding
- [ ] ML classifiers for semantic intent
- [ ] Conversation-level analysis (not just per-message)
- [ ] RAG content sanitisation
- [ ] Regular adversarial testing
- [ ] Canary injection for guardrail verification

### Intent Declaration Bypasses
- [ ] Structured declarations with verifiable fields
- [ ] Manager attestation for sensitive access
- [ ] Audit trail of all declarations
- [ ] Periodic re-certification
- [ ] Behavioural monitoring independent of declaration
- [ ] Clear consequences for misuse

### Agentic Bypasses
- [ ] Infrastructure-level scope enforcement
- [ ] Action validation independent of agent reasoning
- [ ] Tool output sanitisation
- [ ] Circuit breakers with hard limits
- [ ] Approval workflows for significant actions
- [ ] Outcome validation after completion

### Architectural Bypasses
- [ ] Shadow AI detection (network, DLP)
- [ ] API gateway for all model access
- [ ] Configuration change controls
- [ ] Privileged access management
- [ ] Good internal AI alternatives

### Process Bypasses
- [ ] HITL workload management
- [ ] Canary testing for reviewers
- [ ] Reviewer metrics and monitoring
- [ ] Separation of duties for config
- [ ] Configuration drift detection

---

*AI Security Blueprint — Bypass Prevention*
