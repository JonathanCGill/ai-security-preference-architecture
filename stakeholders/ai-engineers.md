# AI Engineers

**ML Engineers, AI Developers, Data Scientists, Platform Engineers — implementation patterns, not governance theory.**

> *Part of [Stakeholder Views](README.md) · [AI Runtime Behaviour Security](../)*

---

## The Problem You Have

You're building AI systems. Your security and risk teams have requirements that sound like governance bureaucracy. You've been asked for "guardrails," "a Judge," "human oversight," "PACE resilience" — but what you actually need is:

- **What do I implement?** Concrete patterns, not abstract principles.
- **Where do I put it?** Architecture-level placement in the pipeline.
- **How do I test it?** Verification that controls actually work.
- **What breaks if I get it wrong?** Failure modes you need to handle.
- **What already exists?** Libraries, services, and platform features you can use instead of building from scratch.

---

## What This Framework Gives You

### The three things you're building

Every AI system needs some combination of these. Your risk tier determines how much:

**1. Guardrails** — input/output filters that run on every request.

What you're implementing:
- Input: injection detection, content policy check, PII redaction, schema validation
- Output: hallucination check (ground against source), PII scan, toxicity filter, format validation
- Latency budget: ~10-20ms total
- Libraries: NVIDIA NeMo Guardrails, Guardrails AI, LangChain output parsers, AWS Bedrock Guardrails, Azure AI Content Safety

**2. LLM-as-Judge** — an independent LLM that evaluates your task agent's output.

What you're implementing:
- A separate model (different from your task agent) that receives the input, output, and context
- A structured evaluation prompt that checks policy compliance, factual grounding, safety, quality
- A scoring/classification response (pass/fail/escalate with confidence)
- Routing logic: pass → deliver, fail → block, low-confidence → human review queue

Key constraint: **the Judge must use a different model than your task agent**. Same-model evaluation has correlated failure modes. If GPT-4 hallucinates a fact, GPT-4 evaluating that fact has a higher chance of missing it than Claude evaluating it, and vice versa.

- Implementation guide: [LLM-as-Judge Implementation](../extensions/technical/llm-as-judge-implementation.md)
- Prompt examples: [Judge Prompt Examples](../extensions/templates/judge-prompt-examples.md)
- Model selection: [Judge Model Selection](../extensions/technical/judge-model-selection.md)
- Calibration: [Judge Assurance](../core/judge-assurance.md)

**3. Circuit breaker / PACE fail postures** — what your system does when control layers fail.

What you're implementing:
- Health checks on guardrail and Judge services
- Fallback routing when each layer is unavailable
- A kill switch that removes the AI from the path entirely
- Pre-defined degradation: full service → limited scope → human-only → static fallback

This is infrastructure code, not AI code. Treat it like any service reliability pattern.

### Implementation by risk tier

| Tier | What You Build | Judge Configuration | Human Oversight |
|---|---|---|---|
| **LOW** | Basic input/output guardrails | Optional — 1-5% sampling for monitoring | None (exception-based) |
| **MEDIUM** | Standard guardrails + Judge integration | 5-10% sampling, batch evaluation | Review flagged items only |
| **HIGH** | Full guardrail suite + Judge + routing | 20-50% coverage, near real-time | Flagged items + random sampling |
| **CRITICAL** | Hardened guardrails + Judge + human gate | 100% coverage, synchronous (blocks delivery) | All high-impact decisions reviewed |

### Platform-specific patterns

If you're building on a specific platform, these map framework controls to platform services:

| Platform | Pattern Guide | Key Services |
|---|---|---|
| AWS Bedrock | [AWS Bedrock Patterns](../infrastructure/reference/platform-patterns/aws-bedrock.md) | Bedrock Guardrails, CloudWatch, IAM |
| Azure AI | [Azure AI Patterns](../infrastructure/reference/platform-patterns/azure-ai.md) | Azure AI Content Safety, Responsible AI toolkit |
| Databricks | [Databricks Patterns](../infrastructure/reference/platform-patterns/databricks.md) | MLflow, Unity Catalog, Model Serving |
| LangChain / LangGraph | [Integration Guide](../maso/integration/integration-guide.md) | LangSmith, callbacks, output parsers |

### Testing your controls

Controls that aren't tested don't work. The framework provides:

