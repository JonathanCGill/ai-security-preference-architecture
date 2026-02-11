# README.md — Proposed Additions

This document shows exactly what to add to the existing README.md to integrate the new files.

---

## 1. Add to "Core Content" table

Add these rows to the existing Core Content table:

```markdown
| [Judge Assurance](/core/judge-assurance.md) | Evaluate and calibrate the LLM-as-Judge |
| [Multi-Agent Controls](/core/multi-agent-controls.md) | Delegation, trust, and accountability for agent-to-agent systems |
| [Multimodal Controls](/core/multimodal-controls.md) | Practical controls for image, audio, video, and document inputs |
| [Memory and Context](/core/memory-and-context.md) | Session isolation, context hygiene, persistent memory controls |
| [Streaming Controls](/core/streaming-controls.md) | Validation patterns for token-by-token delivery |
| [Reasoning Model Controls](/core/reasoning-model-controls.md) | Controls for models with internal chain-of-thought |
```

---

## 2. Add to "Extensions" technical table

Add these rows:

```markdown
| [extensions/technical/soc-integration.md](/extensions/technical/soc-integration.md) | SOC integration: alert taxonomy, identity correlation, escalation, SIEM rules |
| [extensions/technical/rag-security.md](/extensions/technical/rag-security.md) | RAG pipeline security: ingestion, access control, indirect prompt injection |
| [extensions/technical/supply-chain.md](/extensions/technical/supply-chain.md) | Supply chain: model provenance, dependency management, AI-BOM |
| [extensions/technical/nhi-lifecycle.md](/extensions/technical/nhi-lifecycle.md) | Non-human identity lifecycle for AI agents |
| [extensions/technical/cost-and-latency.md](/extensions/technical/cost-and-latency.md) | Cost modelling, sampling strategies, latency budgets |
| [extensions/technical/anomaly-detection-ops.md](/extensions/technical/anomaly-detection-ops.md) | Operationalising behavioral anomaly detection |
```

---

## 3. Add to "Insights" table — new section "Operational Gaps"

Add a new subsection under Insights:

```markdown
### Operational Gaps

| Article | Summary |
|---------|---------|
| [The Supply Chain Problem](/insights/the-supply-chain-problem.md) | You don't control the model you deploy |
| [RAG Is Your Biggest Attack Surface](/insights/rag-is-your-biggest-attack-surface.md) | Retrieval pipelines bypass your access controls |
```

---

## 4. Update "The Pattern" table

Replace the existing table to acknowledge the expanded control surface:

```markdown
| Layer | Function | Timing |
|-------|----------|--------|
| **Guardrails** | Prevent known-bad inputs/outputs | Real-time (~10ms) |
| **Judge** | Detect unknown-bad via LLM evaluation | Async (~500ms–5s) |
| **Human Oversight** | Decide edge cases, remain accountable | As needed |
| **Judge Assurance** | Verify the Judge itself | Continuous |
| **Pipeline Controls** | Secure RAG, memory, and data flows | Pre-runtime + runtime |
| **Supply Chain** | Verify models, dependencies, and providers | Pre-deployment + continuous |
| **SOC Integration** | Route AI alerts to security operations | Continuous |
```

---

## 5. Update "Quick Links" table

Add:

```markdown
| Secure a RAG pipeline | [RAG Security](/extensions/technical/rag-security.md) |
| Integrate with SOC | [SOC Integration](/extensions/technical/soc-integration.md) |
| Manage agent identities | [NHI Lifecycle](/extensions/technical/nhi-lifecycle.md) |
| Budget for controls | [Cost and Latency](/extensions/technical/cost-and-latency.md) |
```

---

## 6. Update Scope

Replace the existing Scope section to be more precise about what's now covered:

```markdown
## Scope

**In scope:** Custom LLM applications, AI decision support, document processing, agentic systems,
RAG pipelines, multi-agent systems, and the AI supply chain that supports them.

**Out of scope:**

* Vendor AI products (Copilot, Gemini, etc.) — use vendor controls
* Model training and fine-tuning workflows — see MLOps security guidance
* Pre-deployment functional testing — this guide is about production controls

This guide is **operationally focused** — from deployment architecture through incident response.
```

---

## 7. Remove "*(theoretical)*" label from Emerging Controls

Change:

```markdown
| [Emerging Controls](/core/emerging-controls.md) | Multimodal, reasoning, streaming *(theoretical)* |
```

To:

```markdown
| [Emerging Controls](/core/emerging-controls.md) | Research-stage controls for advanced scenarios |
```

The practical content for multimodal, reasoning, and streaming now lives in dedicated core documents. Emerging Controls retains truly theoretical content only.
