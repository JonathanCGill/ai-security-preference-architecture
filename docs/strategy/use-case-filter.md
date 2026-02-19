# Use Case Filter

*A structured decision flow for determining whether AI is the right solution — and what to use instead when it isn't.*

> Part of [AI Strategy](./)

---

## Why This Exists

Organisations reach for AI because it's available, not because it's appropriate. The framework's [first control](../insights/the-first-control.md) makes the point for security: the most effective way to reduce AI risk is to not use AI where it doesn't belong.

This filter makes that principle operational. Given a business problem, it walks through a structured set of questions and arrives at one of five recommendations — from "use traditional software" to "this needs generative AI." Each exit point names a specific technology approach, its risk profile, and whether the framework applies.

The filter sits between [Business Alignment](business-alignment.md) (is this a real problem worth solving?) and [Use Case Definition](use-case-definition.md) (what exactly will the AI system do?). Use it after you've confirmed the problem is real and before you start defining the AI use case.

---

## The Filter

![Use Case Filter](../images/strategy-use-case-filter.svg)

Seven questions, evaluated in order. Each question has a clear exit or a continuation. The first "yes" that leads to a non-AI exit is the answer — don't keep going just because you want to reach the AI options.

---

## The Seven Questions

### Q1: Can the problem be solved with deterministic rules?

If the logic can be expressed as "if X then Y" with bounded inputs and predictable outputs, you don't need AI. You need a rules engine, workflow automation, or traditional code.

| Signal | Suggests Rules | Suggests Not Rules |
|--------|---------------|-------------------|
| Business logic is documented and stable | Yes | |
| Decision trees exist or could be written | Yes | |
| Inputs are structured and bounded | Yes | |
| Exact, reproducible results are required every time | Yes | |
| Outputs are auditable and must match regulatory expectations precisely | Yes | |
| Logic changes frequently based on context | | Yes |
| Inputs are unstructured or ambiguous | | Yes |
| Edge cases outnumber the rules | | Yes |

**Exit → Traditional Software / Rules Engine**

| Attribute | Value |
|-----------|-------|
| Risk profile | Lowest — existing SDLC applies |
| Framework applies? | No |
| Examples | Eligibility checks, tax calculations, routing logic, compliance rules |
| Typical cost | Low build, low operate |
| Maintenance | Update rules when policy changes |

**Common mistake:** Building an AI system to replicate logic that already exists in a rules engine — or could. If the business has a procedure manual that staff follow step by step, that's a rules engine, not an AI use case.

---

### Q2: Is this a structured, repeatable process operating on existing systems?

If the work is repetitive, follows a fixed sequence, and involves interacting with existing application UIs or APIs, consider RPA or workflow automation before AI.

| Signal | Suggests RPA/Automation | Suggests Not RPA |
|--------|------------------------|------------------|
| Process follows the same steps every time | Yes | |
| Work involves copying data between systems | Yes | |
| Inputs come from structured forms or databases | Yes | |
| No judgement required — just execution | Yes | |
| Process requires understanding context or intent | | Yes |
| Inputs vary significantly between cases | | Yes |
| Exceptions are common and require interpretation | | Yes |

**Exit → RPA / Workflow Automation**

| Attribute | Value |
|-----------|-------|
| Risk profile | Low — deterministic, auditable |
| Framework applies? | No |
| Examples | Invoice processing, data migration, report generation, system onboarding |
| Typical cost | Low–medium build, low operate |
| Maintenance | Update when upstream systems change |

**Common mistake:** Using AI to "read" structured forms that could be parsed with templates, or to "automate" a process that's really just moving data between systems.

---

### Q3: Can this be solved with search, retrieval, or database queries?

If the user needs to find specific information from a known data source, the answer is often search — not AI. Retrieval-augmented generation (RAG) is AI; a well-configured search index is not.

| Signal | Suggests Search/Retrieval | Suggests Not Search |
|--------|--------------------------|---------------------|
| User knows roughly what they're looking for | Yes | |
| Answer exists verbatim in a document or database | Yes | |
| Results can be ranked by relevance without interpretation | Yes | |
| Query patterns are predictable | Yes | |
| User needs a synthesised answer across multiple sources | | Yes |
| Query is conversational or ambiguous | | Yes |
| Answer requires reasoning, not just retrieval | | Yes |

**Exit → Search / Database**

| Attribute | Value |
|-----------|-------|
| Risk profile | Low — deterministic ranking, no generation |
| Framework applies? | No |
| Examples | Knowledge base lookup, product catalogue search, policy document retrieval, FAQ matching |
| Typical cost | Low build (if data is indexed), low operate |
| Maintenance | Keep index current |

**Common mistake:** Building a RAG chatbot when users would be better served by a good search interface. RAG adds hallucination risk, requires guardrails, and needs Judge evaluation. Search returns actual documents. If the documents contain the answer and users can find them, search wins.

