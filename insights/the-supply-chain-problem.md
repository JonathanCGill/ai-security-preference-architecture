# The Supply Chain Problem

## You Don't Control the Model You Deploy

Every enterprise AI system depends on components you didn't build:

- **Foundation models** from OpenAI, Anthropic, Google, Meta
- **Open-source models** downloaded from Hugging Face, Ollama, or similar
- **Frameworks** like LangChain, LlamaIndex, CrewAI, AutoGen
- **Embedding models** that encode your proprietary data
- **Vector databases** that store and retrieve that data

You test your application. You monitor your outputs. But you have no visibility into whether the model you're calling today is the same model you evaluated last month.

---

## The Risks Are Not Hypothetical

| Risk | Example |
|------|---------|
| **Model update without notice** | Provider updates weights or system prompt; your evaluated baseline is invalid |
| **Dependency compromise** | Malicious package in your AI toolchain (LangChain had CVEs in 2023–2024) |
| **Model poisoning** | Open-source model weights tampered with before you download them |
| **Embedding drift** | Embedding model update changes retrieval behaviour across your RAG pipeline |
| **Shadow AI** | Teams deploy models you haven't evaluated, using your data |

Traditional software has SBOMs. AI systems need equivalent provenance documentation — what NDAA and EU AI Act drafters are calling "AI-BOMs."

---

## What the Framework Misses

The three-layer pattern (Guardrails → Judge → Human) monitors runtime behaviour. It doesn't address:

1. **Whether the model you're monitoring is the model you approved**
2. **Whether the framework dependencies are trustworthy**
3. **Whether the model weights have integrity**

Runtime monitoring detects symptoms. Supply chain controls prevent the disease.

---

## The Pattern

| Control | What It Does | When |
|---------|-------------|------|
| **Model pinning** | Lock to specific model version/checkpoint | Deployment |
| **Dependency scanning** | Audit AI framework dependencies for vulnerabilities | CI/CD |
| **Weight verification** | Hash-based integrity checks on downloaded models | Download + deployment |
| **Provider change monitoring** | Detect when API-accessed models change behaviour | Continuous |
| **AI-BOM generation** | Document all AI components, versions, and sources | Release |
| **Shadow AI discovery** | Identify unsanctioned model usage across the enterprise | Periodic |

---

## The Uncomfortable Truth

For API-accessed models (OpenAI, Anthropic, etc.), you cannot verify model integrity. You are trusting the provider. Your controls are:

1. **Contractual** — SLAs that require change notification
2. **Behavioral** — Continuous evaluation that detects drift (this is where the Judge helps)
3. **Architectural** — Abstraction layers that let you switch providers if trust breaks

For self-hosted models, you have more control but more responsibility. You own the full chain from download to deployment.

Neither is inherently safer. Both need explicit controls.

---

## Related

- [Controls](/core/controls.md) — Runtime monitoring as a compensating control for supply chain opacity
- [Behavioral Anomaly Detection](/insights/behavioral-anomaly-detection.md) — Detecting provider-side changes via output drift
- [Supply Chain Controls](/extensions/technical/supply-chain.md) — Implementation guidance
