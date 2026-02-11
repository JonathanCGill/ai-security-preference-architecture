# Supply Chain Controls

> Verifying what you deploy and what you depend on.

## Scope

Controls for the AI supply chain: model provenance, framework dependencies, and provider trust. Complements runtime monitoring with pre-deployment and continuous verification.

---

## Controls by Component

### 1. Model Provenance

#### API-Accessed Models (OpenAI, Anthropic, Google, etc.)

You cannot verify the model weights. Your controls are contractual and behavioural.

| Control | Implementation |
|---------|---------------|
| **Model version pinning** | Use explicit model version strings (e.g., `claude-sonnet-4-5-20250929`), not aliases (`claude-3-sonnet`) |
| **Change notification** | Contractual requirement for advance notice of model updates or deprecations |
| **Behavioral baseline** | Run a standardised evaluation suite against the model weekly; alert on score drift |
| **Multi-provider capability** | Architecture supports switching providers; reduces single-provider lock-in risk |
| **Provider security assessment** | Evaluate provider's SOC 2, data handling, and model security practices |

#### Self-Hosted Models (Llama, Mistral, etc.)

You have more control. You also have more responsibility.

| Control | Implementation |
|---------|---------------|
| **Hash verification** | Verify SHA-256 hash of downloaded weights against published hashes |
| **Trusted sources only** | Download from official repos only (Meta for Llama, Mistral for Mistral) |
| **Air-gapped download** | For sensitive environments: download on a clean machine, verify, then transfer |
| **Model card review** | Read the model card. Understand training data, known limitations, and licence terms |
| **Vulnerability scanning** | Scan model files for known malicious payloads (e.g., pickle exploits in PyTorch models) |
| **Version control** | Store approved model versions in your own artefact repository |

#### Fine-Tuned Models

| Control | Implementation |
|---------|---------------|
| **Training data audit** | Document and review all data used for fine-tuning |
| **Base model provenance** | Verify the base model before fine-tuning (controls above) |
| **Output comparison** | Compare fine-tuned model outputs against base model on a standard test suite |
| **Version control** | Store fine-tuned model artefacts with full lineage (base model + training data + hyperparameters) |

### 2. Framework Dependencies

AI applications depend on rapidly evolving frameworks with large dependency trees.

| Control | Implementation |
|---------|---------------|
| **Dependency pinning** | Pin all AI framework versions in requirements.txt / package-lock.json |
| **Vulnerability scanning** | Run Dependabot, Snyk, or equivalent on AI dependencies |
| **Dependency review** | Before upgrading LangChain, LlamaIndex, etc., review changelogs for security-relevant changes |
| **Minimal dependencies** | Don't install the entire framework if you only need retrieval. Reduce attack surface |
| **Private package mirror** | For sensitive environments: mirror approved packages internally |

#### High-Risk Dependencies

These frameworks have had security-relevant issues. Monitor them closely:

| Framework | Why It's High-Risk |
|-----------|-------------------|
| LangChain | Large attack surface, rapid release cycle, history of arbitrary code execution issues |
| Hugging Face Transformers | Pickle deserialization in model loading |
| PyTorch / TensorFlow | Model loading can execute arbitrary code |
| Vector database clients | Direct access to your embedded data |

### 3. Embedding Model Provenance

Embedding models are often overlooked. They encode your proprietary data. If the embedding model is compromised, your entire RAG pipeline is compromised.

| Control | Implementation |
|---------|---------------|
| **Pin embedding model version** | Changing the embedding model requires re-embedding your entire corpus. Pin it. |
| **Test embedding consistency** | Periodically embed known test strings and verify the output hasn't changed |
| **Monitor provider changes** | If using an API embedding model, monitor for version changes (same as LLM version pinning) |

---

## AI-BOM (AI Bill of Materials)

Document every AI component in your system. This is emerging as a regulatory requirement (NDAA, EU AI Act).

### Template

```yaml
ai_bom:
  system_name: "Customer Support Assistant"
  version: "2.1.0"
  last_updated: "2026-02-11"
  
  models:
    - name: "claude-sonnet-4-5-20250929"
      provider: "Anthropic"
      type: "api"
      purpose: "Response generation"
      version_pinned: true
      last_evaluated: "2026-02-01"
    
    - name: "text-embedding-3-large"
      provider: "OpenAI"
      type: "api"
      purpose: "Document embedding"
      version_pinned: true
      last_evaluated: "2026-01-15"
  
  frameworks:
    - name: "langchain"
      version: "0.3.12"
      pinned: true
      last_vulnerability_scan: "2026-02-10"
    
    - name: "chromadb"
      version: "0.5.2"
      pinned: true
      last_vulnerability_scan: "2026-02-10"
  
  data_sources:
    - name: "Product knowledge base"
      type: "internal"
      classification: "Internal"
      last_ingested: "2026-02-09"
      document_count: 12500
    
    - name: "Customer FAQ"
      type: "internal"
      classification: "Public"
      last_ingested: "2026-02-10"
      document_count: 850
  
  guardrails:
    - type: "input"
      provider: "custom"
      rules_version: "1.4"
    
    - type: "output"
      provider: "custom"
      rules_version: "1.4"
  
  judge:
    model: "gpt-4o"
    provider: "OpenAI"
    evaluation_criteria_version: "2.0"
    last_calibrated: "2026-01-28"
```

### Maintenance

| Activity | Frequency |
|----------|-----------|
| Update AI-BOM | Every deployment and quarterly review |
| Verify model version pins still resolve | Monthly |
| Vulnerability scan dependencies | Weekly (automated) |
| Review provider security posture | Annually or on significant change |

---

## Shadow AI Discovery

Teams will deploy AI without going through your controls. Find it.

| Detection Method | What It Finds |
|-----------------|--------------|
| **Network monitoring** | API calls to known AI provider endpoints (api.openai.com, api.anthropic.com, etc.) |
| **Cloud billing analysis** | Unexpected charges from AI service providers |
| **CASB/SWG logs** | Browser-based access to AI chat interfaces |
| **Endpoint monitoring** | Local LLM installations (Ollama, LM Studio, etc.) |
| **Procurement records** | AI tool subscriptions purchased outside IT |

**Don't just block it.** Understand why teams are using shadow AI, and provide a sanctioned path that meets their needs with appropriate controls.

---

## Related

- [The Supply Chain Problem](/insights/the-supply-chain-problem.md) — The problem statement
- [Behavioral Anomaly Detection Ops](/extensions/technical/anomaly-detection-ops.md) — Detecting model drift as a supply chain signal
- [Judge Assurance](/core/judge-assurance.md) — Evaluating model behaviour over time