---

### Q4: Does it require pattern recognition on structured data?

If the task involves classification, regression, anomaly detection, or prediction based on tabular or structured data, traditional machine learning is likely more appropriate than generative AI. Traditional ML models are smaller, faster, cheaper, more predictable, and easier to explain.

| Signal | Suggests Traditional ML | Suggests Not Traditional ML |
|--------|------------------------|----------------------------|
| Structured input data (tables, numbers, categories) | Yes | |
| Classification or regression task | Yes | |
| Historical labelled data available | Yes | |
| Explainability matters (regulatory, customer-facing) | Yes | |
| Output is a score, class, or prediction — not text | Yes | |
| Input is unstructured (natural language, images, audio) | | Yes |
| Task requires generating novel content | | Yes |
| Reasoning across multiple steps is needed | | Yes |

**Exit → Traditional ML**

| Attribute | Value |
|-----------|-------|
| Risk profile | Low–Medium — predictable, testable |
| Framework applies? | Partially (monitoring, bias detection, model governance) |
| Examples | Fraud scoring, churn prediction, demand forecasting, credit risk |
| Typical cost | Medium build (data science), low–medium operate |
| Maintenance | Retrain on schedule; monitor for drift |

**Common mistake:** Using an LLM to classify structured data that a logistic regression or random forest could handle with better accuracy, lower cost, and full explainability. LLMs are not better at everything — they're better at language.

---

### Q5: Does it require understanding unstructured input?

If the task involves natural language, images, audio, or video — and understanding, not just processing — then AI is appropriate. The question is what kind.

| Signal | Suggests AI | What Kind |
|--------|-------------|-----------|
| Natural language understanding (intent, sentiment, entities) | Yes | NLP / LLM |
| Image recognition or classification | Yes | Computer vision |
| Audio transcription or analysis | Yes | Speech models |
| Document understanding (layout + content) | Yes | Document AI / multimodal |

**Continue to Q6** — AI is appropriate, but the type matters.

---

### Q6: Does it need to generate novel content?

If the task requires creating text, images, code, or other content that doesn't exist yet — not just finding or classifying existing content — then generative AI is appropriate.

| Signal | Suggests Generative AI | Suggests Non-Generative |
|--------|----------------------|------------------------|
| Output is draft text, summaries, or responses | Yes | |
| Content must be contextualised to the specific input | Yes | |
| Template-based responses won't cover the variation | Yes | |
| Output is a classification, score, or label | | Yes — use traditional ML or NLP |
| Responses can be assembled from predefined blocks | | Yes — use templating + retrieval |

**If No → Traditional NLP / Computer Vision / Speech**

| Attribute | Value |
|-----------|-------|
| Risk profile | Low–Medium |
| Framework applies? | Partially (monitoring, bias, model governance) |
| Examples | Sentiment analysis, named entity recognition, image classification, transcription |

**If Yes → Continue to Q7.**

---

### Q7: Does it require multi-step reasoning, tool use, or autonomous action?

This is the boundary between a generative AI application and an agentic AI system. If the AI needs to plan, use tools, call APIs, make decisions across multiple steps, or take actions in external systems, it's agentic.

| Signal | Suggests Agentic | Suggests Non-Agentic |
|--------|------------------|---------------------|
| AI needs to break a task into sub-tasks | Yes | |
| AI calls external APIs or tools | Yes | |
| AI makes sequential decisions where each depends on the last | Yes | |
| AI takes actions with real-world consequences (send, write, execute) | Yes | |
| Task is single-turn: input → output | | Yes |
| AI produces text/content but doesn't act on it | | Yes |

**If No → LLM / Generative AI**

| Attribute | Value |
|-----------|-------|
| Risk profile | Medium–Critical (depends on use case) |
| Framework applies? | Yes — full framework |
| Examples | Customer service drafting, document summarisation, code generation, content creation |
| Typical cost | Medium build, medium–high operate (guardrails, Judge, HITL) |
| Maintenance | Guardrail tuning, Judge calibration, prompt management, model updates |

**If Yes → Multi-Agent / Agentic AI**

| Attribute | Value |
|-----------|-------|
| Risk profile | High–Critical |
| Framework applies? | Yes — full framework + [MASO](../maso/) |
| Examples | Automated research workflows, autonomous customer resolution, multi-system orchestration |
| Typical cost | High build, high operate |
| Maintenance | All of the above + agent coordination, sandbox management, action validation |

---

## The Five Exits — Summary

| Exit | Technology | Risk Profile | Framework? | Key Advantage |
|------|-----------|-------------|------------|---------------|
| **1** | Rules / traditional software | Lowest | No | Deterministic, auditable, cheapest to operate |
| **2** | RPA / workflow automation | Low | No | Handles repetition without judgement |
| **3** | Search / database | Low | No | Returns real documents, no hallucination |
| **4** | Traditional ML | Low–Medium | Partial | Explainable, testable, predictable |
| **5a** | LLM / generative AI | Medium–Critical | Full | Handles unstructured input, generates content |
| **5b** | Multi-agent / agentic AI | High–Critical | Full + MASO | Autonomous multi-step reasoning and action |

