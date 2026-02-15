# Future Considerations

**What this framework acknowledges but doesn't yet address — and why.**

---

## Context

The 2026 International AI Safety Report, authored by over 100 experts across 30+ countries, provides the most comprehensive evidence base to date on general-purpose AI capabilities, risks, and risk management. Its core findings — defence-in-depth, the evaluation gap, the inadequacy of any single safeguard — validate the three-layer pattern this framework implements.

But the Bengio report is deliberately broad. It covers the full lifecycle of general-purpose AI, from training through societal impact. This framework is deliberately narrow: it covers **what happens in production** for organisations deploying AI systems.

That scoping is intentional. This framework will not attempt to address every AI risk. Some problems require policy, not controls. Some require research, not checklists. Some are simply outside the scope of what an enterprise security framework can usefully influence.

What follows are areas surfaced by the report and by operational experience that may inform future releases — or may remain permanently out of scope.

---

## Under Active Consideration

These topics are likely to be addressed in future iterations of this framework.

### Open-Weight Deployment Controls

Organisations deploying self-hosted open-weight models inherit control responsibilities that would otherwise sit with the model provider — input/output filtering, safeguard maintenance, abuse monitoring, and more. The current framework doesn't differentiate control requirements by deployment model. It should. See [Open-Weight Models Shift the Burden](../insights/open-weight-models-shift-the-burden.md) for the argument and interim guidance.

### Adversarial Robustness of the Judge Layer

The Judge layer is itself an LLM, and LLMs are susceptible to manipulation. The Bengio report documents models distinguishing between evaluation and deployment contexts — a capability that, if directed at the Judge, could undermine the entire detection layer. The Judge needs its own threat model. See [When the Judge Can Be Fooled](when-the-judge-can-be-fooled.md) for analysis and mitigations.

### Small Model and Edge Deployments

Small language models running on desktops, mobile devices, and edge infrastructure create a control environment that this framework doesn't yet address. Compute constraints limit what guardrails can run locally. Network constraints may prevent Judge evaluation entirely. Observability tooling may not exist for the deployment platform.

These models are proliferating — embedded in productivity tools, developer environments, and device-level assistants. They're often below the visibility threshold of enterprise security teams, yet they process real data and influence real decisions.

A future iteration of this framework will need to consider how to apply proportionate controls to systems where traditional infrastructure enforcement may not be available. The principles of the three-layer pattern still hold; the implementation mechanisms will be different.

---

## Acknowledged but Deferred

These are important topics that fall outside the practical scope of an enterprise deployment security framework.

### Training-Time Safety

The Bengio report identifies three layers of defence: building safer models during training, adding controls at deployment, and monitoring after deployment. This framework addresses the second and third layers. Training-time safety — RLHF, constitutional AI, safety fine-tuning — is the responsibility of model developers, not deployers.

Deployers should demand transparency from providers about training-time safety measures and should verify that safety behaviours survive fine-tuning and customisation. But specifying how models should be trained is outside this framework's scope and competence.

### Evaluation Gap

Pre-deployment tests do not reliably predict real-world performance. The Bengio report characterises this as one of the defining challenges of AI risk management. This framework implicitly addresses the evaluation gap by emphasising runtime monitoring over pre-deployment assurance — but it doesn't provide guidance on pre-deployment evaluation itself.

Organisations should treat pre-deployment evaluations as necessary but insufficient, and invest proportionally in the runtime controls this framework describes.

### Societal and Systemic Risks

Labour market disruption, threats to human autonomy, concentration of power, and other systemic risks raised by the Bengio report are policy challenges, not control implementation challenges. This framework has nothing useful to add on these topics.

### CBRN Risks

The report documents concerns about AI systems assisting in biological, chemical, radiological, and nuclear weapons development. For organisations in relevant sectors (pharmaceuticals, defence, research), these risks should inform risk-tier classification and may warrant domain-specific controls beyond what this framework provides. For general enterprise deployment, these risks are managed at the model provider level and through sector-specific regulation.

---

## Principles That Won't Change

Regardless of which topics enter scope in future releases, the framework's core principles remain stable:

- **Runtime behavioral monitoring over design-time assurance.** You can't fully test a non-deterministic system before deployment.
- **Infrastructure enforcement over prompt-based controls.** Controls that can be bypassed through conversation aren't controls.
- **Defence-in-depth through complementary layers.** No single layer is sufficient. The value is in the combination.
- **Proportionality to risk.** Not every system needs every control. Risk tiers exist for a reason.
- **Practical over theoretical.** If it can't be implemented by an engineering team with a deadline, it doesn't belong in this framework.

---

*This document will be updated as the framework evolves. Feedback on prioritisation is welcome — see [CONTRIBUTING.md](../CONTRIBUTING.md).*
