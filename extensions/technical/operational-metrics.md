# AI Security Operational Metrics

How do you know if the framework is working? This document defines the metrics to track.

---

## Metrics Framework

### The Three Questions

Every metric should help answer one of these questions:

1. **Is the AI system working correctly?** (Quality metrics)
2. **Is the AI system secure?** (Security metrics)
3. **Is the control framework effective?** (Assurance metrics)

---

## Quality Metrics

### Q1. Output Accuracy

| Metric | Definition | Target by Tier | Alert Threshold |
|--------|------------|----------------|-----------------|
| **Hallucination rate** | % of outputs containing unsupported claims (detected by Judge) | CRITICAL: <1%, HIGH: <3%, MEDIUM: <5% | >2x target |
| **Grounding score** | % of claims that can be traced to source data | CRITICAL: >95%, HIGH: >90% | <target - 5% |
| **Factual error rate** | % of outputs with verifiable factual errors | CRITICAL: <0.5%, HIGH: <1% | >2x target |

### Q2. Output Quality

| Metric | Definition | Target | Alert Threshold |
|--------|------------|--------|-----------------|
| **User satisfaction** | Rating from user feedback (1-5) | >4.0 | <3.5 |
| **Task completion rate** | % of interactions that achieve user goal | >85% | <75% |
| **Escalation rate** | % of interactions escalated to human | Varies by tier | >2x baseline |
| **Baseline drift** | Deviation from known-good baseline outputs | <10% | >20% |

### Q3. Availability

| Metric | Definition | Target | Alert Threshold |
|--------|------------|--------|-----------------|
| **Uptime** | % of time system is available | >99.5% | <99% |
| **Response latency (p50)** | Median response time | <2s | >5s |
| **Response latency (p99)** | 99th percentile response time | <10s | >30s |
| **Error rate** | % of requests returning errors | <1% | >3% |

---

## Security Metrics

### S1. Guardrail Effectiveness

| Metric | Definition | Target | Alert Threshold |
|--------|------------|--------|-----------------|
| **Input guardrail block rate** | % of inputs blocked | Baseline ± 2σ | >3σ from baseline |
| **Output guardrail block rate** | % of outputs blocked | Baseline ± 2σ | >3σ from baseline |
| **False positive rate** | % of blocks that were incorrect (sampled) | <5% | >10% |
| **Bypass detection rate** | % of known-bad inputs blocked (adversarial testing) | >95% | <90% |
| **Time to guardrail update** | Days between new threat and guardrail update | <7 days | >14 days |

### S2. Attack Detection

| Metric | Definition | Target | Alert Threshold |
|--------|------------|--------|-----------------|
| **Prompt injection detection rate** | % of injection attempts detected (any layer) | >90% | <80% |
| **Mean time to detect (MTTD)** | Time from attack to detection | <1 hour | >4 hours |
| **Attack volume trend** | Week-over-week change in attack attempts | Track only | >50% increase |

### S3. Data Protection

| Metric | Definition | Target | Alert Threshold |
|--------|------------|--------|-----------------|
| **PII leakage incidents** | Count of PII in outputs not caught by guardrails | 0 | Any |
| **Cross-user leakage incidents** | Count of data appearing in wrong user's context | 0 | Any |
| **Context isolation verification** | % of sessions verified for isolation | 100% (CRITICAL) | <100% |

---

## Assurance Metrics

### A1. Judge Effectiveness

| Metric | Definition | Target | Alert Threshold |
|--------|------------|--------|-----------------|
| **Judge coverage** | % of interactions evaluated by Judge | Per tier target | <target |
| **Judge latency** | Time from interaction to evaluation | <5 min (CRITICAL), <1 hr (HIGH) | >2x target |
| **Judge accuracy** | % agreement with human reviewers (sampled) | >90% | <85% |
| **Finding rate** | % of evaluated interactions with findings | Track trend | >2x baseline |
| **Finding backlog** | Count of findings awaiting human review | <24 hrs worth | >48 hrs worth |

### A2. HITL Effectiveness

| Metric | Definition | Target | Alert Threshold |
|--------|------------|--------|-----------------|
| **Override rate** | % of AI recommendations overridden by HITL | Track trend | <5% (may indicate rubber-stamping) |
| **Decision time** | Average time for HITL to review and decide | Benchmark | <50% of benchmark |
| **Canary detection rate** | % of planted test cases caught by HITL | >95% (CRITICAL), >90% (HIGH) | <target |
| **Inter-reviewer agreement** | % agreement between different reviewers (sampled) | >85% | <75% |
| **Reviewer fatigue index** | Quality decline over shift (measured by canary performance) | No decline | Significant decline |

### A3. Control Coverage

| Metric | Definition | Target | Alert Threshold |
|--------|------------|--------|-----------------|
| **Systems with guardrails** | % of AI systems with guardrails deployed | 100% | <100% |
| **Systems with Judge** | % of HIGH+ systems with Judge coverage | 100% | <100% |
| **Control testing coverage** | % of controls tested in last quarter | 100% | <80% |
| **Audit findings open** | Count of open audit findings | 0 critical/high | Any critical |

---

## Operational Metrics

### O1. Cost

![Cost-Quality Frontier](../../images/cost-quality-frontier.svg)

