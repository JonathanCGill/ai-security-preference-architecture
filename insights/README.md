# Insights

The *why* before the *how*. Each article identifies a specific problem that the [core controls](../core) and [extensions](../extensions) then solve.

---

## Start Here

These establish the foundational argument: AI systems are non-deterministic, so you can't fully test them before deployment. Runtime behavioral monitoring is the answer.

| # | Article | One-Line Summary |
|---|---------|-----------------|
| 1 | [The First Control: Choosing the Right Tool](the-first-control.md) | The best way to reduce AI risk is to not use AI where it doesn't belong |
| 2 | [Why Your AI Guardrails Aren't Enough](why-guardrails-arent-enough.md) | Guardrails block known-bad; you need detection for unknown-bad |
| 3 | [The Judge Detects. It Doesn't Decide.](judge-detects-not-decides.md) | Async evaluation beats real-time blocking for nuance |
| 4 | [Infrastructure Beats Instructions](infrastructure-beats-instructions.md) | You can't secure systems with prompts alone |
| 5 | [Risk Tier Is Use Case, Not Technology](risk-tier-is-use-case.md) | Classification is about deployment context, not model capability |
| 6 | [Humans Remain Accountable](humans-remain-accountable.md) | AI assists decisions; humans own outcomes |

---

## Emerging Challenges

Where the three-layer pattern meets its limits — and what to do about it.

| # | Article | One-Line Summary | Solution |
|---|---------|-----------------|----------|
| 7 | [The Verification Gap](the-verification-gap.md) | Current safety approaches can't confirm ground truth | [Judge Assurance](../core/judge-assurance.md) |
| 8 | [Behavioral Anomaly Detection](behavioral-anomaly-detection.md) | Aggregating signals to detect drift from normal | [Anomaly Detection Ops](../extensions/technical/anomaly-detection-ops.md) |
| 9 | [Multimodal AI Breaks Your Text-Based Guardrails](multimodal-breaks-guardrails.md) | Images, audio, and video bypass text controls | [Multimodal Controls](../core/multimodal-controls.md) |
| 10 | [When AI Thinks Before It Answers](when-ai-thinks.md) | Reasoning models need reasoning-aware controls | [Reasoning Model Controls](../core/reasoning-model-controls.md) |
| 11 | [When Agents Talk to Agents](when-agents-talk-to-agents.md) | Multi-agent systems have accountability gaps | [Multi-Agent Controls](../core/multi-agent-controls.md) |
| 12 | [The Memory Problem](the-memory-problem.md) | Long context and persistent memory create new risks | [Memory and Context Controls](../core/memory-and-context.md) |
| 13 | [You Can't Validate What Hasn't Finished](you-cant-validate-unfinished.md) | Real-time streaming breaks the validation model | [Streaming Controls](../core/streaming-controls.md) |

---

## Operational Gaps

Blind spots in most enterprise AI security programmes.

| # | Article | One-Line Summary | Solution |
|---|---------|-----------------|----------|
| 14 | [The Supply Chain Problem](the-supply-chain-problem.md) | You don't control the model you deploy | [Supply Chain Controls](../extensions/technical/supply-chain.md) |
| 15 | [RAG Is Your Biggest Attack Surface](rag-is-your-biggest-attack-surface.md) | Retrieval pipelines bypass your existing access controls | [RAG Security](../extensions/technical/rag-security.md) |

---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
