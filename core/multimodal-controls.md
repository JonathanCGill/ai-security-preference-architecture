# Multimodal Controls

> Practical controls for AI systems that process images, audio, video, or documents — not just text.

## The Problem

The three-layer pattern was designed for text-in, text-out systems. Most enterprise AI is moving beyond that:

- **Document processing** — PDFs, scanned images, handwritten forms
- **Image analysis** — Product images, medical imaging, visual inspection
- **Audio** — Transcription, call centre analysis, voice agents
- **Video** — Surveillance analysis, content moderation, visual Q&A

Text-based guardrails cannot inspect an image. A regex that catches "ignore previous instructions" in text won't catch the same phrase rendered as text in a PNG.

---

## Attack Surfaces by Modality

| Modality | Attack Vector | Example |
|----------|--------------|---------|
| **Image** | Text-in-image injection | Adversarial text rendered in an image that the model reads as instructions |
| **Image** | Steganographic payload | Data hidden in pixel values, invisible to humans, processed by models |
| **Image** | Adversarial perturbation | Pixel-level modifications that change model classification |
| **Audio** | Inaudible command injection | Ultrasonic frequencies the model processes but humans can't hear |
| **Audio** | Voice cloning for auth bypass | Synthetic voice that passes voice biometric checks |
| **Document** | Embedded instruction in PDF metadata | Adversarial content in document properties or invisible layers |
| **Document** | OCR manipulation | Characters that OCR reads differently from how humans read them |
| **Video** | Frame injection | Single adversarial frames inserted into video streams |

---

## Controls by Layer

### Guardrails for Multimodal Input

| Control | What It Does | Tooling |
|---------|-------------|---------|
| **Image-to-text extraction + text guardrails** | OCR the image, apply text guardrails to extracted text | Tesseract, AWS Textract, Azure Document Intelligence + existing text guardrails |
| **File type validation** | Reject unexpected file types, verify magic bytes match extension | Standard input validation — not AI-specific |
| **Metadata stripping** | Remove EXIF, PDF metadata, document properties before processing | ExifTool, PyPDF2, purpose-built sanitisers |
| **Image content classification** | Pre-screen images for NSFW, violence, or policy-violating content before LLM processing | AWS Rekognition, Google Vision SafeSearch, Azure Content Safety |
| **Audio transcription + text guardrails** | Transcribe audio, apply text guardrails to transcript | Whisper, AWS Transcribe + existing text guardrails |
| **File size and dimension limits** | Prevent resource exhaustion from oversized inputs | Standard input validation |

### Key Principle

**Convert multimodal inputs to text where possible, then apply your existing text guardrails.**

This doesn't catch everything (adversarial perturbations won't survive OCR), but it catches the most common attack: text-based prompt injection delivered via a non-text modality.

### Judge Evaluation for Multimodal Outputs

The Judge needs to evaluate outputs in context of the input modality.

| Scenario | Judge Approach |
|----------|---------------|
| Text response to image query | Standard text evaluation — same as text-only |
| Image generation | Content classification on output image + text evaluation of any captions |
| Audio generation | Transcribe output + text evaluation |
| Document generation | Extract text from generated document + text evaluation |

**Limitation:** Image and audio evaluation by an LLM-as-Judge is less reliable than text evaluation. The judge may not "see" subtle content in generated images the way a human would.

**Compensating control:** Increase human review sample rate for multimodal outputs. If your text-only human review rate is 5%, consider 15–20% for multimodal.

### Human Oversight Adjustments

| Modality | Additional Human Oversight |
|----------|--------------------------|
| **Image generation** | Higher sample rate — LLM judges are weaker on visual content |
| **Audio (customer-facing)** | Review both transcript and audio — tone matters, transcripts lose it |
| **Document processing (regulated)** | Verify extraction accuracy against source document |
| **Video** | Spot-check at defined intervals — full review is impractical |

---

## Cross-Modal Attacks

The most dangerous attacks combine modalities. A benign text prompt + a malicious image can bypass guardrails that only check each input independently.

| Attack | How It Works | Control |
|--------|-------------|---------|
| **Split instruction** | Half the instruction in text, half in image | Evaluate combined context, not inputs independently |
| **Modality mismatch** | Benign text, adversarial image | Apply guardrails to each modality AND to the combined input |
| **Format escalation** | Text query triggers image generation that bypasses text output guardrails | Apply content classification to all output modalities |

**Architectural principle:** Evaluate the full multimodal context as a unit, not each modality in isolation.

---

## What's Still Theoretical

Being honest about limitations:

| Area | Status | Why |
|------|--------|-----|
| **Adversarial image detection** | Research stage | No reliable production-grade detector for adversarial perturbations |
| **Steganography detection in AI context** | Research stage | Traditional steganalysis exists but isn't integrated with AI guardrails |
| **Ultrasonic audio injection prevention** | Research stage | Known attack vector, no standardised enterprise control |
| **Video real-time analysis at scale** | Early adoption | Latency and cost prohibitive for most enterprises |

For these, the control is: **risk-accept with monitoring, or don't deploy that modality in high-risk tiers.**

---

## Implementation Priority

| If Your System Handles... | Implement First |
|--------------------------|----------------|
| Text only | You don't need this document yet |
| Text + document upload (PDF, DOCX) | Metadata stripping, OCR + text guardrails, file validation |
| Text + image input | Image content classification, OCR extraction, cross-modal evaluation |
| Image generation | Output content classification, increased human review |
| Audio (transcription/voice) | Transcription + text guardrails, audio quality validation |
| Video | Treat as research/Tier 3 — high human oversight |

---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
