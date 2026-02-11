# Cost and Latency

> The three-layer pattern is not free. Budget for it.

## The Problem

Each layer adds cost and latency:

| Layer | Latency Added | Cost Per Request | At 1M requests/month |
|-------|-------------|-----------------|---------------------|
| **Guardrails** (rule-based) | 5–20ms | ~$0 (compute only) | Negligible |
| **Guardrails** (ML classifier) | 20–100ms | $0.001–0.005 | $1K–5K |
| **Judge** (LLM evaluation) | 500ms–5s | $0.01–0.05 | $10K–50K |
| **Human Oversight** (per review) | Minutes–hours | $5–50 per review | Depends on sample rate |

For a Tier 3 system running the full pattern on every request, the Judge alone can cost more than the generator.

---

## Sampling Strategies

You don't have to judge every request. Match evaluation density to risk.

### By Risk Tier

| Risk Tier | Guardrails | Judge | Human Review |
|-----------|-----------|-------|-------------|
| **Tier 1** (Low) | 100% of requests | 5–10% sample | 1% or anomaly-triggered |
| **Tier 2** (Medium) | 100% of requests | 25–50% sample | 5% + all judge flags |
| **Tier 3** (High) | 100% of requests | 100% of requests | 10% + all judge flags |

### Adaptive Sampling

Increase judge evaluation rate when signals indicate elevated risk:

| Trigger | Sampling Adjustment |
|---------|-------------------|
| Guardrail block rate above baseline | Increase judge rate by 2x |
| New user (first 50 requests) | Judge 100% |
| After-hours usage (if unusual for your environment) | Increase judge rate by 2x |
| Prompt attack detected | Judge 100% for that user for 24 hours |
| Model provider change notification | Judge 100% for 48 hours |

### Stratified Sampling

Not all requests carry equal risk. Sample by category:

| Request Type | Judge Rate | Rationale |
|-------------|-----------|-----------|
| FAQ / simple lookup | 5% | Low risk, repetitive |
| Creative generation | 25% | More variable, higher guardrail miss rate |
| Data analysis / summarisation | 50% | Accesses user data, exfiltration risk |
| Decision support | 100% | Consequential output |
| Actions / tool use | 100% | Real-world impact |

---

## Latency Budgets

Design your latency budget before adding controls.

### Example: Customer-Facing Chat (Tier 2, Streaming)

| Component | Budget | Actual |
|-----------|--------|--------|
| Input guardrails | 20ms | 15ms (rule-based) |
| LLM generation (first token) | 500ms | 400ms |
| Buffer evaluation (per chunk) | 50ms | 30ms (rule-based) |
| **Total to first visible token** | **570ms** | **445ms** |
| Post-stream judge evaluation | N/A (async) | 2s |

### Example: Document Processing (Tier 3, Non-Streaming)

| Component | Budget | Actual |
|-----------|--------|--------|
| Input guardrails | 100ms | 50ms |
| LLM generation (complete) | 10s | 8s |
| Output guardrails | 100ms | 60ms |
| Judge evaluation | 5s | 3s |
| **Total before delivery** | **15.2s** | **11.1s** |

### What Breaks the Budget

| Problem | Cause | Mitigation |
|---------|-------|-----------|
| Judge adds 5s to every request | Using large model for judge | Use smaller model (Haiku-class) for routine evaluation |
| Guardrail latency spikes | ML classifier cold start | Pre-warm classifiers, use rule-based for latency-critical path |
| Multiple judge calls per request | Evaluating multiple dimensions separately | Batch evaluations into a single prompt |
| Human review blocks delivery | Synchronous human review on all flags | Async review for medium flags; synchronous only for high/critical |

---

## Cost Optimisation

### Judge Model Selection

| Judge Model Tier | Cost (per 1K eval tokens) | Accuracy | When to Use |
|-----------------|--------------------------|----------|-------------|
| **Small** (Haiku, GPT-4o-mini) | ~$0.001 | 80–85% | Tier 1, high-volume screening |
| **Medium** (Sonnet, GPT-4o) | ~$0.01 | 88–93% | Tier 2, balanced cost/accuracy |
| **Large** (Opus, GPT-4) | ~$0.05 | 93–97% | Tier 3, consequential decisions |

### Tiered Evaluation

Run cheap evaluation first; escalate to expensive evaluation only when needed:

```
Request → Rule-based guardrails (free, fast)
  ↓ (passed)
Request → Small model judge (cheap, fast)
  ↓ (flagged or uncertain)
Request → Large model judge (expensive, accurate)
  ↓ (flagged)
Request → Human review (most expensive)
```

This reduces cost by 60–80% compared to running the large model on everything.

### Caching

Judge evaluations on identical or near-identical inputs can be cached:

| Cache Type | Hit Rate | Risk |
|-----------|----------|------|
| Exact match (same input hash) | Low (5–10%) | None |
| Semantic similarity (embedding distance < threshold) | Medium (15–30%) | Adversarial inputs designed to be semantically similar but functionally different |

**Only cache for Tier 1.** For Tier 2–3, the risk of cache-based bypass outweighs the cost saving.

---

## Budgeting Template

| Line Item | Monthly Estimate |
|-----------|-----------------|
| Generator LLM API costs | $ ___ |
| Input guardrails (if ML-based) | $ ___ |
| Output guardrails (if ML-based) | $ ___ |
| Judge LLM API costs (at sampling rate ___%) | $ ___ |
| Human review (estimated ___ reviews × $___/review) | $ ___ |
| Monitoring infrastructure (SIEM, dashboards) | $ ___ |
| **Total security overhead** | **$ ___** |
| **As % of generator cost** | **____%** |

**Rule of thumb:** Security overhead is typically 15–40% of generator cost for Tier 2, and 40–100% for Tier 3.

---

## Related

- [Controls](/core/controls.md) — The layers being costed
- [Judge Assurance](/core/judge-assurance.md) — Evaluating judge accuracy (affects model selection)
- [Streaming Controls](/core/streaming-controls.md) — Latency implications of streaming vs. non-streaming
- [Risk Tiers](/core/risk-tiers.md) — Tier determines control density and therefore cost
