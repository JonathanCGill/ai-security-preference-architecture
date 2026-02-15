# Reasoning Model Controls

> Addendum for [Emerging Controls](/core/emerging-controls.md) — practical guidance for reasoning models.
>
> *This document uses the simplified three-tier system (Tier 1/2/3). See [Risk Tiers — Simplified Tier Mapping](risk-tiers.md#simplified-tier-mapping) for the mapping to LOW/MEDIUM/HIGH/CRITICAL.*

## The Problem

Reasoning models (OpenAI o1/o3, Claude with extended thinking, Gemini with thinking mode) generate internal chain-of-thought before producing a response. The reasoning trace may:

- Contain content that contradicts system instructions
- Reveal information the final output was designed to withhold
- Show the model "considering" harmful approaches before selecting a safe one
- Be manipulated to bypass safety controls that only check the final output

---

## Current State of Access

| Provider | Reasoning Trace Access | Implication |
|----------|----------------------|-------------|
| OpenAI (o1/o3) | Not exposed via API | Cannot monitor reasoning — output-only evaluation |
| Anthropic (extended thinking) | Exposed as `thinking` blocks | Can monitor, but content is explicitly marked as draft reasoning |
| Google (Gemini thinking) | Partially exposed | Limited inspection capability |
| Open-source (Qwen QwQ, etc.) | Fully visible | Full monitoring possible |

---

## Controls

### When Reasoning Traces Are Not Accessible

You're evaluating a black box. Your controls are output-focused:

| Control | Implementation |
|---------|---------------|
| **Strengthen output evaluation** | Judge criteria should include consistency checks: "Does this response contradict the system prompt?" |
| **Behavioral testing** | Periodically test with adversarial inputs designed to exploit reasoning-level bypasses |
| **Comparative evaluation** | Compare reasoning model outputs against non-reasoning model outputs on the same inputs; flag divergences |
| **Provider assurance** | Evaluate provider's claims about reasoning trace safety filtering |

### When Reasoning Traces Are Accessible

Additional controls become possible:

| Control | Implementation |
|---------|---------------|
| **Trace scanning** | Apply guardrails to reasoning traces, not just final outputs |
| **Instruction adherence check** | Verify the reasoning trace doesn't contain evidence of attempting to bypass system instructions |
| **Sensitive data in trace** | Check reasoning traces for PII or confidential data that shouldn't appear even in internal reasoning |
| **Reasoning-output consistency** | Flag cases where the reasoning leads to one conclusion but the output states another |

### Practical Limitations

Be honest about what you can't do:

1. **Reasoning traces are not ground truth.** The model may "think" one thing and output another — this is by design (safety training teaches models to reason past harmful thoughts).
2. **Monitoring traces at scale is expensive.** Reasoning traces can be 10–100x longer than the final output.
3. **Most reasoning traces are benign.** The signal-to-noise ratio for trace monitoring is very low.

### Recommendation by Risk Tier

| Risk Tier | Reasoning Model Approach |
|-----------|------------------------|
| **Tier 1** | Use reasoning models freely; output-only evaluation sufficient |
| **Tier 2** | Output-focused evaluation with periodic behavioral testing |
| **Tier 3** | If traces are accessible: sample-based trace monitoring. If not: enhanced output evaluation + adversarial testing. Consider whether a reasoning model is necessary — a non-reasoning model with stronger output controls may be more governable |

---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
