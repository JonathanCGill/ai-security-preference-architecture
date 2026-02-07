# Emerging AI Trends — Impact on AI Security Blueprint

This document assesses how emerging AI trends affect the reference architecture and identifies required adaptations.

---

## Executive Summary

The core architecture principle — **Guardrails prevent, Judge detects, Humans decide** — remains valid across emerging trends. However, **agentic AI** fundamentally challenges the interaction-centric model and requires architectural extension.

| Architecture Component | Robustness | Action Required |
|-----------------------|------------|-----------------|
| Three-layer model | ✅ Robust | None |
| Risk-based tiering | ✅ Robust | Extend criteria for agentic |
| Guardrails | ⚠️ Stressed | Extend for multimodal, agentic |
| Judge | ⚠️ Stressed | Extend for trajectories |
| HITL | ⚠️ Stressed | Shift to checkpoints |
| Logging | ✅ Robust | Extend for traces |

---

## Trend Analysis

### 1. Agentic AI

**What it is:** AI systems that take autonomous multi-step actions, use tools, interact with external systems, and operate with minimal human intervention.

**Impact: HIGH — Requires architectural extension**

| Current Model | Agentic Reality |
|---------------|-----------------|
| Single request → response | Multi-step execution chains |
| Evaluate one interaction | Evaluate trajectory and cumulative effect |
| Human reviews before action | Actions happen autonomously |
| Clear boundaries | Tool use, API calls, real-world effects |
| One AI to govern | Orchestrator + multiple specialist agents |

**What breaks:**
- Interaction-centric Judge can't assess multi-step chains
- Sampling strategies assume independent interactions
- HITL can't review every step in real-time
- Guardrails designed for text I/O, not tool calls

**Required adaptations:**

![Agentic AI Control Model](../../images/agentic-control-model.svg)

**New control requirements for agentic AI:**

| Control | Purpose |
|---------|---------|
| **Plan approval** | Review intended actions before execution |
| **Action-level guardrails** | Check each tool call / action |
| **Circuit breakers** | Hard limits on steps, cost, scope |
| **Trajectory logging** | Full trace of execution path |
| **Trajectory evaluation** | Judge assesses full chain |
| **Deviation detection** | Alert when execution diverges from plan |
| **Rollback capability** | Undo actions where possible |

---

### 2. Multimodal AI

**What it is:** AI that processes and generates images, audio, video, and combinations thereof.

**Impact: MEDIUM — Extend existing controls**

**What works:**
- Three-layer model applies (guardrails, Judge, HITL)
- Risk-based approach applies
- Logging and audit requirements apply

**What needs extension:**

| Modality | Guardrail Maturity | Judge Capability | Notes |
|----------|-------------------|------------------|-------|
| Text | ✅ Mature | ✅ Strong | Current focus |
| Images | ⚠️ Emerging | ⚠️ Developing | NSFW, deepfakes, PII in images |
| Audio | ⚠️ Limited | ⚠️ Limited | Voice cloning, impersonation |
| Video | ❌ Immature | ❌ Limited | Computational cost high |

**Required adaptations:**

1. **Input guardrails** — Extend to detect:
   - Harmful image content (NSFW, violence, CSAM)
   - Deepfakes and manipulated media
   - PII in images (faces, documents)
   - Audio impersonation attempts

2. **Output guardrails** — Extend to filter:
   - Generated NSFW content
   - Generated deepfakes / impersonation
   - Copyright-infringing generations
   - Watermarking for AI-generated content

3. **Judge** — Must evaluate:
   - Image/audio/video appropriateness
   - Cross-modal consistency (does image match text?)
   - Multimodal attack patterns

4. **Logging** — Must capture:
   - Input media (or hashes/references)
   - Generated media
   - Sufficient for investigation

**Platform support:**

| Platform | Image Guardrails | Audio/Video |
|----------|-----------------|-------------|
| Bedrock Guardrails | ✅ Image filters | ❌ Limited |
| Azure Content Safety | ✅ Image analysis | ⚠️ Some audio |
| Google Cloud | ✅ Vision Safety | ⚠️ Some audio |

---

### 3. Reasoning Models

**What it is:** Models that "think" before responding (o1, o3, Claude extended thinking, DeepSeek R1).

**Impact: LOW — Already addressed**

The architecture already accommodates reasoning models:
- [Judge Model Selection Guide](03-llm-as-a-judge.md) covers tiered Judge with reasoning models
- Reasoning models are well-suited to the Judge role
- Extended thinking provides audit trail

**Minor considerations:**
- Cost management (reasoning models are expensive)
- Latency for real-time applications
- Transparency of reasoning chain

---

### 4. Longer Context Windows

**What it is:** Models that can process 100K, 200K, 1M+ tokens.

**Impact: LOW — Operational adjustments**

**What works:** Architecture unchanged.

**Adjustments needed:**

| Aspect | Impact |
|--------|--------|
| Logging cost | Higher storage requirements |
| Judge cost | Evaluating longer contexts costs more |
| Sampling | May need to adjust sampling rates |
| Attack surface | More room for injection in long contexts |

**Guardrail considerations:**
- Prompt injection can be buried deeper in long contexts
- May need segmented scanning
- Attention-based attacks exploit long contexts

---

### 5. Real-Time / Streaming AI

**What it is:** AI that processes and generates content in real-time streams (live conversation, video analysis).

