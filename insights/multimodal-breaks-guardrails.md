# Multimodal AI Breaks Your Text-Based Guardrails

*Images, audio, and video create attack surfaces your current controls can't see*

---

Most AI security controls assume text. Pattern matching on prompts. Keyword filters on outputs. Classifiers trained on language.

Multimodal AI — systems that process images, audio, video alongside text — renders these controls partially blind.

---

## The New Attack Surface

### Images as injection vectors

Text-based prompt injection is well understood: embed instructions in user input, override the system prompt. Guardrails can pattern-match known signatures.

Image-based injection is harder. An attacker embeds text in an image — visible or steganographic. The AI reads it. The guardrails don't, because they're scanning the text input, not the pixels.

Demonstrated attacks include:
- Instructions hidden in image backgrounds
- Text rendered in fonts that OCR reads but humans miss
- QR codes that models interpret as commands
- Adversarial patches that alter model behaviour

Your text-based input validation sees: "Describe this image."
The model sees: "Describe this image" + embedded instruction to ignore previous guidelines.

### Audio and video multiply the problem

Audio adds voice cloning, synthetic speech, and instructions embedded in frequencies humans don't hear. Video combines all attack vectors — images, audio, text overlays — in a temporal stream where malicious content can appear in a single frame.

Every modality is an input channel. Every input channel is an attack surface.

### Generated content risks

Multimodal models don't just consume images — they create them. The output risks expand accordingly:

- Deepfakes of real people
- Synthetic evidence (fake documents, fake screenshots)
- Generated CSAM
- Branded content without authorisation
- Imagery that bypasses text-based output filters

You can filter the word "violence." You can't as easily filter an image that depicts it without saying it.

---

## What Still Works

The three-layer model still applies. The implementation changes.

### Guardrails (adapted)

Text guardrails remain necessary — they catch text-based attacks. But they need companions:

| Modality | Guardrail Approach |
|----------|-------------------|
| Images | OCR + text extraction before input validation; image classifiers for content policy |
| Audio | Speech-to-text + text validation; audio classifiers for synthetic detection |
| Video | Frame extraction + image analysis; audio track analysis; temporal pattern detection |

The principle holds: inspect inputs before they reach the model. The inspection just got more complex and more expensive.

### Judge (extended)

The Judge needs multimodal capability to evaluate multimodal interactions.

A text-only Judge reviewing a conversation where the user uploaded an image is reviewing half the interaction. It can't assess whether the image contained an injection attempt. It can't evaluate whether the generated image was appropriate.

Options:
- Multimodal Judge model (evaluates all modalities natively)
- Modality-specific Judges (image Judge, audio Judge, orchestrated)
- Hybrid approach (multimodal for flagging, specialist for deep evaluation)

Cost increases. Latency increases. Coverage requires it anyway.

### Human oversight (more critical)

Humans can look at an image and judge appropriateness in ways that classifiers struggle with. Context matters. Cultural nuance matters. "Is this deepfake harmful?" often requires human judgment.

Multimodal content is harder to review at scale — you can't skim an image the way you skim text. HITL processes need:
- Tools for efficient multimodal review
- Specialists for specific content types
- Clear escalation for edge cases
- Realistic throughput expectations

---

## What Doesn't Work

### Text-only logging

If your interaction logs capture the text but not the images, you can't investigate multimodal incidents. You can't evaluate what you didn't record.

Log everything. Yes, it's expensive. The alternative is flying blind.

### Single-modality classification

A text classifier says the prompt is clean. An image classifier says the image is clean. Neither catches the attack that spans both — benign text + benign image = malicious combination.

Cross-modal analysis is necessary and nascent.

### Static allow/deny lists

Text-based deny lists don't apply to images. Image hash-based blocking only catches exact matches. Generative content creates infinite variations.

The cat-and-mouse game that text security has played for years is starting over in every new modality.

---

## Framework Extension

For multimodal AI systems, extend the standard controls:

| Control Area | Extension Required |
|--------------|-------------------|
| Input guardrails | Multi-modal extraction and analysis pipeline |
| Output guardrails | Generated content classifiers per modality |
| Logging | Full content capture across all modalities |
| Judge | Multimodal evaluation capability |
| HITL | Tooling and training for non-text review |
| Risk tiering | Consider modality-specific risks in classification |

The framework principles hold. The implementation is harder.

---

## The Uncomfortable Truth

Multimodal security is immature. The attacks are ahead of the defences. Best practices are emerging, not established.

If you're deploying multimodal AI in production:
- Acknowledge the gaps
- Over-invest in logging (you'll need it for incidents you can't yet prevent)
- Keep humans close to the outputs
- Constrain scope until controls mature

The framework provides structure. Multimodal fills in that structure with uncertainty.

---

*From the [Enterprise AI Security Framework](https://github.com/JonathanCGill/enterprise-ai-security-controls-framework) — operational controls for enterprise AI systems.*
