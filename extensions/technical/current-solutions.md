# Current AI Safety Solutions

A reference guide to production-ready guardrail, evaluation, and safety solutions implementing the three-layer pattern.

---

## Solutions At a Glance

| Solution | Type | What It Does | Layer | Open Source | Key Limitation |
|----------|------|--------------|-------|-------------|----------------|
| **AWS Bedrock Guardrails** | Managed | Content filtering, PII detection, hallucination checks, denied topics | Guardrails | No | 30 denied topic limit; cross-region IAM issues |
| **Azure AI Content Safety** | Managed | Harm classification (0-7 severity), prompt shields, groundedness | Guardrails | No | English-optimized; 10K char limit per request |
| **NVIDIA NeMo Guardrails** | Framework | Programmable rails (input/output/dialog/retrieval/execution) | Guardrails | Yes | Dialog rails don't work with reasoning models |
| **Guardrails AI** | Framework | Output validation, structured output enforcement, retry logic | Guardrails | Yes | Output-focused; less input validation |
| **Llama Guard 3/4** | Model | LLM-based content classification (safe/unsafe + category) | Guardrails/Judge | Yes | ~33% attack bypass rate; English-optimized |
| **OpenAI Moderation API** | API | Harm classification across categories | Guardrails | No | OpenAI models only; limited customization |
| **DeepEval** | Framework | LLM-as-judge evaluation, 50+ metrics, CI/CD integration | Judge | Yes | LLM calls add cost/latency at scale |
| **Galileo** | Platform | Eval-to-guardrail lifecycle, Luna models for monitoring | Judge | No | Platform dependency |
| **Prompt Guard (Meta)** | Model | Prompt injection and jailbreak detection | Guardrails | Yes | Needs fine-tuning for best results |
| **LlamaFirewall (Meta)** | Tool | Security guardrail for AI systems | Guardrails | Yes | Early stage |

---

## Solutions by Use Case

| If You Need... | Primary Choice | Alternative |
|----------------|----------------|-------------|
| Turnkey AWS guardrails | AWS Bedrock Guardrails | — |
| Turnkey Azure guardrails | Azure AI Content Safety | — |
| Self-hosted, customizable | NVIDIA NeMo Guardrails | Guardrails AI |
| Open-source safety model | Llama Guard 3/4 | Prompt Guard |
| LLM evaluation/testing | DeepEval | Galileo |
| Production monitoring | Confident AI (DeepEval) | Galileo |
| Structured output validation | Guardrails AI | NeMo Guardrails |
| Multimodal content safety | Azure AI Content Safety | Llama Guard 4 |
| Hallucination detection | AWS Bedrock (Automated Reasoning) | DeepEval metrics |

---

## Solutions by Layer

### Guardrails Layer (Real-time, ~10-100ms)

| Solution | Input | Output | Multimodal | Customizable | Self-Hosted |
|----------|-------|--------|------------|--------------|-------------|
| AWS Bedrock Guardrails | ✓ | ✓ | Images (preview) | Limited | No |
| Azure AI Content Safety | ✓ | ✓ | ✓ | Custom categories | No |
| NVIDIA NeMo Guardrails | ✓ | ✓ | Limited | Highly | Yes |
| Guardrails AI | Limited | ✓ | No | Highly | Yes |
| Llama Guard | ✓ | ✓ | Llama Guard 4 | Via prompting | Yes |
| OpenAI Moderation | ✓ | ✓ | No | No | No |

### Judge Layer (Async, ~500ms-5s)

| Solution | Metrics | Custom Criteria | Production Monitoring | CI/CD |
|----------|---------|-----------------|----------------------|-------|
| DeepEval | 50+ | G-Eval, DAG | Via Confident AI | ✓ |
| Galileo | Multiple | ✓ | Built-in | ✓ |
| Custom LLM prompts | Unlimited | ✓ | DIY | DIY |

---

## Industry Context

The AI security industry has converged on a common pattern: **layered runtime controls** combining fast filtering (guardrails), deeper evaluation (LLM-as-judge), and human oversight. This guide catalogs the major solutions implementing this pattern, with honest assessments of capabilities, limitations, and appropriate use cases.

> This page exists to give credit where it's due and help practitioners select appropriate tools. The Framework synthesizes and explains the pattern these solutions implement.

---

