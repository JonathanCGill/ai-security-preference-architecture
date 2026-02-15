# Judge Assurance

> Who judges the judge?

## The Problem

The three-layer pattern depends on the LLM-as-Judge to detect "unknown-bad" outputs that guardrails miss. But the Judge is itself an LLM. It hallucinates. It drifts. It can be manipulated.

If you deploy a Judge without evaluating its accuracy, you've added cost and latency without knowing whether you've added safety.

---

## What Can Go Wrong

| Failure Mode | Impact |
|-------------|--------|
| **False negatives** | Judge approves harmful outputs — the whole point of the Judge is defeated |
| **False positives** | Judge flags benign outputs — users lose trust, teams disable the Judge |
| **Judge drift** | Judge model is updated by provider, changing evaluation behaviour without notice |
| **Judge manipulation** | Adversarial inputs crafted to pass the Judge while failing for humans |
| **Evaluation collapse** | Judge uses same reasoning patterns as the generator — shared blind spots |

The last one is the most insidious. If your generator is GPT-4 and your judge is also GPT-4, they may share the same failure modes. The judge won't catch what it can't see.

---

## Controls

### 1. Establish a Human-Labelled Ground Truth

Before you trust the judge, measure its accuracy against human judgment.

| Step | What |
|------|------|
| Sample | Randomly select N outputs per week (N depends on volume and risk tier) |
| Label | Have qualified humans evaluate the same outputs the Judge evaluated |
| Compare | Calculate agreement rate, precision, recall, F1 |
| Track | Plot these metrics over time |

**Minimum viable metrics:**

| Metric | What It Measures | Target (adjust per risk tier) |
|--------|-----------------|-------------------------------|
| **Agreement rate** | How often Judge and human agree | >90% for HIGH, >95% for CRITICAL |
| **False negative rate** | Judge missed something human caught | <5% for HIGH, <2% for CRITICAL |
| **False positive rate** | Judge flagged something human approved | <15% (higher tolerance — better to over-flag) |
| **Inter-rater reliability** | Consistency between human evaluators | Cohen's κ > 0.7 |

### 2. Use a Different Model for the Judge

Avoid shared blind spots. If your generator uses one model family, your judge should use a different one.

| Generator | Judge Options |
|-----------|--------------|
| GPT-4 / GPT-4o | Claude, Gemini, Llama-based |
| Claude | GPT-4, Gemini, Llama-based |
| Open-source (Llama, Mistral) | Any commercial model, or a different open-source family |

This doesn't eliminate shared failures (all LLMs share some biases), but it reduces correlated failure significantly.

### 3. Calibrate Continuously

The Judge's accuracy is not static. Track it weekly.

```
Week 1: Agreement 94% | FN 3% | FP 12%
Week 2: Agreement 93% | FN 4% | FP 11%
Week 3: Agreement 87% | FN 8% | FP 14%  ← drift detected, investigate
```

**Triggers for investigation:**

| Signal | Threshold | Action |
|--------|-----------|--------|
| Agreement drops >5% week-over-week | Immediate | Investigate — possible model update or data shift |
| FN rate exceeds tier threshold | Immediate | Increase human review sample, tighten guardrails |
| FP rate exceeds 25% | Next review cycle | Re-tune Judge prompt/criteria — alert fatigue risk |

### 4. Adversarial Test the Judge

Red-team the judge the same way you'd red-team the generator.

| Test | Method |
|------|--------|
| **Known-bad passthrough** | Submit known-harmful outputs and verify the Judge catches them |
| **Subtle policy violations** | Test edge cases — outputs that are technically compliant but violate intent |
| **Judge prompt injection** | Attempt to manipulate the Judge's evaluation via crafted generator outputs |
| **Consistency** | Submit the same output multiple times — does the Judge give consistent scores? |

### 5. Fail Safe

When the Judge is unavailable, degraded, or untrusted:

| Scenario | Response |
|----------|----------|
| Judge API timeout | Apply guardrails only + increase human review sample |
| Judge accuracy below threshold | Increase human review to 100% for affected risk tier |
| Judge model deprecated by provider | Switch to backup judge model (you should have one) |
| Judge cost exceeds budget | Sample-based evaluation (see [Cost and Latency](../extensions/technical/cost-and-latency.md)) |

**Never fail open.** If the Judge can't evaluate, tighten other controls. Don't skip evaluation.

---

## Integration with Existing Controls

| Existing Layer | Judge Assurance Addition |
|---------------|------------------------|
| **Guardrails** | Guardrails remain the first line — Judge assurance doesn't replace them |
| **Judge** | Add meta-evaluation metrics, calibration, adversarial testing |
| **Human Oversight** | Humans now serve dual purpose — deciding edge cases AND calibrating the Judge |
| **Metrics** | Add Judge-specific metrics to your monitoring dashboard |

---

## The Honest Assessment

Even with all these controls, you cannot prove the Judge is correct. You can only measure its accuracy against human judgment, track its consistency over time, and react when it drifts.

This is the same epistemic limitation identified in [The Verification Gap](../insights/the-verification-gap.md). The Judge doesn't solve it — it manages it. Judge Assurance ensures you know how well it's managing.
---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
