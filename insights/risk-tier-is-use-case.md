# Risk Tier Is Use Case, Not Technology

*The same model can be low-risk or critical — classification is about deployment, not capability*

---

"We're using GPT-4. What controls do we need?"

Wrong question.

GPT-4 in a sandbox where developers experiment with prompts needs minimal controls. GPT-4 making credit decisions needs maximum controls. Same model. Completely different risk.

Risk classification is about what the AI system does, not what it can do.

---

## The Technology Trap

It's tempting to classify AI systems by their underlying technology:

- "Foundation models are high-risk"
- "Fine-tuned models are medium-risk"
- "Small models are low-risk"

This is backwards. A small model that denies loans is higher risk than a massive model that suggests meeting times. Capability doesn't determine impact. Deployment does.

---

## What Actually Determines Risk

### Decision authority

Does the AI make decisions or inform them?

An AI that recommends — with a human choosing whether to follow the recommendation — is lower risk than an AI that acts autonomously. The human provides a check.

An AI that executes decisions without human review? Higher risk. Every output has direct consequences.

### Reversibility

Can you undo the outcome if the AI is wrong?

A wrong email draft is deleted in seconds. A wrong trade executes in milliseconds and moves markets. A wrong medical recommendation might not be caught until harm occurs.

Reversible errors are lower risk than irreversible ones.

### Data sensitivity

What information does the system access?

An AI working with public data has limited harm potential. An AI with access to customer PII, financial records, or health information can cause significant damage through exposure, misuse, or inference.

The same model becomes higher risk when you connect it to sensitive data.

### Audience

Who consumes the output?

Internal users with domain expertise can spot AI errors. They know when something looks wrong. They exercise judgment.

External customers trust the output. They don't have context to evaluate accuracy. They act on what the AI says.

Customer-facing deployments are higher risk than internal tools.

### Scale

How many people or decisions are affected?

A bug affecting one user is an incident. A bug affecting a million users is a crisis. Scale multiplies impact.

High-volume deployments require tighter controls even when individual transaction risk is low.

### Regulatory context

Is this a regulated activity?

Credit decisions, medical advice, employment screening, and financial recommendations all have regulatory frameworks. AI systems in these domains face heightened scrutiny and potential liability regardless of technical risk.

Regulation elevates risk tier.

---

## The Risk Matrix

| Factor | Lower Risk | Higher Risk |
|--------|-----------|-------------|
| Authority | Recommends | Decides |
| Reversibility | Easily undone | Permanent |
| Data | Public | Sensitive |
| Audience | Internal experts | External customers |
| Scale | Few users | Many users |
| Regulation | Unregulated | Regulated |

A system that's high on multiple factors is high-tier. A system that's low across the board is low-tier. Most systems fall somewhere in the middle.

---

## Tier Definitions

**CRITICAL** — Direct, automated decisions affecting customers, finances, or safety. Errors cause immediate, significant harm.

*Credit approval. Fraud blocking. Medical triage. Trading systems.*

**HIGH** — Significant influence on decisions or access to sensitive data. Errors cause material harm with some recovery path.

*Customer service with account access. HR screening. Legal document analysis.*

**MEDIUM** — Moderate impact, primarily internal use, human review expected. Errors cause inconvenience or inefficiency.

*Internal Q&A. Document drafting. Code generation with review.*

**LOW** — Minimal impact, non-sensitive context. Errors are minor inconveniences.

*Public FAQ bot. Content suggestions. Brainstorming tools.*

---

## Controls Follow Tier

Once you've classified, controls become clear:

| Control | LOW | MEDIUM | HIGH | CRITICAL |
|---------|-----|--------|------|----------|
| Input guardrails | Basic | Standard | Enhanced | Maximum |
| Output guardrails | Basic | Standard | Enhanced | Maximum |
| Judge coverage | — | 10% | 100% | 100% + real-time |
| Human review | Exceptions | Sampling | Risk-based | All significant |
| Logging retention | 90 days | 1 year | 3 years | 7 years |

Don't over-engineer LOW tier systems. Don't under-invest in CRITICAL ones.

---

## Classification Changes

Risk tier isn't permanent. Systems move.

**Upgrade triggers:**
- Adding sensitive data access
- Adding autonomous action capability
- Moving from internal to customer-facing
- Incident revealing higher actual impact

**Downgrade requirements:**
- Extended stable operation (6+ months)
- No significant incidents
- Reduced scope documented
- Governance approval

Review classifications at least annually. The system you deployed last year may have evolved.

---

## The Practical Test

When assessing a new AI system, ask:

1. If this goes wrong, who gets hurt and how badly?
2. Will we find out before or after the damage?
3. Can we undo it?

The answers tell you more about risk tier than any technical specification.

---

## The Bottom Line

Stop asking "what model is this?" Start asking "what does it do, to whom, with what data?"

Risk lives in deployment, not technology.

Classify accordingly.

---

*From the [Enterprise AI Security Framework](https://github.com/JonathanCGill/enterprise-ai-security-controls-framework) — operational controls for enterprise AI systems.*
