# The Evidence Gap: What Research Actually Says About Runtime AI Security

*Where the science supports the controls — and where it doesn't yet*

---

Every framework makes a promise. This one promises that three-layer runtime behavioral security — guardrails, LLM-as-Judge, human oversight — is the answer to AI's non-determinism problem. The argument is intuitive, the architecture is clean, and the control domains map neatly to emerging standards.

But what does the research actually say?

This article examines the peer-reviewed evidence, industry data, and honest limitations behind each pillar of runtime AI security. The goal isn't to validate or undermine the framework. It's to ground it — to distinguish what's proven from what's promising, and what's promising from what's still hope.

---

## The Case for Runtime: Strong and Getting Stronger

The foundational claim — that design-time testing is insufficient for non-deterministic AI — is well supported. Carnegie Mellon's Software Engineering Institute [identifies the core problem clearly](https://www.sei.cmu.edu/blog/the-challenges-of-testing-in-a-non-deterministic-world/): traditional quality assurance assumes deterministic behaviour, where given input X you always get output Y. AI agents break this assumption entirely.

The challenge runs deeper than stochastic outputs. Even setting temperature to zero and using fixed random seeds cannot guarantee identical outputs across runs, due to hardware-level floating-point effects and token probability ties. The SIGPLAN research community [frames the shift directly](https://blog.sigplan.org/2025/03/20/testing-ai-software-isnt-like-testing-plain-old-software/): testing AI software isn't like testing traditional software because multiple outputs may be "correct," creating a flexible correctness paradigm that conventional test oracles cannot handle.

This makes the case for runtime monitoring real. If you cannot fully validate behaviour before deployment, you must observe it during deployment. The question is whether our monitoring tools are up to the job.

---

## Guardrails: Necessary, Brittle, Evolving

Research consistently confirms that guardrails are necessary — and consistently demonstrates that they are insufficient alone. An [ICLR 2025 paper on R2-Guard](https://proceedings.iclr.cc/paper_files/paper/2025/file/a07e87ecfa8a651d62257571669b0150-Paper-Conference.pdf) showed that state-of-the-art guardrail models like LlamaGuard, OpenAI's moderation API, and ToxicChat encode safety knowledge implicitly from annotated training data, causing them to overlook complex interrelationships among safety categories and leaving them vulnerable to adversarial jailbreaks.

The [2024 arXiv survey on LLM risks and guardrails](https://arxiv.org/abs/2406.12934) reinforced the tension: effective guardrail design requires deep understanding of use case, regulation, and ethics, while balancing competing requirements like accuracy and privacy remains an ongoing challenge with no general solution.

Three generations of guardrails have emerged:

| Generation | Era | Approach | Limitation |
|---|---|---|---|
| **Rule-based** | 2022–2023 | Keyword matching, regex, deny lists | Trivially bypassed with encoding or paraphrasing |
| **Jailbreak-aware** | 2023–2024 | Learned classifiers (Llama Guard etc.) | Misses novel attacks; brittle to distribution shift |
| **Contextual** | 2025+ | Full-conversation analysis, behavioural context | Higher latency; early-stage maturity |

The [ADL's 2025 safety research](https://www.adl.org/resources/report/safety-divide-open-source-ai-models-fall-short-guardrails-antisemitic-dangerous) found that open-source models can be easily manipulated to generate harmful content, even with guardrails in place, underscoring the gap between guardrail intent and guardrail effectiveness.

**What the evidence supports:** Guardrails are essential as a fast, first-pass filter. They catch known-bad patterns with low latency. But they are demonstrably insufficient for unknown-bad, semantic violations, or adversarial inputs.

**What it doesn't support:** Any claim that guardrails provide comprehensive protection, even for known attack categories. The attack surface evolves faster than deny lists.

---

## LLM-as-Judge: Promising, Biased, Domain-Limited

The LLM-as-Judge pattern — using a second model to evaluate the first — is one of the most actively researched topics in AI evaluation. The evidence is nuanced.

A [comprehensive 2024 survey on LLM-as-a-Judge](https://arxiv.org/abs/2411.15594) examined strategies to enhance reliability, including consistency improvements, bias mitigation, and adaptation to diverse scenarios. The researchers found that while LLM judges can achieve high agreement with human evaluators on general tasks, reliability degrades significantly in specific conditions.

The bias catalogue is extensive and empirically documented:

| Bias | Evidence | Impact |
|---|---|---|
| **Position bias** | [Shi et al., 2024](https://arxiv.org/abs/2406.07791) | Judges favour responses based on order in the prompt, modulated by model family and context window |
| **Verbosity bias** | Multiple studies, 2024–2025 | Judges prefer longer, more formal outputs regardless of substantive quality — an artefact of RLHF training |
| **Self-preference** | [CALM framework, 2024](https://arxiv.org/html/2410.02736v1) | Judges assign higher scores to outputs similar to their own generations (lower perplexity = higher rating) |
| **Expert domain gap** | [Szymanski et al., IUI 2025](https://dl.acm.org/doi/10.1145/3708359.3712091) | Agreement between LLM judges and domain experts ranges from 60–68% in specialised fields |

The domain limitation is particularly important for enterprise security. If an LLM-as-Judge evaluating financial advice agrees with human experts only 60–68% of the time, the assurance layer has a measurable, significant error floor. The [EMNLP 2025 findings on multilingual reliability](https://aclanthology.org/2025.findings-emnlp.587.pdf) add another dimension: LLM judges fail to maintain consistency across languages, which matters for global enterprise deployments.

Mitigation strategies exist — randomised response ordering, majority voting across multiple runs, chain-of-thought prompting — and [research shows](https://arxiv.org/abs/2411.15594) they measurably improve alignment. But they add latency and cost, and none eliminate the biases entirely.

**What the evidence supports:** LLM-as-Judge catches categories of failures that guardrails miss, particularly semantic policy violations and subtle behavioural drift. It is a meaningful assurance layer.

**What it doesn't support:** Treating Judge findings as authoritative without human review. The measured bias and domain gap mean the Judge is a signal source, not a decision-maker — which is precisely how this framework positions it, and the research validates that design choice.

---

## Human Oversight: The Accountability Anchor That Doesn't Scale

The third layer — human oversight — is where the framework meets its hardest constraint: human attention is finite, expensive, and cognitively unreliable at scale.

A [2024 study in Legal and Forensic Medicine](https://www.sciencedirect.com/science/article/pii/S1871678424005636) asked bluntly whether human oversight of AI systems is still possible, concluding that complete oversight may no longer be viable in certain contexts. The paper argued for strategic interventions leveraging human-AI collaboration rather than comprehensive human review.

The scalability numbers tell the story. Production-ready human-in-the-loop systems aim for a 10–15% escalation rate, with human review adding 0.5–2.0 seconds per decision. For high-throughput AI systems processing thousands of transactions per second, even a 1% escalation rate creates an unmanageable queue.

Worse, the quality of human oversight is compromised by well-documented cognitive biases:

- **Automation bias:** Humans systematically overtrust computer-generated outputs, a tendency documented even with simple algorithms. Explainability techniques, meant to help, can [paradoxically increase overreliance](https://pmc.ncbi.nlm.nih.gov/articles/PMC11976012/).
- **Organisational pressure:** A [JRC study](https://www.edps.europa.eu/data-protection/our-work/publications/techdispatch/2025-09-23-techdispatch-22025-human-oversight-automated-making_en) found that employees tended to prioritise company interests over fairness when reviewing AI outputs, regardless of whether those interests were stated explicitly.
- **The handoff problem:** Human factors research has shown that the "monitoring for rare events" scenario — where a human watches an automated system and intervenes when something goes wrong — actively degrades human performance. The 2018 Uber autonomous vehicle fatality remains a stark example.

**What the evidence supports:** Humans are essential for accountability, edge-case judgment, and regulatory compliance. The framework's positioning of humans as the decision-maker (not the detector) is sound.

**What it doesn't support:** The implicit assumption that human oversight scales linearly with system deployment. At enterprise volume, the bottleneck isn't whether humans can review — it's whether the review is meaningful or performative. Risk-tiered sampling rates help, but the gap between "human is accountable" and "human meaningfully reviewed" widens with scale.

---

## Prompt Injection: The Unsolved Foundation

Beneath the three-layer model lies an uncomfortable reality: prompt injection — ranked #1 in the [OWASP Top 10 for LLM Applications 2025](https://owasp.org/www-project-top-10-for-large-language-model-applications/) — remains a fundamentally unsolved problem.

OpenAI's Chief Information Security Officer Dane Stuckey has [openly acknowledged](https://versprite.com/blog/still-obedient-prompt-injection-in-llms-isnt-going-away-in-2025/) that prompt injection remains "a frontier, unsolved security problem." The reason is architectural: unlike traditional computing, where code and data occupy distinct memory spaces, LLMs process everything as a unified token stream. NVIDIA researchers describe this as "control-data plane confusion" — there is no principled way for current models to distinguish instructions from data.

The empirical evidence is stark. Researchers [tested 36 LLM-integrated applications and found 31 vulnerable](https://simonwillison.net/2025/Nov/2/new-prompt-injection-papers/) — an 86% failure rate. A study of [12 published defences subjected to adaptive attacks](https://simonwillison.net/2025/Nov/2/new-prompt-injection-papers/) found that a dedicated red team defeated all of them. In [medical AI simulations](https://pmc.ncbi.nlm.nih.gov/articles/PMC12717619/), prompt injection succeeded in 94.4% of trials, including 91.7% of extremely high-harm scenarios involving dangerous medications.

This doesn't invalidate layered defence. It contextualises it. The three-layer model is not a solution to prompt injection; it's a strategy for managing a risk that current AI architecture cannot eliminate. Defence-in-depth reduces the probability and blast radius of successful attacks. It doesn't prevent them.

Meta's ["Rule of Two"](https://simonwillison.net/2025/Nov/2/new-prompt-injection-papers/) offers a pragmatic design principle: never allow an LLM that processes untrusted input to also have direct access to sensitive actions. This infrastructure-level separation — which aligns with the framework's "infrastructure beats instructions" principle — is currently the most robust available mitigation.

---

## Multi-Agent Systems: Where Theory Outpaces Practice

The framework's MASO (Multi-Agent Security Operations) extension addresses a real and growing threat. The [Cooperative AI Foundation's 2025 report on multi-agent risks](https://arxiv.org/abs/2502.14143) identified three failure modes — miscoordination, conflict, and collusion — along with seven risk factors including emergent agency, network effects, and commitment problems.

The [ACM Computing Surveys](https://dl.acm.org/doi/10.1145/3716628) systematic review documented threats across six dimensions: perception, reasoning, action, agent-to-environment, agent-to-agent, and memory. Real-world exploits have already materialised: the [EchoLeak exploit (CVE-2025-32711)](https://arxiv.org/html/2510.23883v1) against Microsoft Copilot demonstrated how engineered email prompts could trigger automated sensitive data exfiltration without user interaction, receiving a CVSS score of 9.3.

The research on [open challenges in multi-agent security](https://arxiv.org/html/2505.02077v1) warns that novel threats emerge from agent interaction that cannot be addressed by securing individual agents in isolation — including steganographic collusion channels, cascading jailbreaks across agent boundaries, and coordinated adversarial behaviours that evade single-agent detection.

The framework's 93 MASO controls across six domains are directionally aligned with these identified threats. But honesty demands acknowledging that the empirical validation base for multi-agent security controls is thin. Most controls are derived from threat modelling and architectural reasoning, not from measured effectiveness in production deployments. The field is building the aeroplane in flight.

---

## RAG Security: The Evidence Backs the Alarm

One area where the framework's warnings are strongly validated by research is RAG (Retrieval-Augmented Generation) security. The attack surface is broad and empirically demonstrated.

[PoisonedRAG (USENIX Security 2025)](https://www.usenix.org/system/files/usenixsecurity25-zou-poisonedrag.pdf) showed that injecting just 5 malicious documents into a corpus of millions caused the target AI to return attacker-chosen false answers 90% of the time for triggered queries. [Machine Against the RAG (USENIX Security 2025)](https://www.usenix.org/system/files/usenixsecurity25-shafran.pdf) demonstrated retrieval jamming: an adversary with only query access and database insert permissions can create "blocker" documents that prevent the system from answering targeted questions at all.

The [formal threat model for RAG systems](https://arxiv.org/html/2509.20324v1) catalogues the full attack surface: corpus poisoning, indirect prompt injection, retrieval jamming, privacy leakage, membership inference, and access control bypass. Cutting-edge [2026 research on hybrid RAG](https://arxiv.org/html/2602.08668v2) introduces an entirely new class: retrieval pivot attacks that exploit vector-to-graph boundaries to leak data that neither vector search nor graph traversal would expose independently.

With [30–60% of enterprise AI use cases now relying on RAG architectures](https://arxiv.org/html/2505.08728v2), the framework's classification of RAG as the biggest attack surface is not alarmist — it's empirically grounded.

---

## The Adoption Reality: A Field in the Gap

The research paints a consistent picture of an industry caught between ambition and readiness:

| Metric | Source | Finding |
|---|---|---|
| AI adoption rate | [McKinsey 2024](https://www.pointguardai.com/blog/security-the-missing-link-in-enterprise-ai-adoption) | 90% of organisations implementing or planning LLM use cases |
| Security preparedness confidence | Same survey | Only 5% feel highly confident in AI security |
| AI-specific security controls | [BigID 2025](https://www.prnewswire.com/news-releases/new-study-reveals-major-gap-between-enterprise-ai-adoption-and-security-readiness-302469214.html) | 47% have no AI-specific security controls in place |
| Data pasted into AI tools | [Cyberhaven 2024](https://www.magicmirror.team/blog/latest-adoption-risk-and-governance-insights-in-enterprise-ai) | 485% increase between 2023 and 2024 |
| Advanced AI security strategy | [Stanford AI Index 2025](https://www.ciphernorth.com/blog/nist-ai-risk-management-framework-rmf) | Only 6% of organisations |
| Regulatory readiness | [BigID 2025](https://www.prnewswire.com/news-releases/new-study-reveals-major-gap-between-enterprise-ai-adoption-and-security-readiness-302469214.html) | 55% unprepared for AI regulatory compliance |

This gap is the strongest argument for a framework like this one. Not because every control is proven effective — some are, some aren't yet — but because the alternative (deploying with no structured controls) is demonstrably worse. The [SANS Institute's 2025 guidance](https://www.sans.org/blog/securing-ai-in-2025-a-risk-based-approach-to-ai-controls-and-governance) advocates a risk-based, incremental approach: deploy in less critical environments first, validate controls, then expand. This maps directly to the framework's risk-tiered model.

---

## The Regulatory Landscape: Demanding More Than Tools Can Deliver

Regulation is simultaneously validating the need for controls and outpacing the tooling available to implement them.

[NIST AI 600-1](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf), released in July 2024, translates the AI RMF into concrete actions for generative AI across twelve risk categories, from hallucinations to privacy leakage to harmful bias. The [EU AI Act's](https://digital-strategy.ec.europa.eu/en/policies/regulatory-framework-ai) phased implementation — prohibited practices from February 2025, GPAI model obligations from August 2025, full high-risk requirements by 2026–2027 — creates compliance pressure with timelines that enterprises are [struggling to meet](https://www.dlapiper.com/en/insights/publications/ai-outlook/2025/the-european-commission-considers-pause-on-ai-act-entry-into-application).

The [European Commission's November 2025 proposal](https://www.crowell.com/en/insights/client-alerts/eu-ai-act-gdpr-and-digital-laws-changes-proposed) to delay and simplify elements of the AI Act acknowledges the gap between regulatory ambition and practical readiness. Harmonised standards under CEN-CENELEC are delayed. Conformity assessment bodies are not yet appointed in many member states. [Over 40 leading European enterprises](https://symbio6.nl/en/blog/criticism-of-eu-ai-act) asked the Commission to pause upcoming requirements, while [privacy advocates warned](https://www.bsr.org/en/blog/the-eu-ai-act-where-do-we-stand-in-2025) that delays risk rolling back accountability safeguards.

The AI supply chain adds another dimension. Traditional SBOMs don't capture AI-specific risks. [AIBOM standards are emerging](https://noma.security/blog/securing-ai-systems-through-transparency-the-critical-role-of-ai-bills-of-materials/) — CycloneDX 1.6 added ML-BOM support in April 2024, SPDX 3.0 included AI profiles — but [adoption remains low](https://sdtimes.com/ai/from-sbom-to-ai-bom-rethinking-supply-chain-security-for-ai-native-software/), with 48% of security professionals admitting their organisations are falling behind even on basic SBOM requirements. [IBM's 2025 data](https://venturebeat.com/security/seven-steps-to-ai-supply-chain-visibility) showed that 97% of breached AI systems lacked proper access controls.

Frameworks like this one provide a bridge between regulatory requirements and implementation reality. The mappings to NIST, OWASP, ISO 42001, and the EU AI Act are valuable not because they guarantee compliance, but because they translate abstract obligations into operational controls that teams can actually deploy.

---

## What This Means: An Honest Assessment

The evidence supports this framework's core architecture more than it undermines it. But it also reveals uncomfortable truths that honest practitioners need to acknowledge:

**The research validates:**
- Non-deterministic AI requires runtime monitoring; design-time testing alone is insufficient
- Layered defence (guardrails + detection + human oversight) is more effective than any single control
- Risk-tiered approaches match the SANS, NIST, and emerging regulatory consensus
- Infrastructure-level enforcement is more reliable than instruction-level constraints
- RAG is a critical and empirically demonstrated attack surface
- Multi-agent systems introduce qualitatively new security challenges

**The research cautions:**
- Guardrails are demonstrably brittle against adaptive adversaries and novel attacks
- LLM-as-Judge carries measurable biases (position, verbosity, self-preference) and a 32–40% disagreement rate with domain experts in specialised fields
- Human oversight suffers from automation bias, organisational pressure, and fundamental scalability limits
- Prompt injection remains architecturally unsolved — all current defences are mitigations, not solutions
- Multi-agent security controls are theoretically grounded but empirically unvalidated at scale
- Regulatory timelines are outpacing available tooling and standardisation

**The honest position is this:** runtime behavioral security is the best available approach to a problem that doesn't yet have a complete solution. The framework provides structured risk management for AI systems in a period where most organisations have no structured approach at all. That's not the same as claiming the controls are proven effective. It's claiming they're the most defensible bet given current evidence — and that's a claim the research supports.

---

## Key Takeaways

1. **The case for runtime monitoring over design-time-only testing is empirically strong.** Non-determinism, flexible correctness, and emergent behaviour make pre-deployment validation insufficient. This is well-established in the literature.

2. **Each control layer has measured limitations.** Guardrails miss novel attacks. LLM-as-Judge carries systematic biases. Human oversight doesn't scale. No single layer is sufficient — but the combination is stronger than any alternative documented in the research.

3. **Prompt injection remains the bedrock unsolved problem.** All runtime controls operate on a foundation where the instruction-data boundary doesn't exist. Defence-in-depth reduces probability and blast radius, but cannot prevent exploitation entirely.

4. **The adoption gap is the most urgent finding.** With 47% of organisations having no AI-specific controls and only 6% having advanced strategies, the framework's value lies less in perfection and more in providing structured, risk-proportionate security where most have none.

5. **Multi-agent security is building ahead of evidence.** The MASO controls address real, documented threats but lack large-scale empirical validation. Organisations adopting these controls should treat them as best-available guidance, not proven standards, and invest in measuring their effectiveness.

6. **Regulation validates the direction but outpaces the tooling.** NIST, the EU AI Act, and ISO 42001 all point toward the controls this framework implements. The gap between regulatory demand and implementation readiness is the industry's central challenge for 2025–2027.

---

## Related

- [Why Your AI Guardrails Aren't Enough](why-guardrails-arent-enough.md) — The three-layer model in detail
- [The Judge Detects. It Doesn't Decide.](judge-detects-not-decides.md) — Why async evaluation, not blocking
- [The Verification Gap](the-verification-gap.md) — Confirming ground truth for AI outputs
- [When Agents Talk to Agents](when-agents-talk-to-agents.md) — Multi-agent accountability challenges
- [RAG Is Your Biggest Attack Surface](rag-is-your-biggest-attack-surface.md) — Retrieval pipeline risks
- [The Supply Chain Problem](the-supply-chain-problem.md) — Model provenance and AIBOM
- [Humans Remain Accountable](humans-remain-accountable.md) — The human oversight principle
- [Risk Tiers](../core/risk-tiers.md) — Risk-proportionate control allocation

---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
