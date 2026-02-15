# You Can't Validate What Hasn't Finished

*Real-time AI breaks the inspect-then-deliver model*

---

The standard AI security model: input arrives, guardrails inspect, model processes, guardrails inspect output, response delivered.

This assumes the output is complete before delivery.

Real-time and streaming AI doesn't work that way.

---

## The Streaming Challenge

Voice assistants, live translation, real-time transcription, interactive agents — these systems produce output as they go. The user receives partial results while the model is still generating.

Token by token. Word by word. Sentence by sentence.

By the time you could evaluate the complete output, the user has already heard most of it.

---

## What Breaks

### Output guardrails

Traditional output guardrails inspect the full response. They look for patterns, check for PII, evaluate against policies.

Streaming guardrails face impossible choices:

**Option 1: Buffer everything, validate, then stream**
- Adds latency the streaming model was designed to avoid
- User experience suffers
- Defeats the purpose of streaming

**Option 2: Validate chunks as they stream**
- Can't see patterns that span chunks
- Harmful content might be split across boundaries
- Policy violations assembled word by word pass through

**Option 3: Let it stream, evaluate after**
- User has already received potentially harmful content
- Evaluation becomes incident detection, not prevention
- The horse has left the barn

None of these is satisfactory.

### The PII problem

Streaming a response that mentions a customer name:

"The account holder, J..."
*— Can you stop it here? You don't know what comes next.*

"...ohn Smith, has..."
*— Too late. PII delivered.*

Partial content doesn't reveal whether the full content violates policy.

### Tone and trajectory

A response can start appropriately and go wrong:

"I understand you're frustrated. Let me help you with..."
*— So far, fine.*

"...a workaround that technically violates our terms of service but..."
*— Problem.*

Chunk-by-chunk validation can't see where the response is heading. By the time the trajectory is clear, significant content has streamed.

---

## Real-Time Interactions Compound This

Voice and video add constraints:

**Latency sensitivity**: Users expect real-time responses. Even small delays feel unnatural. Heavy validation breaks the interaction model.

**No take-backs**: Once audio is spoken or video is shown, it can't be unspoken or unshown. Text streaming can at least be edited on screen. Voice is gone.

**Continuous generation**: A voice agent might speak for 30 seconds continuously. That's not chunks — that's a constant stream of potentially reviewable content with no natural pause points.

---

## Adaptation Strategies

### Shift left harder

If you can't fully validate outputs, validate inputs more aggressively.

- Tighter input constraints
- More restrictive system prompts
- Narrower scope for real-time systems
- User verification before sensitive topics

Prevention over detection when detection is too late.

### Probabilistic interruption

Don't wait for certainty. Interrupt on probability.

Monitor the stream for concerning signals. If confidence that something problematic is developing exceeds threshold, interrupt:

"Let me rephrase that..."
"Actually, I should clarify..."
*[silence / topic change]*

This is imperfect. False positives are awkward. But late interruption beats no interruption.

### Post-hoc consequences

If you can't prevent, ensure consequences.

- Full stream logging
- Rapid async evaluation
- Fast incident detection
- Automated alerting on policy violations
- Quick remediation (follow-up correction, customer contact)

The Judge operates after delivery, but operates fast.

### Scope limitation

Some content shouldn't stream.

Sensitive topics, high-risk decisions, regulated advice — these might require the non-streaming path even in a real-time system.

"Let me look into that and get back to you in a moment..."
*[switch to full validation path]*

Streaming for low-risk content. Buffered for high-risk content. The system adapts based on topic.

---

## Framework Implications

### Risk tiering includes modality

A text chatbot and a voice agent with identical capabilities are not the same risk tier. The voice agent has less controllable output.

Real-time modalities push systems toward higher tiers.

### Guardrail architecture changes

| Traditional | Real-Time Adaptation |
|-------------|---------------------|
| Full output inspection | Streaming pattern detection |
| Block before delivery | Interrupt during delivery |
| Binary pass/fail | Confidence-based intervention |
| Synchronous | Parallel monitoring |

### Judge timing changes

The Judge can't wait for a batch. Concerning streams need fast evaluation.

Near-real-time Judge for streaming systems:
- Evaluate streams as they complete (not daily batch)
- Prioritise streams that triggered interruption
- Alert on violations within minutes, not hours

### Acceptance of imperfection

Streaming AI will sometimes deliver content that wouldn't have passed full validation.

The framework should:
- Acknowledge this explicitly
- Set expectations about what's preventable
- Emphasise detection and response over pure prevention
- Define acceptable residual risk for streaming use cases

---

## The Trajectory

Real-time AI is becoming the default. Voice interfaces, live assistance, instant translation — users expect immediate response.

The inspect-then-deliver model assumes time we don't have.

Security approaches will adapt: more prevention, probabilistic intervention, faster detection, graceful degradation. But the fundamental tension — validation takes time, streaming doesn't wait — won't disappear.

Build for imperfect control. Design for fast recovery.

---

*From the [Enterprise AI Security Framework](https://github.com/JonathanCGill/enterprise-ai-security-controls-framework) — operational controls for enterprise AI systems.*