## Quick Reference: Solution Categories

| Category | Purpose | Examples |
|----------|---------|----------|
| **Platform Guardrails** | Cloud-native filtering integrated with AI services | AWS Bedrock Guardrails, Azure AI Content Safety |
| **Open-Source Frameworks** | Self-hosted, customizable guardrail systems | NVIDIA NeMo Guardrails, Guardrails AI |
| **Safety Models** | LLM-based content moderation | Llama Guard, OpenAI Moderation API |
| **Evaluation Frameworks** | LLM-as-Judge implementation | DeepEval, Galileo |
| **Standards & Guidance** | Risk frameworks and taxonomies | OWASP LLM Top 10, NIST AI RMF |

---

## Platform Guardrails

### AWS Bedrock Guardrails

**Overview:** Managed guardrail service integrated with Amazon Bedrock foundation models. Provides content filtering, PII detection, denied topics, and (uniquely) automated reasoning checks for hallucination detection.

**How It Works:**
- Evaluates both user inputs and model responses against configured policies
- Six safeguard types: content filters, denied topics, word filters, sensitive info, contextual grounding, automated reasoning
- Can be used via API without invoking the model (ApplyGuardrail API)
- Works with any model (Bedrock-hosted or external via API)

**Strengths:**
- Automated Reasoning checks claim 99% accuracy for hallucination detection (AWS claim)
- Blocks up to 88% of harmful content (AWS benchmark)
- Native integration with Bedrock agents, knowledge bases, and flows
- Cross-model consistency — same guardrails work across different FMs

**Limitations:**
- **Cross-region complexity:** Known IAM permission issues when guardrails and agents are in different regions
- **Input tagging limitations:** Not currently supported with managed prompts
- **Latency cost:** Adds processing time; charges apply even when blocking input
- **30 denied topic limit:** May be insufficient for complex policy sets

**Known Issues:**
- Access denied errors when using cross-region guardrails with Bedrock Agents (requires careful IAM configuration)
- VPC endpoint limitations for cross-region access
- Streaming not fully supported with all guardrail configurations

**Best For:** Organizations already using AWS Bedrock who want turnkey guardrails with minimal setup.

**Not Recommended For:** Complex multi-region deployments without careful IAM planning; use cases requiring more than 30 denied topics.