- [Testing Guidance](../extensions/templates/testing-guidance.md) — structured test scenarios for each control layer
- [Red Team Playbook](../maso/red-team/red-team-playbook.md) — 13 adversarial scenarios (prompt injection, data exfiltration, privilege escalation, consensus manipulation)
- [Judge Assurance](../core/judge-assurance.md) — how to measure Judge accuracy, calibrate confidence thresholds, detect drift
- [When the Judge Can Be Fooled](../core/when-the-judge-can-be-fooled.md) — failure modes specific to the evaluation layer

---

## Your Starting Path

| # | Document | Why You Need It |
|---|---|---|
| 1 | [Controls](../core/controls.md) | Three-layer implementation reference — what to build |
| 2 | [Quick Start](../QUICK_START.md) | Zero to working controls in 30 minutes |
| 3 | [LLM-as-Judge Implementation](../extensions/technical/llm-as-judge-implementation.md) | Judge layer patterns, prompts, routing logic |
| 4 | [Judge Assurance](../core/judge-assurance.md) | How to measure and calibrate Judge accuracy |
| 5 | [Checklist](../core/checklist.md) | Track what you've implemented |

**If you're building agents:** [Agentic Controls](../core/agentic.md) — tool scoping, action classification, confirmation gates.

**If you're building multi-agent systems:** [MASO Integration Guide](../maso/integration/integration-guide.md) — message bus signing, per-agent identity, cross-agent DLP.

**If you're building RAG:** [RAG Security](../extensions/technical/rag-security.md) — the attack surface you probably haven't considered.

---

## What You Can Do Monday Morning

1. **Add input guardrails.** If you have no controls today, start with injection detection on input. NVIDIA NeMo, Guardrails AI, or your platform's built-in content safety. This alone catches ~90% of known-pattern attacks.

2. **Add output grounding.** If your system uses RAG, validate that the response is actually grounded in the retrieved documents. This catches hallucinated facts before they reach users.

3. **Implement a Judge on 10% of traffic.** Pick a different model from your task agent. Use the [Judge Prompt Examples](../extensions/templates/judge-prompt-examples.md) as starting points. Log results. Measure the catch rate. This tells you what your guardrails are missing.

4. **Wire a circuit breaker.** If your guardrail service goes down, your system should degrade to a safe state — not continue without protection. A simple health check and fallback route takes an afternoon.

5. **Red team your own system.** Spend an hour trying to break it. The [Red Team Playbook](../maso/red-team/red-team-playbook.md) has structured scenarios. Document what you find. This is the most effective way to identify control gaps.

---

## Common Objections — With Answers

**"The Judge adds latency to every request."**
Only for CRITICAL tier. For HIGH tier, run it asynchronously — it doesn't block the response. For MEDIUM tier, run it on a sample. For LOW tier, it's optional. See [Cost & Latency](../extensions/technical/cost-and-latency.md).

**"Our model is already aligned / fine-tuned / safe."**
Model alignment is necessary but insufficient. Alignment reduces the base rate of harmful outputs but doesn't eliminate it. Prompt injection bypasses alignment. RAG poisoning bypasses alignment. Edge cases that weren't in the training data bypass alignment. Runtime controls catch what alignment misses.

**"We don't have budget for a second model (the Judge)."**
The Judge doesn't have to be expensive. A smaller, faster model (Haiku-class) running a focused evaluation prompt often outperforms a larger model for specific policy checks. Sample at 10% to start. The [Judge Model Selection](../extensions/technical/judge-model-selection.md) guide covers cost-effective configurations.

**"Human oversight doesn't scale."**
Correct — which is why the framework doesn't require human review of every transaction (except at CRITICAL tier). The Judge handles scale. Humans handle the edge cases the Judge flags and the random samples that keep the system honest. See [Humans Remain Accountable](../insights/humans-remain-accountable.md).

**"This is security's job, not mine."**
Security builds the platform. You deploy on it. On approved infrastructure, most controls — guardrails, logging, circuit breakers — are platform primitives that you inherit by deploying, not things you implement from scratch. The framework gives you concrete patterns for the use-case-specific configuration that sits on top. The [Controls](../core/controls.md) document tells you what each layer does. The [Checklist](../core/checklist.md) tracks your progress. If you're building controls from scratch, either the platform is missing a capability (flag it) or you're building something genuinely novel (in which case the framework guides your design).

---

*AI Runtime Behaviour Security, 2026 (Jonathan Gill).*
