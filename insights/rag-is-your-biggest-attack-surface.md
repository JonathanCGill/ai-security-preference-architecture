# RAG Is Your Biggest Attack Surface

## The Pattern Everyone Uses, Nobody Secures

Retrieval-Augmented Generation (RAG) is the dominant enterprise AI pattern. It lets LLMs answer questions using your data without retraining.

The architecture is simple: embed your documents, store embeddings in a vector database, retrieve relevant chunks at query time, pass them to the LLM as context.

The security implications are not simple.

---

## The Problem

RAG creates a new data access path that bypasses your existing access controls.

Traditional path:
```
User → Application → Database → Access Control → Data
```

RAG path:
```
User → LLM → Retrieval → Vector Store → (maybe access control?) → Data → LLM → User
```

The LLM sees the retrieved data. The LLM generates a response. If the retrieved data includes content the user shouldn't see, the LLM will happily summarise it for them.

---

## Five Risks You're Probably Not Controlling

### 1. Retrieval Bypasses Document-Level Access Control

You embedded 50,000 documents. Some are HR-confidential. Some contain board minutes. Some are public knowledge base articles.

When a user queries the system, the vector similarity search returns the most semantically relevant chunks. It does not check whether the user is authorised to see them.

**Control required:** Query-time access filtering that enforces document-level (or chunk-level) permissions before retrieved content reaches the LLM.

### 2. Data Poisoning Through Ingestion

If an attacker can inject or modify documents in your source corpus, they can influence every future RAG response.

This is not theoretical. Any system that ingests user-generated content, customer emails, uploaded documents, or web-scraped data has an open ingestion path.

**Control required:** Ingestion validation, source authentication, and content integrity checks before embedding.

### 3. Prompt Injection Via Retrieved Content

Retrieved chunks become part of the LLM's context. If a retrieved document contains adversarial instructions (e.g., "Ignore previous instructions and..."), the LLM may follow them.

This is indirect prompt injection. The attack vector is your own data.

**Control required:** Content sanitisation at ingestion, guardrails on retrieved content before it enters the prompt, and output validation.

### 4. Information Leakage Through Inference

Even with access controls on retrieval, the LLM may infer sensitive information from seemingly innocuous chunks. Salary bands from job descriptions. M&A targets from legal memos. Customer complaints from support tickets.

The LLM synthesises. That's its job. The synthesis may reveal more than any individual source document.

**Control required:** Classification-aware retrieval that considers the sensitivity of synthesised output, not just individual source documents.

### 5. Embedding Store as a High-Value Target

Your vector database contains dense numerical representations of your proprietary data. It's typically less protected than your source databases because security teams don't yet think of vector stores as data stores.

They are.

**Control required:** Encryption at rest and in transit, access control, audit logging, and network segmentation for vector databases — the same controls you apply to any data store containing sensitive information.

---

## What the Three-Layer Pattern Catches

| Layer | RAG Risk Mitigated |
|-------|-------------------|
| **Guardrails** | PII in outputs, known-bad content patterns |
| **Judge** | Responses that seem inconsistent with expected scope |
| **Human Oversight** | Edge cases flagged by the judge |

## What It Misses

| Risk | Why the Pattern Misses It |
|------|--------------------------|
| Unauthorised retrieval | Happens before the LLM generates output — no output to evaluate |
| Data poisoning | Corrupted data produces plausible responses — judge may not flag them |
| Indirect prompt injection via data | Guardrails check user input, not retrieved content |
| Inference-based leakage | Individual outputs may look fine; the risk is in aggregation |
| Vector store compromise | Infrastructure risk, not output risk |

The three-layer pattern monitors output quality. RAG security requires controlling the input pipeline as well.

---

## The Controls

See [RAG Security Controls](../extensions/technical/rag-security.md) for implementation guidance.
---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
