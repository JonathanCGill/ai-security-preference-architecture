# Streaming Controls

> Validating output that hasn't finished yet.

## The Problem

Most LLM deployments use streaming (Server-Sent Events) to deliver tokens incrementally. Users see output as it's generated, typically 20–80ms per token.

The three-layer pattern assumes you can evaluate a complete response:

1. Guardrails check the full output → **Can't. It's not complete.**
2. Judge evaluates the full output → **Can't. Same reason.**
3. Human reviews if flagged → **User already saw it.**

By the time evaluation is possible, the content is already on screen.

---

## Why This Matters

For Tier 1 (low risk) — internal chatbots, content drafting — this is acceptable. Users understand they're interacting with a draft.

For Tier 2–3 — customer-facing, regulated, consequential — delivering unevaluated content to users is a control gap.

---

## Control Patterns

### Pattern 1: Buffer and Release

Hold tokens in a server-side buffer. Release in chunks after evaluation.

```
LLM → Buffer (N tokens) → Guardrail check → Release to client → Buffer next N tokens → ...
```

| Parameter | Setting | Trade-off |
|-----------|---------|-----------|
| Buffer size | 50–100 tokens (~1–2 sentences) | Larger = better detection, worse perceived latency |
| Evaluation | Text guardrails on buffer content | Fast rules only — no LLM judge in the loop |
| Release trigger | Guardrail pass OR buffer timeout | Timeout prevents indefinite blocking |
| Block action | Replace buffer content with safe message | User sees interruption, not harmful content |

**Latency impact:** 200–500ms additional delay per chunk. Acceptable for most use cases.

**Limitation:** Evaluation is per-chunk, not whole-response. Context-dependent violations (safe first half, harmful when combined with second half) will be missed.

### Pattern 2: Stream with Post-Hoc Evaluation

Deliver tokens immediately. Evaluate the complete response asynchronously. Retract or flag if evaluation fails.

```
LLM → Client (real-time)
  ↓ (parallel)
LLM → Complete response → Judge evaluation
  ↓ (if flagged)
Client ← Retraction/warning message
```

| Step | Timing | What Happens |
|------|--------|-------------|
| Streaming delivery | Real-time | User sees output immediately |
| Response completion | 2–30s after stream starts | Full response available for evaluation |
| Judge evaluation | 500ms–5s after completion | Async quality check |
| Retraction (if needed) | 3–35s after first token | Warning appended, content flagged in UI |

**Limitation:** User has already seen the content. Retraction limits further harm but doesn't prevent initial exposure.

**When to use:** Tier 1–2 where the risk of initial exposure is acceptable and the primary goal is audit trail and pattern detection, not prevention.

### Pattern 3: Non-Streaming for High Risk

Don't stream. Generate the complete response, evaluate it, then deliver.

```
LLM → Complete response → Guardrails → Judge → Deliver (or block)
```

| Trade-off | Impact |
|-----------|--------|
| Latency | User waits 3–30s for response with no incremental feedback |
| UX | Worse perceived performance — mitigate with loading indicators |
| Safety | Full evaluation before delivery — strongest control |

**When to use:** Tier 3 and any Tier 2 use case where content reaches customers, regulators, or triggers consequential actions.

---

## Choosing the Right Pattern

| Risk Tier | Recommended Pattern | Rationale |
|-----------|-------------------|-----------|
| **Tier 1** (Internal, low risk) | Stream with post-hoc evaluation | Users are employees, risk is low, UX matters |
| **Tier 2** (Business impact) | Buffer and release | Balance between safety and responsiveness |
| **Tier 2** (Customer-facing) | Non-streaming OR buffer-and-release | Customer exposure requires pre-delivery evaluation |
| **Tier 3** (Regulated, consequential) | Non-streaming | Full evaluation before any content is delivered |

---

## Implementation Notes

### Buffer and Release — Server-Side

```python
# Pseudocode — adapt to your framework
buffer = []
buffer_size = 75  # tokens

async for token in llm.stream(prompt):
    buffer.append(token)
    if len(buffer) >= buffer_size:
        chunk_text = "".join(buffer)
        if guardrail.check(chunk_text).passed:
            yield chunk_text
            buffer = []
        else:
            yield "[Content filtered by safety controls]"
            # Log the blocked content for review
            log_blocked_content(chunk_text, request_id)
            break  # Stop generation

# Flush remaining buffer
if buffer:
    chunk_text = "".join(buffer)
    if guardrail.check(chunk_text).passed:
        yield chunk_text
```

### Post-Hoc Retraction — Client-Side

The client must support retraction. This means:

1. **Response IDs** — Every streamed response has a unique ID
2. **Retraction channel** — WebSocket or SSE channel for retraction messages
3. **UI handling** — Client replaces or flags retracted content
4. **Audit logging** — Both the original content and retraction are logged

---

## What You Lose with Streaming

| Capability | Impact on Streaming |
|-----------|-------------------|
| Full-response guardrails | Degraded — chunk-level only (Pattern 1) or post-hoc (Pattern 2) |
| Judge evaluation | Async only — cannot block delivery in real-time |
| Consistent quality scoring | Scores apply to complete response, available only after stream ends |
| Deterministic safety | You cannot guarantee no user ever sees problematic content in a stream |

This is an inherent trade-off. If you need deterministic safety, don't stream.

---

## Related

- [You Can't Validate What Hasn't Finished](/insights/you-cant-validate-unfinished.md) — The problem statement
- [Controls](/core/controls.md) — The three-layer pattern (designed for complete responses)
- [Cost and Latency](/extensions/technical/cost-and-latency.md) — Latency budgets for evaluation