**The honest answer is often hybrid.** A single system might use rules for routing, search for retrieval, traditional ML for scoring, and an LLM for response generation. The filter applies per-component, not per-system. The framework applies to the AI components; the non-AI components follow standard SDLC.

---

## Applying the Filter — Worked Examples

### Example 1: "We want AI to answer employee HR questions"

| Question | Answer | Result |
|----------|--------|--------|
| Q1: Deterministic rules? | Some questions are policy lookups, but many need interpretation | Partial — split |
| Q2: Structured process? | No — questions are freeform | Continue |
| Q3: Search/retrieval? | Many answers exist in policy documents | **Partial exit: search covers 60-70%** |
| Q5: Unstructured input? | Yes — natural language questions | Continue |
| Q6: Generate content? | Yes — needs to synthesise answers from policy | Continue |
| Q7: Agentic? | No — answer questions, don't take actions | **Exit: LLM (generative AI)** |

**Recommendation:** Hybrid. Search-first architecture where a retrieval system surfaces relevant policy documents, and an LLM synthesises the answer. The LLM component falls under the framework; the search component doesn't. If the system also books leave or updates records, Q7 triggers agentic controls for those components.

### Example 2: "We want AI to detect fraudulent transactions"

| Question | Answer | Result |
|----------|--------|--------|
| Q1: Deterministic rules? | Existing rules catch 85% of fraud, but miss novel patterns | Partial — rules for known patterns |
| Q4: Pattern recognition on structured data? | Yes — transaction data is structured; task is classification | **Exit: Traditional ML** |

**Recommendation:** Traditional ML for the fraud scoring model. Rules engine for known patterns. LLM not needed — the task is classification on structured data, not language understanding. The ML model needs monitoring, bias detection, and model governance (partial framework), but not guardrails, Judge evaluation, or HITL in the AI-framework sense.

### Example 3: "We want AI to generate marketing content"

| Question | Answer | Result |
|----------|--------|--------|
| Q1: Deterministic rules? | No — creative content | Continue |
| Q2: Structured process? | No | Continue |
| Q3: Search? | No — generating new content, not finding existing | Continue |
| Q5: Unstructured input? | Yes — briefs are natural language | Continue |
| Q6: Generate content? | Yes — that's the entire purpose | Continue |
| Q7: Agentic? | No — generates drafts; humans publish | **Exit: LLM (generative AI)** |

**Recommendation:** LLM / generative AI. Full framework applies. Risk tier depends on audience (internal drafts vs. published to customers), data sensitivity (does it access customer data for personalisation?), and decision authority (do humans review every output before publishing?).

### Example 4: "We want AI to process insurance claims end-to-end"

| Question | Answer | Result |
|----------|--------|--------|
| Q1: Deterministic rules? | Partial — some claim types follow strict rules | Split: rules for simple claims |
| Q5: Unstructured input? | Yes — claim descriptions, photos, medical reports | Continue |
| Q6: Generate content? | Yes — draft assessments, correspondence | Continue |
| Q7: Agentic? | Yes — needs to pull data from multiple systems, make payment decisions, send communications | **Exit: Multi-agent / agentic AI** |

**Recommendation:** Hybrid with agentic components. Rules engine for simple, deterministic claims. LLM + agentic controls for complex claims requiring document understanding, multi-system lookup, and decision-making. Full framework + MASO applies to the agentic components. This is HIGH or CRITICAL tier depending on autonomy — if the AI approves payments without human review, it's CRITICAL.

---

## When to Re-Run the Filter

The filter isn't one-and-done. Re-evaluate when:

| Trigger | Why |
|---------|-----|
| **Scope expands** | FAQ bot that now handles account changes needs re-filtering |
| **New AI capabilities emerge** | Task that required rules in 2024 might benefit from AI in 2026 |
| **Volume changes** | Low-volume process handled by humans might need AI at scale |
| **Accuracy requirements change** | "Good enough" ML model might need LLM reasoning for edge cases |
| **Regulations change** | New explainability requirements might push you from LLM back to traditional ML |

---

## Relationship to Other Articles

- **[Business Alignment](business-alignment.md)** answers "is this problem worth solving?" — run this filter after that's confirmed
- **[Use Case Definition](use-case-definition.md)** defines the AI system in detail — run this filter before that starts
- **[From Idea to Production](idea-to-production.md)** includes tool selection as Stage 3 — this filter is the detailed version of that stage
- **[The First Control](../insights/the-first-control.md)** provides the principle; this filter provides the process

---

*AI Runtime Behaviour Security, 2026 (Jonathan Gill).*
