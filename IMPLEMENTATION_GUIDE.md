# AI Security Controls — Implementation Guide

This guide points you to resources for implementing AI security controls. We don't provide code—the APIs change frequently and untested code causes more problems than it solves.

---

## The Pattern

![AI Security Control Pattern](images/control-pattern.svg)

| Component | Purpose |
|-----------|---------|
| **Input Guardrails** | Block malicious inputs before they reach the LLM |
| **Output Guardrails** | Validate and sanitize responses before delivery |
| **Judge Queue** | Async LLM evaluation of sampled interactions |
| **Human Review** | Final decision on edge cases and flagged content |

---

## Open Source Implementations

These projects have tested, maintained code:

| Project | What It Does | Link |
|---------|--------------|------|
| **NeMo Guardrails** | NVIDIA's guardrails framework, Colang-based | https://github.com/NVIDIA/NeMo-Guardrails |
| **Guardrails AI** | Output validation and structured generation | https://github.com/guardrails-ai/guardrails |
| **LangChain** | Includes moderation chains and safety tools | https://github.com/langchain-ai/langchain |
| **LlamaGuard** | Meta's safety classifier | https://github.com/meta-llama/PurpleLlama |
| **Rebuff** | Prompt injection detection | https://github.com/protectai/rebuff |

---

## Cloud Provider Documentation

### AWS Bedrock

| Component | Link |
|-----------|------|
| Bedrock Guardrails (managed) | https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails.html |
| ApplyGuardrail API | https://docs.aws.amazon.com/bedrock/latest/APIReference/API_runtime_ApplyGuardrail.html |
| Terraform resource | https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/bedrock_guardrail |
| boto3 reference | https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/bedrock-runtime.html |

### Azure OpenAI

| Component | Link |
|-----------|------|
| Content Filtering | https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/content-filter |
| Prompt Shields | https://learn.microsoft.com/en-us/azure/ai-services/content-safety/concepts/jailbreak-detection |
| Python SDK | https://learn.microsoft.com/en-us/azure/ai-services/openai/quickstart |

### Google Vertex AI

| Component | Link |
|-----------|------|
| Safety Filters | https://cloud.google.com/vertex-ai/generative-ai/docs/learn/responsible-ai |
| Python SDK | https://cloud.google.com/vertex-ai/generative-ai/docs/start/quickstarts/quickstart-multimodal |

### OpenAI

| Component | Link |
|-----------|------|
| Moderation API | https://platform.openai.com/docs/guides/moderation |
| Chat Completions | https://platform.openai.com/docs/api-reference/chat |

### Anthropic

| Component | Link |
|-----------|------|
| Messages API | https://docs.anthropic.com/en/api/messages |
| Prompt Engineering | https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering |

---

## Standards and Frameworks

| Resource | Link |
|----------|------|
| OWASP LLM Top 10 | https://owasp.org/www-project-top-10-for-large-language-model-applications/ |
| NIST AI RMF | https://www.nist.gov/itl/ai-risk-management-framework |
| MITRE ATLAS | https://atlas.mitre.org/ |
| EU AI Act | https://artificialintelligenceact.eu/ |

---

## What to Build

1. **Input validation**: Use your cloud provider's managed guardrails, or adapt patterns from NeMo/Guardrails AI.

2. **Output validation**: PII detection, forbidden content, structured output validation. See Guardrails AI for examples.

3. **Sampling and evaluation**: Not every interaction needs review. Sample 5-10% plus all flagged items.

4. **Human review queue**: Priority-based queue with SLA tracking. Standard engineering—use your existing tooling.

5. **Logging and metrics**: Log all interactions (inputs, outputs, blocks, latency). Essential for debugging and compliance.

---

## Recommendations

- **Start with managed services** (Bedrock Guardrails, Azure Content Filtering) before building custom.
- **Use existing libraries** rather than writing regex patterns from scratch.
- **Test against real attacks**—see OWASP LLM Top 10 for attack categories.
- **Plan for false positives**—overly aggressive filters frustrate users.
- **Keep humans in the loop**—automated systems miss edge cases.

---

For governance, compliance, and organizational guidance, see the full [Enterprise AI Security Framework](README.md).