**Pricing:** Per 1,000 text units (1,000 characters each). Word filters free. See [AWS Pricing](https://aws.amazon.com/bedrock/pricing/).

**Documentation:** [AWS Bedrock Guardrails](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails.html)

---

### Azure AI Content Safety

**Overview:** Microsoft's content moderation service providing text and image analysis with severity scoring across harm categories.

**How It Works:**
- Multi-class classification for hate, violence, sexual content, self-harm
- Severity levels 0-7 for text, 0-3 for images
- Prompt Shields for jailbreak and injection detection
- Groundedness detection for hallucination
- Protected material detection for copyright

**Strengths:**
- Multimodal support (text, images, text+image)
- Granular severity scoring (not just binary)
- Custom categories API for domain-specific content
- Integration with Azure OpenAI and Foundry
- Protected material detection for copyright compliance

**Limitations:**
- **Language support:** Optimized for English; performance varies for other languages (German, Japanese, Spanish, French, Italian, Portuguese, Chinese supported)
- **10K character limit:** Per submission for text moderation
- **Image recognition limits:** May miss content in unclear or edited images
- **Cannot detect CSAM:** Explicitly stated limitation
- **Evolving threats:** May not keep pace with new attack techniques

**Known Issues:**
- False positives reported in scientific/medical contexts (pharmaceutical companies report legitimate content being flagged)
- Groundedness detection inconsistencies (some users report it returns empty results)
- Content filter token costs can be significant (reported 10x other costs in some deployments)

**Best For:** Microsoft Azure customers needing content moderation with severity scoring and multimodal support.

**Not Recommended For:** Non-English content at scale; scientific/medical applications without custom configuration.

**Pricing:** Per text record (1,000 characters) and per image. See [Azure Pricing](https://azure.microsoft.com/pricing/details/cognitive-services/content-safety/).

**Documentation:** [Azure AI Content Safety](https://learn.microsoft.com/azure/ai-services/content-safety/)

---

## Open-Source Frameworks

### NVIDIA NeMo Guardrails

**Overview:** Open-source Python library for adding programmable guardrails to LLM applications. Highly customizable with support for multiple rail types and integration with major LLM providers.

**How It Works:**
- Five rail types: input, dialog, retrieval, execution, output
- Colang 2.0 DSL for defining conversational flows
- Can orchestrate multiple rails with configurable execution order
- Supports GPU acceleration for low-latency performance

**Strengths:**
- **Highly programmable:** Colang DSL allows complex policy logic
- **Multi-rail orchestration:** Coordinate input, dialog, retrieval, execution, and output rails
- **LLM provider agnostic:** Works with OpenAI, Azure, Anthropic, HuggingFace, NIM
- **LangChain/LangGraph integration:** Native support for popular frameworks
- **GPU acceleration:** NVIDIA hardware optimization for performance

**Limitations:**
- **Learning curve:** Colang DSL requires learning
- **LLM dependency:** Most rails require an LLM for evaluation (adds latency/cost)
- **Dialog rails not supported with reasoning models:** Documented limitation
- **Built-in rails may not suit production:** NVIDIA explicitly states "may or may not be suitable for a given production use case"

**Known Issues:**
- Jailbreak detection container setup issues reported (GitHub Issue #690)
- Reasoning traces can interfere with guardrails, triggering false positives
- Threads not supported in streaming mode
- No automatic thread cleanup mechanism

**Vendor Recommendation:** NVIDIA states developers should "work with their internal application team to ensure guardrails meets [their] requirements" — tune for your use case.

**Best For:** Teams needing highly customizable, self-hosted guardrails with complex policy logic.

**Not Recommended For:** Simple use cases where managed services suffice; teams without Python/ML expertise.

**License:** Apache 2.0

**Documentation:** [NeMo Guardrails Docs](https://docs.nvidia.com/nemo/guardrails/)

**GitHub:** [github.com/NVIDIA/NeMo-Guardrails](https://github.com/NVIDIA/NeMo-Guardrails)

---

### Guardrails AI

**Overview:** Open-source Python framework for adding structural and semantic validation to LLM outputs. Focus on output validation with a library of reusable validators.

**How It Works:**
- Define "guards" that validate LLM outputs
- Validator library (Guardrails Hub) with pre-built checks
- Supports structured output validation (JSON, etc.)
- Can retry/reask on validation failure

**Strengths:**
- **Validator ecosystem:** Large library of pre-built validators
- **Structured output focus:** Strong at ensuring output format compliance
- **Retry logic:** Automatic correction on validation failure
- **Simple API:** Easy to integrate

**Limitations:**
- **Output-focused:** Less comprehensive for input validation
- **LLM dependency:** Many validators require LLM calls
- **Limited multimodal:** Primarily text-focused

**Best For:** Applications requiring structured LLM outputs with validation; RAG pipelines needing output quality checks.

**License:** Apache 2.0

**Documentation:** [guardrailsai.com](https://www.guardrailsai.com/)

**GitHub:** [github.com/guardrails-ai/guardrails](https://github.com/guardrails-ai/guardrails)

---

## Safety Models

### Meta Llama Guard

**Overview:** LLM-based input/output moderation model from Meta, fine-tuned for safety classification. Available in multiple versions (Llama Guard 1, 2, 3, 4) with evolving capabilities.

**How It Works:**
- Fine-tuned Llama model that classifies content as safe/unsafe
- Outputs category of violation when unsafe
- Instruction-tunable — can adapt to custom taxonomies via prompting
- Available in quantized versions for lower deployment cost

**Versions:**
| Version | Base Model | Languages | Categories |
|---------|------------|-----------|------------|
| Llama Guard 3 | Llama 3 | 8 languages | 14 (MLCommons taxonomy) |
| Llama Guard 4 | Llama 4 Scout (12B) | Multilingual | MLCommons + custom |

**Strengths:**
- **Open weights:** Self-hostable, customizable
- **Instruction-tunable:** Adapt to custom policies via prompting
- **MLCommons aligned:** Standard taxonomy for interoperability
- **Multilingual:** Llama Guard 3+ supports 8 languages
- **Tool use awareness:** Can detect code interpreter abuse

**Limitations:**
- **English-optimized:** Performance varies in other languages
- **Context sensitivity:** May flag therapeutic discussions of self-harm
- **Adversarial vulnerability:** As an LLM, susceptible to prompt injection
- **False positive rate:** May increase refusals to benign prompts
- **Attack bypass rate:** Independent testing shows ~33% of attacks bypass protection

**Known Issues:**
- Llama Guard is an LLM and can be prompted to generate any text (not just classifications)
- Performance on custom taxonomies requires fine-tuning for best results
- Longer context windows can reduce guardrail effectiveness

**Meta's Recommendation:** "There is no one-size-fits-all guardrail detection to prevent all risks. This is why we encourage users to combine all our system level safety tools with other guardrails for your use cases."

**Best For:** Organizations wanting self-hosted safety classification with customization capability.

**Not Recommended For:** Production use without additional guardrail layers; non-English deployments without testing.

**License:** Llama Community License (requires "Built with Llama" attribution)

**Documentation:** [Llama Protections](https://www.llama.com/llama-protections/)

**Models:** [Llama Guard 3 on HuggingFace](https://huggingface.co/meta-llama/Llama-Guard-3-8B)

---

### OpenAI Moderation API

**Overview:** OpenAI's content moderation endpoint for detecting harmful content in text.

**How It Works:**
- API endpoint that classifies text across harm categories
- Returns category flags and confidence scores
- Free to use for OpenAI API customers

**Strengths:**
- **Free:** No additional cost for OpenAI customers
- **Simple API:** Single endpoint, easy integration
- **Fast:** Low latency classification

**Limitations:**
- **OpenAI ecosystem only:** Designed for OpenAI models
- **Text only:** No multimodal support
- **Limited customization:** Cannot adapt categories
- **English-focused:** Performance varies in other languages

**Best For:** Quick content filtering for OpenAI-based applications.

**Documentation:** [OpenAI Moderation](https://platform.openai.com/docs/guides/moderation)

---

## Evaluation Frameworks (LLM-as-Judge)

### DeepEval / Confident AI

**Overview:** Open-source LLM evaluation framework providing pytest-like testing for LLM applications. Supports both development-time benchmarking and production monitoring.

**How It Works:**
- Define test cases with inputs, outputs, and expected behaviors
- Run metrics (G-Eval, hallucination, relevancy, etc.) against outputs
- LLM-as-judge approach for most metrics
- Integrates with CI/CD pipelines
- Confident AI cloud platform for collaboration and monitoring

**Strengths:**
- **Comprehensive metrics:** 50+ research-backed evaluation metrics
- **Flexible:** Works with any LLM provider
- **Production monitoring:** Async evals in production via Confident AI
- **Red teaming:** Built-in adversarial testing for 40+ vulnerabilities
- **Component-level evals:** Can evaluate individual pipeline components

**Limitations:**
- **LLM dependency:** Most metrics require LLM calls (cost, latency)
- **Rate limits:** Can hit LLM provider limits during large evaluations
- **Metric-outcome fit:** Metrics may not correlate with business outcomes without calibration

**Known Issues:**
- Rate limit errors common during large evaluations
- False positives/negatives require metric tuning
- Production evals need async architecture to avoid blocking

**Best For:** Teams needing comprehensive LLM evaluation with CI/CD integration.

**License:** Apache 2.0 (open-source); Confident AI platform has free and paid tiers

**Documentation:** [deepeval.com](https://deepeval.com/)

**GitHub:** [github.com/confident-ai/deepeval](https://github.com/confident-ai/deepeval)

---

### Galileo

**Overview:** LLM evaluation platform with "eval-to-guardrail" lifecycle — evaluations developed in testing become production guardrails.

**How It Works:**
- Define evaluation criteria during development
- Test against datasets to calibrate
- Deploy same evals as production guardrails
- Luna models provide low-cost monitoring

**Strengths:**
- **Unified lifecycle:** Evals → Guardrails workflow
- **Low-cost monitoring:** Luna models for production
- **Observability:** Built-in tracing and debugging

**Limitations:**
- **Platform dependency:** Requires Galileo platform
- **Proprietary:** Less flexibility than open-source options

**Best For:** Teams wanting integrated eval-to-production workflow.

**Documentation:** [rungalileo.io](https://www.rungalileo.io/)

---

## Standards and Guidance

### OWASP LLM Top 10 (2025)

**Overview:** Industry-standard taxonomy of security risks for LLM applications, maintained by OWASP with 500+ contributors.

**Categories:**
1. Prompt Injection
2. Insecure Output Handling
3. Training Data Poisoning
4. Model Denial of Service
5. Supply Chain Vulnerabilities
6. Sensitive Information Disclosure
7. Insecure Plugin Design
8. Excessive Agency
9. Overreliance
10. Model Theft

**Also Available:** OWASP Top 10 for Agentic Applications (December 2025)

**Use For:** Risk identification, security assessments, compliance documentation.

**Documentation:** [owasp.org/www-project-top-10-for-large-language-model-applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/)

---

### NIST AI Risk Management Framework

**Overview:** US government framework for AI risk management with four core functions: Govern, Map, Measure, Manage.

**Use For:** Enterprise AI governance, federal compliance, risk assessment structure.

**Documentation:** [nist.gov/itl/ai-risk-management-framework](https://www.nist.gov/itl/ai-risk-management-framework)

---

### ISO 42001

**Overview:** International standard for AI management systems. Certifiable.

**Use For:** Formal AI governance certification, enterprise compliance.

**Documentation:** [iso.org/standard/81230.html](https://www.iso.org/standard/81230.html)

---

## Emerging Solutions

### LlamaFirewall (Meta)

Security guardrail tool for building secure AI systems. Part of Meta's Llama Protections suite.

### Prompt Guard (Meta)

Multi-label classifier for detecting prompt injections and jailbreaks. Available in 86M and 22M parameter versions.

### CyberSecEval (Meta)

Benchmarks for measuring LLM cybersecurity risks and defensive capabilities.

### Lasso Security Secure Gateway

Model-agnostic security gateway providing guardrails across any AI platform.

---

## Solution Selection Guide

| If You Need... | Consider |
|----------------|----------|
| Turnkey AWS integration | AWS Bedrock Guardrails |
| Turnkey Azure integration | Azure AI Content Safety |
| Highly customizable, self-hosted | NVIDIA NeMo Guardrails |
| Output validation focus | Guardrails AI |
| Self-hosted safety model | Llama Guard |
| LLM evaluation/testing | DeepEval |
| Integrated eval-to-production | Galileo |
| Risk taxonomy | OWASP LLM Top 10 |
| Governance framework | NIST AI RMF, ISO 42001 |

---

## Common Limitations Across Solutions

| Limitation | Description | Mitigation |
|------------|-------------|------------|
| **Language coverage** | Most optimized for English | Test non-English thoroughly; consider translation layers |
| **Novel attacks** | Pattern-based detection misses new techniques | Combine with behavioral monitoring; update regularly |
| **False positives** | Over-blocking legitimate content | Tune thresholds; allow human override |
| **Latency/cost** | LLM-based evaluation adds overhead | Tier your evaluation; sample at scale |
| **Context sensitivity** | May misclassify domain-specific content | Custom fine-tuning; domain-specific rules |
| **Adversarial vulnerability** | LLM-based guards can be attacked | Defense in depth; multiple layers |

---

## Implementation Recommendations

1. **Layer your defenses:** No single solution catches everything. Combine fast guardrails + LLM evaluation + human oversight.

2. **Start simple:** Begin with platform-native guardrails before custom solutions.

3. **Test in your domain:** Published benchmarks may not reflect your use case. Measure performance on your data.

4. **Plan for false positives:** Overly aggressive guardrails harm user experience. Build in human override paths.

5. **Budget for evaluation:** LLM-as-judge has real costs. Factor into architecture decisions.

6. **Update continuously:** Attacks evolve. Guardrails need regular updates.

---

## Credits and Acknowledgments

This guide synthesizes publicly available documentation, research, and community feedback. Credit to:

- **NVIDIA** — NeMo Guardrails and documentation
- **Meta** — Llama Guard, Prompt Guard, and Llama Protections ecosystem
- **AWS** — Bedrock Guardrails documentation and best practices
- **Microsoft** — Azure AI Content Safety transparency notes
- **Confident AI** — DeepEval framework and documentation
- **OWASP** — LLM Top 10 and community contributions
- **NIST** — AI Risk Management Framework
- **The broader AI safety research community**

---

*Last updated: February 2026*

*This page aims to provide accurate, helpful information. Solutions evolve rapidly — verify current capabilities with vendor documentation.*
