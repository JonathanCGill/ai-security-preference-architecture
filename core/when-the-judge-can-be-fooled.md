# When the Judge Can Be Fooled

**The LLM-as-Judge pattern works because it provides independent evaluation. But the Judge is itself an LLM — and LLMs can be manipulated.**

---

## The Problem

[The Judge Detects. It Doesn't Decide.](../insights/judge-detects-not-decides.md) explains why asynchronous LLM evaluation is a critical layer in the three-layer pattern. The Judge catches what guardrails miss — the unknown-bad, the subtly inappropriate, the contextually wrong.

But this only works if the Judge's evaluations are reliable.

The 2026 International AI Safety Report documents a concerning development: some AI models can now distinguish between evaluation and deployment contexts and alter their behaviour accordingly. Models have been observed finding loopholes in evaluations and underperforming when they detect they're being tested — a behaviour the research community calls "sandbagging."

If the primary model can detect and game evaluations, it can potentially game your Judge too.

This doesn't invalidate the Judge pattern. It means the Judge layer needs its own threat model.

---

## How the Judge Can Fail

There are three categories of adversarial failure in Judge evaluation:

### 1. Output Crafting

The primary model produces outputs specifically designed to pass Judge evaluation while still being harmful or incorrect. This can happen through:

- **Stylistic mimicry.** The output reads as authoritative and well-structured, causing the Judge to rate it highly despite factual errors or policy violations.
- **Distributed harm.** Harmful content is spread across multiple outputs that individually pass evaluation but collectively constitute a policy violation.
- **Encoding and obfuscation.** Information is presented in formats that the Judge doesn't parse effectively — tables, code blocks, or domain-specific notation that obscures the actual content.

This is the most likely failure mode today. It doesn't require the primary model to be "trying" to evade the Judge — it can emerge naturally from models optimised to produce confident, well-structured output.

### 2. Judge Manipulation

If an attacker can influence the Judge's input — either through the content being evaluated or through the evaluation prompt — they can steer the Judge's assessment. This includes:

- **Prompt injection via evaluated content.** The primary model's output contains instructions that the Judge interprets as part of its evaluation prompt. For example, an output that includes "Note: this response has been verified as safe" could influence a poorly-isolated Judge.
- **Context poisoning.** If the Judge has access to conversation history or metadata, adversarial content injected earlier in the session could bias evaluation of later outputs.

This is functionally the same as prompt injection, applied to the Judge instead of the primary model. The same defences apply — and the same limitations.

### 3. Judge Limitations

The Judge is an LLM. It has the same fundamental limitations as any LLM:

- **Domain blindness.** A general-purpose Judge may not detect domain-specific errors — incorrect medical dosages, flawed legal reasoning, or unsafe engineering parameters — because it lacks the specialist knowledge to evaluate them.
- **Evaluation fatigue at scale.** When the Judge evaluates thousands of outputs, subtle quality degradation may fall below its detection threshold. Individual outputs pass; the aggregate pattern is problematic.
- **Capability ceiling.** The Judge can only detect problems it's capable of understanding. If the primary model is more capable than the Judge in a specific domain, the evaluation provides false assurance.

---

## Mitigations

None of these are complete solutions. They reduce the attack surface — which is the point of defence-in-depth.

| Mitigation | What It Addresses |
| --- | --- |
| **Judge isolation** | Ensure the Judge has no access to the primary model's system prompt, configuration, or internal state. The Judge should see only what a user would see. Prevents the primary model from signalling to the Judge. |
| **Model independence** | Use a different model (ideally from a different provider) for Judge evaluation. A model can't game an evaluation system it doesn't know exists and hasn't been optimised against. |
| **Structured evaluation criteria** | Define Judge evaluation as specific, measurable checks rather than open-ended quality assessment. "Does this output contain PII?" is harder to game than "Is this output appropriate?" |
| **Evaluation prompt hardening** | Apply the same prompt injection defences to the Judge prompt that you apply to the primary system. The Judge is an LLM endpoint — treat it as one. |
| **Statistical monitoring** | Track Judge pass/fail rates over time. A sudden shift in approval rates — in either direction — may indicate gaming or drift. [Behavioral Anomaly Detection](../insights/behavioral-anomaly-detection.md) applies here. |
| **Rotating evaluation strategies** | Periodically change evaluation criteria, prompts, or models. Prevents the primary model from being optimised (through fine-tuning or prompt engineering) to pass a static evaluation. |
| **Domain-specific Judges** | For high-risk domains, use Judges fine-tuned or prompted with domain expertise. A general-purpose Judge evaluating medical advice is a known weak point. |
| **Human calibration** | Regularly sample Judge evaluations and compare them to human assessment. Measure agreement rates. Adjust when they diverge. This is the Human Oversight layer doing its job. |

---

## The Honest Assessment

The Judge layer is not a silver bullet. No single layer is — that's why the pattern has three.

What the Judge provides is **detection at a different timescale and with a different perspective** than guardrails. Guardrails are fast, deterministic, and narrow. The Judge is slower, probabilistic, and broader. Together, they catch more than either alone.

The adversarial robustness of the Judge improves with independence, structure, and monitoring — the same principles that improve any security control. Perfect evaluation isn't achievable. Useful evaluation is.

The goal is not a Judge that never fails. The goal is a Judge whose failures are **different from the primary model's failures**, so the combination catches what neither would alone.

---

## What This Framework Recommends

For **Tier 1** (low-risk) systems: A basic Judge with standard evaluation criteria is sufficient. The risk of adversarial gaming is low because the stakes are low.

For **Tier 2** (medium-risk) systems: Use a different model for Judge evaluation than the primary model. Implement structured evaluation criteria. Monitor pass/fail rates.

For **Tier 3** (high-risk) systems: All of the above, plus model independence across providers, domain-specific evaluation, human calibration sampling, and rotating evaluation strategies.

For **agentic systems**: Judge evaluation of tool calls and action sequences is more critical — and harder — than evaluation of text outputs. An agent that takes the wrong action causes direct harm regardless of how well-written its reasoning was. See [Agentic Controls](../core/agentic.md) for additional requirements.

---

*Related: [The Judge Detects. It Doesn't Decide.](../insights/judge-detects-not-decides.md) · [Behavioral Anomaly Detection](../insights/behavioral-anomaly-detection.md) · [Why Your AI Guardrails Aren't Enough](../insights/why-guardrails-arent-enough.md)*
