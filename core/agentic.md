# Agentic AI Controls

Additional controls for AI systems that take autonomous actions.

---

## What Makes Agents Different

| Characteristic | Chatbot | Agent |
|---------------|---------|-------|
| Actions | Responds only | Takes real-world actions |
| Autonomy | Single turn | Multi-step, self-directed |
| Scope | Fixed | May expand based on goals |
| Failure mode | Bad answer | Bad action with consequences |

**Key risk:** Agents can cause harm at machine speed without human review.

---

## Core Principle

**Infrastructure beats instructions.**

Don't tell the agent "only access customer service data."  
Give it credentials that can only access customer service data.

| Bad (Instruction) | Good (Infrastructure) |
|-------------------|----------------------|
| "Only access CS data" | Database view exposes only CS data |
| "Don't send emails without approval" | Email API requires approval token |
| "Stay within budget" | Hard spending cap at API gateway |

---

## Control Categories

### 1. Scope Enforcement

Limit what the agent can access and do — technically, not via prompts.

| Control | Implementation |
|---------|----------------|
| **Network whitelist** | Agent can only reach approved endpoints |
| **Data views** | Agent sees only authorised data subset |
| **Action whitelist** | Only permitted actions can execute |
| **Resource caps** | Hard limits on compute, API calls, cost |
| **Time limits** | Maximum execution duration |

### 2. Action Validation

Validate every action independently. Don't trust agent reasoning.

**Validation flow:**

![Action Validator Flow](../images/action-validator-flow.svg)

### 3. Tool Output Sanitisation

Tool outputs are injection vectors. Treat as untrusted.

| Control | Purpose |
|---------|---------|
| Scan for instructions | Detect "ignore previous" patterns |
| Truncate length | Limit context pollution |
| Mark as data | Clear framing that this is data, not instructions |
| Flag suspicious | Human review before continuing |

### 4. Approval Workflows

Make approval meaningful, not rubber-stamp.

| Bad | Good |
|-----|------|
| "Approve?" | Show context, data, impact, expected outcome |
| Approve/Deny only | Approve / Deny / Modify / Escalate |
| Same approver for all | Different approvers by action type |
| No expiry | Approval expires, must re-request |

### 5. Circuit Breakers

Hard stops that trigger regardless of agent "reasoning."

| Threshold | Action |
|-----------|--------|
| >100 actions in one task | Pause |
| >$50 in API calls | Pause |
| >30 minutes execution | Pause |
| >10% error rate | Pause |
| Any scope violation | Terminate |
| Any irreversible action | Require approval |

---

## Agent Risk Tiers

Agents are typically HIGH or CRITICAL tier. LOW/MEDIUM agents are rare.

| Agent Type | Typical Tier | Key Controls |
|------------|--------------|--------------|
| Read-only research | HIGH | Scope limits, output review |
| Internal automation | HIGH | Action whitelist, circuit breakers |
| Customer-facing | CRITICAL | Full approval workflow |
| Financial actions | CRITICAL | All controls, human approval |

---

## Judge for Agents

Agent interactions need deeper evaluation.

| Additional Criteria | Question |
|--------------------|----------|
| Goal alignment | Did agent pursue stated goal? |
| Action appropriateness | Were actions proportionate? |
| Scope adherence | Did agent stay in bounds? |
| Reasoning quality | Was the reasoning sound? |
| Efficiency | Did agent take unnecessary steps? |

---

## Monitoring

| Signal | Concern |
|--------|---------|
| Action volume spike | Runaway agent |
| Error rate increase | Agent confused or attacking |
| Novel action patterns | Unexpected behaviour |
| Scope boundary probes | Attempted breakout |
| Cost anomalies | Resource abuse |

---

## Key Takeaways

1. **Enforce via infrastructure** — Agents can ignore instructions
2. **Validate every action** — Independent of agent reasoning
3. **Sanitise tool outputs** — They're injection vectors
4. **Use circuit breakers** — Hard stops that can't be reasoned around
5. **Require approval for impact** — Irreversible actions need humans
6. **Monitor aggressively** — Agents can cause harm fast