| Metric | Definition | Target | Alert Threshold |
|--------|------------|--------|-----------------|
| **Cost per interaction** | Total AI cost / interactions | Budget | >120% of budget |
| **Judge cost ratio** | Judge cost / Primary model cost | <30% | >50% |
| **Cost variance** | Actual vs. forecast | ±10% | >25% variance |
| **Cost by risk tier** | Breakdown of cost across tiers | Track | Unexpected shift |

### O2. Capacity

| Metric | Definition | Target | Alert Threshold |
|--------|------------|--------|-----------------|
| **Rate limit utilisation** | % of rate limit consumed | <70% | >85% |
| **Queue depth** | Pending requests in queue | <100 | >1000 |
| **HITL queue depth** | Interactions awaiting human review | <8 hrs | >24 hrs |
| **Judge queue depth** | Interactions awaiting evaluation | <1 hr (CRITICAL) | >2 hrs |

### O3. Change Management

| Metric | Definition | Target | Alert Threshold |
|--------|------------|--------|-----------------|
| **Prompt changes per week** | Count of prompt modifications | Track | Unexpected spike |
| **Guardrail changes per week** | Count of guardrail updates | Track | Unexpected spike |
| **Model version changes** | Count of model version updates | Track | Unplanned change |
| **Rollback rate** | % of changes that required rollback | <10% | >20% |

---

## Metric Dashboards

### Executive Dashboard

For leadership review (monthly):

| Metric | This Period | Prior Period | Trend | Status |
|--------|-------------|--------------|-------|--------|
| AI system availability | | | | |
| Security incidents | | | | |
| Hallucination rate (CRITICAL systems) | | | | |
| User satisfaction | | | | |
| Cost vs. budget | | | | |
| Open audit findings | | | | |

### Operations Dashboard

For daily monitoring:

| Category | Key Metrics | Update Frequency |
|----------|-------------|------------------|
| Health | Uptime, latency, error rate | Real-time |
| Security | Guardrail blocks, attack volume | Real-time |
| Quality | Hallucination rate, user satisfaction | Daily |
| Assurance | Judge coverage, finding backlog | Daily |
| Cost | Daily spend, variance | Daily |

### Security Dashboard

For security team:

| Category | Key Metrics | Update Frequency |
|----------|-------------|------------------|
| Threats | Attack volume, injection attempts | Real-time |
| Controls | Guardrail effectiveness, bypass attempts | Hourly |
| Incidents | Open incidents, MTTD, MTTR | Real-time |
| Compliance | Control coverage, audit status | Weekly |

---

## Alerting Thresholds

### Critical Alerts (Immediate Response)

| Condition | Action |
|-----------|--------|
| PII leakage detected | Page on-call, invoke incident response |
| Uptime <95% for 15 minutes | Page on-call |
| Security incident detected | Page on-call, invoke incident response |
| CRITICAL system Judge coverage <100% | Page on-call |

### High Alerts (Response within 4 hours)

| Condition | Action |
|-----------|--------|
| Hallucination rate >2x target | Notify AI operations |
| Guardrail bypass detected | Notify security team |
| Cost >150% of daily budget | Notify AI operations |
| HITL queue >24 hours | Notify HITL team lead |

### Medium Alerts (Response within 24 hours)

| Condition | Action |
|-----------|--------|
| Baseline drift >20% | Create ticket for investigation |
| Judge accuracy <85% | Create ticket for recalibration |
| User satisfaction <3.5 | Create ticket for review |

---

## Metric Collection

### Data Sources

| Metric Category | Primary Source | Backup Source |
|-----------------|---------------|---------------|
| Quality | Judge evaluations, user feedback | HITL reviews |
| Security | Guardrail logs, SIEM | Incident database |
| Assurance | Judge system, HITL system | Audit logs |
| Operational | Platform metrics, cost APIs | Manual sampling |

### Sampling Requirements

| Risk Tier | Minimum Sample for Statistical Validity |
|-----------|----------------------------------------|
| CRITICAL | 100% (no sampling) |
| HIGH | 20% or 1000/day, whichever is greater |
| MEDIUM | 5% or 500/day, whichever is greater |
| LOW | 1% or 100/day, whichever is greater |

### Data Retention

| Metric Type | Granularity | Retention |
|-------------|-------------|-----------|
| Real-time alerts | Event-level | 90 days |
| Daily aggregates | Daily | 2 years |
| Weekly reports | Weekly | 5 years |
| Monthly reports | Monthly | 7 years |

---

## Reporting Cadence

| Report | Audience | Frequency | Key Content |
|--------|----------|-----------|-------------|
| Operational summary | AI operations | Daily | Health, incidents, anomalies |
| Security summary | Security team | Weekly | Threats, control effectiveness |
| Executive summary | Leadership | Monthly | KPIs, trends, risks |
| Audit report | Compliance | Quarterly | Control coverage, findings |
| Annual review | Board | Annually | Strategy, maturity, roadmap |

---

## Maturity Progression

### Level 1: Basic

- Uptime and error rate tracked
- Manual incident counting
- No systematic quality metrics

### Level 2: Developing

- Guardrail metrics tracked
- Basic quality metrics (user feedback)
- Manual reporting

### Level 3: Defined

- Full metric framework implemented
- Automated dashboards
- Regular reporting cadence

### Level 4: Managed

- Real-time alerting
- Trend analysis and forecasting
- Metrics-driven decision making

### Level 5: Optimising

- Predictive analytics
- Automated remediation
- Continuous improvement feedback loops

---

*AI Security Blueprint — Operational Metrics*
