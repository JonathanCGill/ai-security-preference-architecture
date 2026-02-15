# Behavioral Anomaly Detection — Operational Guide

> From metrics collection to actionable alerts.

## The Gap

The framework describes [Behavioral Anomaly Detection](/insights/behavioral-anomaly-detection.md) as a concept: aggregate signals to detect drift from normal. The [Operational Metrics](/extensions/technical/operational-metrics.md) extension describes what to measure.

This document bridges the gap: how to turn collected metrics into anomaly detection that feeds your SOC.

---

## Step 1: Establish Baselines

You can't detect anomalies without a definition of normal.

### Baseline Period

| System Maturity | Baseline Period | Rationale |
|----------------|----------------|-----------|
| New deployment (<1 month) | 2 weeks minimum | Need enough data for statistical significance |
| Established (1–6 months) | Rolling 30-day window | Captures normal variation |
| Mature (>6 months) | Rolling 30-day, seasonally adjusted | Accounts for weekly/monthly patterns |

### Baseline Metrics

| Metric | What "Normal" Looks Like | Measurement |
|--------|-------------------------|-------------|
| **Requests per user per hour** | Distribution of usage frequency | Mean, standard deviation, P95 |
| **Token volume per request** | Typical input/output length | Mean, P50, P95, P99 |
| **Guardrail block rate** | Percentage of requests blocked | Daily rate, 7-day rolling average |
| **Judge flag rate** | Percentage of responses flagged | Daily rate, 7-day rolling average |
| **Response latency** | Time to first token, time to completion | P50, P95, P99 |
| **Topic distribution** | What subjects users ask about | Top-N topic clusters, relative frequency |
| **Error rate** | Failed requests, timeouts, retries | Daily rate |
| **Unique users per day** | Active user count | Daily count, 7-day rolling average |

### Statistical Approach

For most metrics, a simple z-score against the rolling baseline is sufficient:

```
z = (observed_value - baseline_mean) / baseline_stddev
```

| z-score | Interpretation | Action |
|---------|---------------|--------|
| < 2.0 | Normal variation | No action |
| 2.0–3.0 | Unusual | Log, include in weekly review |
| 3.0–4.0 | Anomalous | Alert AI team, investigate |
| > 4.0 | Highly anomalous | Alert SOC, immediate investigation |

**For rate metrics** (block rate, flag rate): use control charts (SPC). Upper control limit = mean + 3σ.

**For count metrics** (requests per user): use Poisson or negative binomial models for low-count data.

---

## Step 2: Define Detection Rules

### User-Level Anomalies

| Rule | Detection | Likely Cause |
|------|-----------|-------------|
| Request volume spike (single user) | Requests > P99 baseline for that user | Automation, data extraction, or testing |
| Token volume spike (single user) | Output tokens > P99 | Attempting to extract large amounts of data |
| Guardrail block spike (single user) | >5 blocks in 1 hour | Probing for bypass, adversarial testing |
| New user, high volume | First-day usage > P95 of established users | Legitimate power user or compromised account |
| Topic shift | User's topic cluster changes significantly | Role change (legitimate) or account takeover |

### System-Level Anomalies

| Rule | Detection | Likely Cause |
|------|-----------|-------------|
| Global block rate increase | Block rate > UCL (3σ) | New attack pattern, model update, or guardrail misconfiguration |
| Global flag rate increase | Flag rate > UCL (3σ) | Model degradation, provider update, or emerging misuse pattern |
| Latency increase | P95 latency > 2x baseline | Provider issues, resource exhaustion, or DDoS |
| Error rate spike | Error rate > UCL (3σ) | Provider outage, config change, or infrastructure issue |
| Output distribution shift | Cosine similarity of topic distribution vs baseline < 0.8 | Model update, prompt injection campaign, or data drift |

### Model-Level Anomalies (Judge Assurance)

| Rule | Detection | Likely Cause |
|------|-----------|-------------|
| Judge agreement rate drop | Agreement < baseline - 2σ | Judge model update, generator model update, or calibration drift |
| Judge false positive spike | FP rate > UCL | Judge prompt degradation or input distribution shift |
| Judge latency increase | P95 > 2x baseline | Provider issues affecting judge model |

---

## Step 3: Alert and Respond

### Alert Routing

| Anomaly Type | Severity | Route To |
|-------------|----------|----------|
| User-level, single metric | Low | AI platform team (weekly review) |
| User-level, multiple metrics | Medium | SOC L1 for triage |
| User-level, high volume + blocks | High | SOC L2 + AI security |
| System-level, single metric | Medium | AI platform team (same-day review) |
| System-level, multiple metrics | High | SOC L2 + AI platform team |
| Model-level (judge drift) | Medium | AI security team |

### False Positive Management

Anomaly detection will generate false positives. Plan for this:

| Strategy | Implementation |
|----------|---------------|
| **Tune thresholds gradually** | Start at 4σ (fewer alerts), tighten to 3σ as you gain confidence |
| **Allowlisting** | Known batch jobs, automated testing, and power users get adjusted baselines |
| **Correlation** | Single-metric anomalies are logged; multi-metric anomalies are alerted |
| **Feedback loop** | Analysts mark alerts as TP/FP; use this to adjust thresholds quarterly |
| **Alert fatigue monitoring** | Track time-to-acknowledge; if it degrades, you have too many alerts |

---

## Step 4: Tooling

### Build vs. Buy

| Approach | When | Tools |
|----------|------|-------|
| **Extend existing SIEM** | You have Splunk/Sentinel/Elastic and AI logs are already ingested | SIEM detection rules + custom dashboards |
| **Dedicated AI monitoring** | High AI deployment density, need specialised detection | Galileo, Arize, Langfuse, WhyLabs |
| **Custom pipeline** | Unique requirements or cost constraints | Prometheus + Grafana + custom detectors |

### Minimum Viable Implementation

1. **Week 1:** Emit structured AI logs to your existing SIEM (see [SOC Integration](/extensions/technical/soc-integration.md))
2. **Week 2–3:** Collect baseline metrics (2-week minimum)
3. **Week 4:** Implement 3–5 highest-value detection rules (start with user-level volume anomalies and system-level block rate)
4. **Week 5–8:** Tune thresholds based on false positive rate
5. **Ongoing:** Add detection rules as new patterns emerge

---

## Related

- [Behavioral Anomaly Detection](/insights/behavioral-anomaly-detection.md) — The concept
- [Operational Metrics](/extensions/technical/operational-metrics.md) — What to collect
- [SOC Integration](/extensions/technical/soc-integration.md) — Where alerts go
- [Judge Assurance](/core/judge-assurance.md) — Model-level anomaly detection
