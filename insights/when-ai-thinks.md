# When AI Thinks Before It Answers

*Reasoning models create a new problem: do you trust the thinking you can't see?*

---

A new class of models thinks before responding. OpenAI's o1, Anthropic's extended thinking, and similar approaches run internal reasoning chains — sometimes for seconds, sometimes longer — before producing output.

This changes the security model in ways we're only starting to understand.

---

## The Hidden Chain Problem

Traditional LLMs produce output token by token. What you see is what you get. The response is the reasoning.

Reasoning models are different. They generate internal thinking — planning, exploration, self-correction — then produce a final answer. The thinking may or may not be visible to you.

This creates a trust gap:

**What you see:** A well-reasoned, confident answer.
**What you don't see:** The chain of thought that produced it.

Was the reasoning sound? Did the model consider the right factors? Did it explore paths that would concern you before settling on a safe-looking answer? You don't know.

---

## Security Implications

### Guardrails may not see the reasoning

If your input guardrails check the user's prompt and your output guardrails check the final response, neither inspects the internal reasoning.

An attack could:
- Trigger problematic reasoning that produces a clean-looking output
- Embed instructions that affect the thinking chain, not the prompt
- Exploit the gap between what the model "thinks" and what it "says"

The attack surface isn't just input → output anymore. It's input → reasoning → output, and you may only see two of those three.

### The Judge needs reasoning access

A Judge evaluating only the final output can't assess whether the reasoning was appropriate. Two identical outputs can come from very different reasoning paths — one sound, one problematic.

For HIGH and CRITICAL systems, you likely need:
- Access to the reasoning chain (if the model exposes it)
- Judge evaluation of the reasoning, not just the output
- Policies about what constitutes acceptable reasoning

This is new territory. Evaluation criteria for reasoning quality are not well-established.

### Latency changes the calculus

Reasoning takes time. Complex problems might trigger 30 seconds or more of "thinking" before a response.

This affects:
- User experience (acceptable for some use cases, not others)
- Timeout handling (what happens to a request that's still "thinking"?)
- Cost (thinking tokens may be charged differently)
- Guardrail architecture (inline checks on something that takes 30 seconds?)

The assumption that AI responses are fast is baked into many architectures. Reasoning models break that assumption.

---

## What the Framework Says

The three-layer model still applies, with extensions:

### Guardrails

- Continue to validate inputs before they reach the model
- Continue to filter outputs before they reach users
- **Add:** reasoning chain inspection where accessible
- **Accept:** some attacks may occur in the hidden space you can't inspect

### Judge

- Evaluate the final output (still necessary)
- **Add:** evaluate reasoning chains when available
- **Develop:** criteria for sound vs. problematic reasoning
- **Accept:** some evaluation may require matching reasoning capability (expensive)

### Human Oversight

- Review outputs that seem suspicious
- **Add:** review reasoning chains for high-stakes decisions
- **Recognise:** humans may not be qualified to evaluate complex reasoning
- **Escalate:** to domain experts when reasoning is outside reviewer competence

---

## The Deeper Question

Reasoning models are more capable. They solve problems that previous models couldn't. The capability comes from the thinking.

But trust in AI systems has historically come from transparency. We inspect inputs. We inspect outputs. We understand (roughly) how the model works.

Reasoning models ask: do you trust the black box to think well?

The answer isn't yes or no. It's: what evidence do you need, and can you get it?

For LOW-tier systems, maybe you accept the black box. The stakes are low.

For CRITICAL systems, you probably need reasoning visibility. If the model won't show its thinking, it may not be appropriate for decisions that matter.

---

## Practical Guidance

**If you're deploying reasoning models today:**

1. Understand what reasoning access you have (full chain, summary, none)
2. Log the reasoning where available — you'll need it for incidents
3. Adjust latency expectations in your SLAs
4. Consider whether your Judge can evaluate reasoning, or just outputs
5. Be conservative with CRITICAL use cases until evaluation methods mature

**If you're building a framework:**

1. Add "reasoning transparency" as an evaluation criterion for model selection
2. Extend logging requirements to include reasoning chains
3. Develop Judge prompts that can assess reasoning quality
4. Plan for the compute cost of evaluating thinking, not just outputs

---

## The Trajectory

Reasoning capability will improve. Models will think longer and more effectively. The competitive advantage of reasoning means it won't stay optional.

Security and governance approaches that assume transparent, fast, input-output models will age poorly.

The framework needs to evolve to handle systems that think — even when we can't watch them do it.

---

*From the [Enterprise AI Security Framework](https://github.com/JonathanCGill/enterprise-ai-security-controls-framework) — operational controls for enterprise AI systems.*
