# Open-Weight Models Shift the Burden

**When you self-host a model, you inherit the controls the provider would have applied. Most organisations don't realise this until something goes wrong.**

---

## The Deployment Model Changes the Control Surface

This framework classifies AI systems by use case, not by technology. That's deliberate — [Risk Tier Is Use Case, Not Technology](../insights/risk-tier-is-use-case.md) explains why.

But there's a second dimension that matters: **how the model is deployed.**

An organisation calling GPT-4o or Claude via API operates in a fundamentally different security context than one running Llama 3 or Mistral locally. Both might power the same use case. Both sit at the same risk tier. But the control responsibilities are distributed differently.

| Responsibility | Hosted API (e.g. AWS Bedrock, Azure OpenAI) | Self-Hosted Open-Weight |
| --- | --- | --- |
| Input/output filtering | Provider + Deployer | **Deployer only** |
| Model patching and updates | Provider | **Deployer** |
| Safeguard integrity | Provider maintains; deployer configures | **Deployer must verify — or build from scratch** |
| Abuse monitoring | Provider contributes | **Deployer only** |
| Incident response (model-level) | Shared | **Deployer only** |
| Recall capability | Provider can deprecate or patch | **Not possible — weights are permanent** |

The 2026 International AI Safety Report makes this point clearly: open-weight models cannot be recalled once released, their safeguards are easier to remove, and actors can use them outside monitored environments. This isn't a theoretical concern — it's the operating reality for any enterprise deploying open-weight models for cost, latency, or data sovereignty reasons.

---

## What This Means for the Three-Layer Pattern

The three-layer model — Guardrails, Judge, Human Oversight — applies regardless of deployment model. But the **implementation burden shifts** when you self-host.

### Guardrails

With a hosted API, the provider typically applies baseline content filtering before your guardrails even execute. AWS Bedrock Guardrails, Azure AI Content Safety, and similar services add a layer you didn't build and don't maintain.

With open-weight models, **your guardrails are the only guardrails.** There is no provider-side safety net. If you haven't implemented input validation, output filtering, and topic restriction at the infrastructure layer, nothing else will do it for you.

Fine-tuned or quantised variants add another complication. Safety training applied during the model's original post-training can degrade or disappear entirely after fine-tuning. A model that refused harmful requests in its base form may comply after domain-specific fine-tuning — not because you intended it to, but because the safety behaviour wasn't robust to the training process.

### LLM-as-Judge

The Judge layer is less affected by deployment model, since the evaluating LLM can be a separate hosted service regardless of what the primary model is. This is actually a useful architectural pattern: run your primary workload on a self-hosted model for sovereignty or cost, but route evaluation through a hosted Judge with its own provider-maintained safeguards.

However, if you're running the Judge on a self-hosted model too (for the same sovereignty reasons), then the same safeguard concerns apply. An open-weight Judge with degraded safety alignment is worse than no Judge at all — it provides false assurance.

### Human Oversight

Human oversight requirements don't change based on deployment model. But the **information available to human reviewers does.** Hosted APIs often provide usage dashboards, content filtering logs, and abuse detection signals as part of the service. Self-hosted deployments only surface what you've instrumented.

If you haven't built the observability layer, your human reviewers are flying blind.

---

## Control Adjustments for Open-Weight Deployments

Organisations deploying open-weight models should apply these additional controls on top of their risk-tier requirements:

| Control | Rationale |
| --- | --- |
| **Safeguard validation on deployment** | Verify safety behaviours after any fine-tuning, quantisation, or format conversion. Don't assume base-model safety properties survive. |
| **Independent guardrail layer** | Deploy guardrails as a separate service, not as prompt instructions in the model. [Infrastructure Beats Instructions](../insights/infrastructure-beats-instructions.md) applies with extra force here. |
| **Model provenance tracking** | Document the exact model version, source, and any modifications. Open-weight supply chains are opaque — know what you're running. |
| **Periodic re-evaluation** | No provider is pushing safety patches to your deployment. Schedule regular red-teaming and evaluation cycles. |
| **Egress controls and sandboxing** | Self-hosted models with tool access need network-level restrictions. The model can't phone home for safety updates — your infrastructure must enforce boundaries. |
| **Judge independence** | If possible, use a separate model (or provider) for the Judge layer. Avoid evaluating a model's outputs with the same model. |

---

## The Sovereignty Trade-Off

Many organisations choose open-weight models for legitimate reasons: data residency requirements, latency constraints, cost at scale, or regulatory mandates that prohibit sending data to third-party APIs.

These are valid drivers. But they come with a security trade-off that should be made explicitly, not discovered in a post-incident review.

**The question isn't whether to use open-weight models. It's whether you've resourced the controls that the provider would otherwise have maintained.**

If you're deploying open-weight models at a higher risk tier, your control budget — people, tooling, monitoring — needs to reflect the additional responsibilities you've taken on. A Tier 3 use case on a hosted API and the same use case on a self-hosted open-weight model are not equivalent from a control implementation perspective, even though they sit at the same risk tier.

---

## Scope Note

This framework does not currently prescribe different control sets per deployment model. That may change in a future release as open-weight deployment patterns mature and the control evidence base grows. For now, treat the controls in this section as supplementary guidance for self-hosted deployments — applied on top of your existing risk-tier requirements.

Desktop-scale and edge deployments of small models introduce additional considerations (limited compute for guardrails, no network access for Judge evaluation, constrained observability). These are acknowledged but deferred to a future iteration of the framework.

---

*Related: [Infrastructure Beats Instructions](../insights/infrastructure-beats-instructions.md) · [Risk Tier Is Use Case, Not Technology](../insights/risk-tier-is-use-case.md) · [Controls](controls.md)*
