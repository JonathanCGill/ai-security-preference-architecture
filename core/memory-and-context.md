# Memory and Context Controls

> Securing what the model remembers — across turns, sessions, and users.
>
> *This document uses the simplified three-tier system (Tier 1/2/3). See [Risk Tiers — Simplified Tier Mapping](risk-tiers.md#simplified-tier-mapping) for the mapping to LOW/MEDIUM/HIGH/CRITICAL.*

## The Problem

The three-layer pattern evaluates individual requests and responses. But AI systems accumulate context:

- **Within a conversation** — each turn adds to the context window
- **Across conversations** — persistent memory, session history, user profiles
- **Across users** — shared embeddings, cached responses, fine-tuned models

A single request-response pair may be safe. The accumulated context may not be.

---

## Threat Model

| Threat | Vector | Impact |
|--------|--------|--------|
| **Gradual context poisoning** | Early turns inject instructions that influence later turns | Model behaviour changes over a long conversation without triggering per-turn guardrails |
| **Cross-session leakage** | Persistent memory or shared cache surfaces User A's data for User B | Data breach — potentially regulated data |
| **Memory manipulation** | Injecting false "memories" via conversation that persist across sessions | Ongoing manipulation of model behaviour for a user |
| **Context window overflow** | Filling the context with irrelevant content to push out system instructions | Guardrail bypass — system prompt "forgotten" |
| **Accumulated PII** | Individual turns are PII-free but the conversation as a whole builds a profile | Privacy violation — model holds more personal data than any individual turn reveals |

---

## Controls

### 1. Session Isolation

Every user session must be isolated. No shared state between users unless explicitly designed and controlled.

| Requirement | Implementation |
|-------------|---------------|
| **Separate context per user** | Each user gets their own conversation thread — no shared context window |
| **Separate memory per user** | Persistent memory is scoped to the authenticated user |
| **No shared cache for generated responses** | Response caching (if used) must be keyed to user + input, not input alone |
| **Session timeout** | Conversations expire after inactivity — context is not preserved indefinitely |

### 2. Context Window Hygiene

| Control | What It Does |
|---------|-------------|
| **System prompt anchoring** | Re-inject system instructions at intervals in long conversations, not just at the start |
| **Context summarisation** | Periodically summarise old turns and replace verbose history with summaries |
| **Turn limits** | Maximum number of turns per conversation before requiring a new session |
| **Token budget monitoring** | Alert when context window approaches capacity — model behaviour degrades near limits |

### 3. Persistent Memory Controls

For systems that maintain memory across sessions (user preferences, conversation history, learned context):

| Control | What It Does |
|---------|-------------|
| **Memory content filtering** | Apply guardrails to content before it's written to persistent memory |
| **Memory access control** | Only the authenticated user (or authorised system) can read their memory |
| **Memory expiry** | Set TTLs on stored memories — not everything should persist forever |
| **Memory audit trail** | Log what's written to and read from persistent memory |
| **User memory controls** | Users can view, edit, and delete their stored memories |
| **Memory injection prevention** | Validate that persistent memories are genuine (from real conversations) not injected |

### 4. Accumulated Context Evaluation

Don't just evaluate individual turns. Periodically evaluate the full conversation context.

| Trigger | Action |
|---------|--------|
| Every N turns (e.g., 10) | Run the Judge on the full conversation, not just the latest turn |
| Context window >50% full | Check for context poisoning patterns (repeated instructions, topic drift) |
| User requests sensitive action | Evaluate the full conversation for manipulation patterns before allowing the action |

### 5. Cross-Session Data Governance

| Requirement | Implementation |
|-------------|---------------|
| **Data classification** | Classify persistent memory content with the same scheme used for other data stores |
| **Retention policies** | Apply your existing data retention policies to conversation history and memory |
| **Right to deletion** | Implement memory deletion that actually deletes — not just soft-delete |
| **Encryption** | Encrypt persistent memory at rest and in transit — same controls as any data store |

---

## Architecture Patterns

### Stateless (Recommended for Tier 1–2)

No persistent memory. Each conversation starts fresh. Context exists only within the session.

- Simplest to secure
- No cross-session risks
- Users may find it frustrating for repeated tasks

### Stateful with Scoped Memory (Tier 2–3)

Persistent memory scoped to user, with explicit controls.

- Memory is a separate data store with its own access controls
- Memory content is filtered before storage and before retrieval
- Memory has TTLs and audit trails

### Shared Knowledge Base (Tier 3 — requires careful design)

Shared embeddings or knowledge that multiple users access (e.g., company FAQ, product documentation).

- Shared content must be read-only for end users
- Ingestion pipeline is controlled (see [RAG Security](../extensions/technical/rag-security.md))
- User-specific context is never written to shared stores
---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