**Impact: MEDIUM — Latency trade-offs**

**Challenges:**
- Guardrails must be fast enough for real-time
- Can't wait for full response to evaluate
- Judge must work on streaming data

**Adaptations:**

| Component | Streaming Adaptation |
|-----------|---------------------|
| Guardrails | Incremental checking, token-level filtering |
| Logging | Stream capture, chunked storage |
| Judge | Evaluate chunks or sessions, not single interactions |
| HITL | Post-session review, real-time alerts for critical issues |

---

### 6. Fine-Tuned / Custom Models

**What it is:** Organisation-specific models trained or fine-tuned on proprietary data.

**Impact: LOW — Validation requirements**

**Architecture unchanged**, but adds requirements:

| Requirement | Purpose |
|-------------|---------|
| Model validation | Ensure fine-tuning hasn't introduced issues |
| Bias testing | Fine-tuning can introduce or amplify bias |
| Capability assessment | Understand what the model can/can't do |
| Version control | Track model versions and changes |

These align with existing Model Risk Management (SR 11-7) requirements.

---

### 7. Local / Edge AI

**What it is:** AI running on-device (phones, laptops, IoT) rather than cloud.

**Impact: MEDIUM — Different deployment model**

**Challenges:**
- Can't insert guardrails between user and model
- Logging may be limited or delayed
- Judge can't evaluate in real-time
- Less control over model behaviour

**Adaptations:**

| Component | Edge Adaptation |
|-----------|----------------|
| Guardrails | Embedded in application, device-side |
| Logging | Local buffer, sync when connected |
| Judge | Server-side evaluation of synced logs |
| HITL | Async review of aggregated data |

**Risk implications:**
- Less real-time control = higher risk tier
- May need to limit edge AI to lower-risk use cases
- Or accept different control model with delayed assurance

---

### 8. AI-to-AI Interactions

**What it is:** AI systems that communicate with each other, including multi-agent systems and AI pipelines.

**Impact: HIGH — Attribution challenges**

**Challenges:**
- Which AI is "responsible" for an outcome?
- How to evaluate a chain of AI interactions?
- Emergent behaviour from AI combinations
- Attack propagation across AI systems

**Required adaptations:**

For AI-to-AI interactions, implement **unified trace logging** that captures the full chain:
- Trace ID that follows the request across all AI systems
- Per-AI inputs and outputs logged
- Final outcome attribution
- Accountability mapping

**Control requirements:**
- Trace IDs across AI interactions
- Per-AI guardrails still apply
- Judge evaluates full trace
- Attribution model for accountability

---

## Summary: Architecture Durability

### What Remains Stable

| Principle | Why It Survives |
|-----------|-----------------|
| Layered defence | Universal security principle |
| Risk-based controls | Regulatory and practical necessity |
| Human accountability | Regulatory requirement, ethical imperative |
| Logging and audit | Foundation for all assurance |
| Guardrails → Judge → HITL | Functional abstraction, not implementation |

### What Requires Extension

| Trend | Extension Needed |
|-------|-----------------|
| Agentic AI | Plan approval, trajectory evaluation, circuit breakers |
| Multimodal | Extend guardrails and Judge to non-text modalities |
| AI-to-AI | Trace logging, attribution model |
| Edge AI | Delayed assurance model |
| Streaming | Incremental evaluation |

### What Might Break

**Only agentic AI fundamentally challenges the model:**

The current architecture assumes discrete interactions that can be evaluated independently. Agentic AI breaks this by:
1. Creating multi-step chains where context matters
2. Taking real-world actions that can't be "undone"
3. Operating faster than humans can review

**The fix is not to abandon the architecture but to extend it:**
- Add plan-level review (before execution)
- Add trajectory-level evaluation (in addition to interaction-level)
- Add circuit breakers (hard limits during execution)
- Shift HITL from "review everything" to "review decisions and exceptions"

> **See [Agentic Controls](04-agentic-controls.md) for the complete control set.**

---

## Recommendations

### Near-Term (Now)

1. **Implement agentic controls** — See [Agentic Controls](04-agentic-controls.md) for plan approval, circuit breakers, trajectory evaluation
2. **Monitor multimodal guardrail maturity** — Platform capabilities are evolving rapidly
3. **Implement trace logging** — Even for non-agentic systems, correlation IDs enable future capabilities

### Medium-Term (6-12 months)

1. **Develop trajectory Judge** — Extend Judge to evaluate multi-step chains
2. **Build circuit breaker patterns** — Reusable components for agentic systems
3. **Extend guardrails for multimodal** — As platform support matures

### Long-Term (12+ months)

1. **AI-to-AI governance model** — Attribution, accountability across AI chains
2. **Autonomous AI oversight** — When AI operates without human review
3. **Regulatory alignment** — EU AI Act and others will evolve; track and adapt

---

## Conclusion

The reference architecture is **durable but not static**.

The core principle — Guardrails prevent, Judge detects, Humans decide — survives because it describes *functions*, not implementations. As AI capabilities evolve, the implementations change but the functions remain.

**Agentic AI is the critical trend to watch.** It challenges the interaction-centric model and requires genuine architectural extension. Other trends (multimodal, reasoning models, longer contexts) are accommodated with relatively minor adjustments.

The framework should be treated as a **living document** that evolves with the technology. This is not a weakness — it's a design principle.

---

*AI Security Blueprint — Discussion Draft*
