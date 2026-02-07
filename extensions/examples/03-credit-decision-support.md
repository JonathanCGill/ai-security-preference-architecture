# Worked Example: Credit Decision Support at Summit Lending

> Maximum controls for a CRITICAL-tier AI system in financial services.

This example shows full implementation for a high-stakes AI system that directly influences credit decisions. Compare with [Customer Service AI](01-customer-service-ai.md) (HIGH) and [Internal Doc Assistant](02-internal-doc-assistant.md) (MEDIUM) to see how risk tier scales controls.

---

## Important: Control Layer Separation

This example uses the layered control model:

| Layer | What It Does | Timing |
|-------|--------------|--------|
| **Guardrails** | Validate data, block malformed input | Inline |
| **Primary AI** | Generate risk assessment | Inline |
| **100% HITL** | Underwriter reviews and decides | Inline (human) |
| **LLM-as-Judge** | Quality assurance, bias detection | Async, after decision |

**Critical distinction:** The underwriter (HITL) is the decision-maker. The AI provides recommendations. The Judge evaluates the entire process after the fact for quality assurance, bias monitoring, and compliance evidence.

**The Judge does not block recommendations from reaching underwriters.**

---

## The Use Case

**System Name:** CreditAssist

**What it does:**
- Analyses loan applications and provides risk assessment
- Summarises applicant financial profile for underwriters
- Flags potential fraud indicators
- Recommends approval/denial with confidence score
- **Does NOT make final decisions** — underwriter always decides

**What it accesses:**
- Loan application data
- Credit bureau reports
- Bank statements (uploaded by applicant)
- Employment verification
- Internal fraud databases
- Historical loan performance data

**What it cannot do:**
- Automatically approve or deny loans
- Communicate directly with applicants
- Modify application data
- Access other applicants' data

**Scale:**
- 3,000 applications per day
- 200 underwriters using the system
- Peak: 500 applications/hour

**Technology:**
- GPT-4 via Azure OpenAI (isolated tenant)
- Custom fine-tuned model for fraud detection
- Deployed in Summit's private cloud (no public internet)
- Integrated with loan origination system

---

## Step 1: Risk Classification

### Assessment

| Factor | Assessment | Score |
|--------|------------|-------|
| **Decision Impact** | Influences credit decisions affecting consumers | CRITICAL |
| **Data Sensitivity** | Full financial profiles, SSN, income data | CRITICAL |
| **User Population** | Internal underwriters (but affects external consumers) | HIGH |
| **Autonomy Level** | Advisory only, but highly influential | HIGH |
| **Regulatory Scope** | ECOA, FCRA, CFPB, state lending laws, SR 11-7 | CRITICAL |
| **Reputational Risk** | Fair lending violations could be catastrophic | CRITICAL |

### Override Checks

| Question | Answer | Impact |
|----------|--------|--------|
| EU AI Act Annex III high-risk? | Yes (creditworthiness assessment) | → CRITICAL |
| Makes binding financial decisions? | No (advisory), but influences them | → HIGH minimum |
| Could result in discrimination claims? | Yes (fair lending risk) | → CRITICAL |
| Subject to model risk management (SR 11-7)? | Yes | → CRITICAL |

### Classification Decision

**Risk Tier: CRITICAL**

Rationale: CreditAssist directly influences decisions that affect consumers' access to credit. Fair lending violations, bias, or errors could result in regulatory enforcement, class action litigation, and severe reputational damage.

**Approval Required:** AI Governance Committee (CRO chair, CISO, CLO, business line head)

---

## Step 2: Control Architecture

![CreditAssist Control Architecture](../../images/example-credit-architecture.svg)

```
Loan Application
       │
       ▼
┌──────────────────┐
│ Input Guardrails │ ◄── Data validation, completeness, format
│  (Rules-based)   │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│   Primary AI     │ ◄── Generates risk assessment
│ (CreditAssist)   │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│Output Guardrails │ ◄── Format validation, prohibited language check
│  (Rules-based)   │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│   Underwriter    │ ◄── 100% HITL - Human makes decision
│   (Decision)     │
└────────┬─────────┘
         │
         ▼
     Decision Made
         │
         ▼
      Logged
         │
         ▼ (async)
┌──────────────────┐
│  LLM-as-Judge    │ ◄── Quality, bias, explainability review
│ (100% sampling)  │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ Compliance/QA    │ ◄── Reviews findings, regulatory evidence
│    Review        │
└──────────────────┘
```

**Why 100% HITL AND 100% Judge sampling?**
- HITL (underwriter): Required for every decision—regulatory mandate, accountability
- Judge (100%): Required for compliance evidence—bias monitoring, audit trail

These serve different purposes. The underwriter decides. The Judge provides assurance.

---

## Step 3: Guardrails Implementation (Inline)

### Input Guardrails

**Purpose:** Ensure application data is complete, valid, and properly formatted

```python
def validate_application(application: dict) -> Tuple[bool, str, list]:
    """
    Inline validation of loan application data.
    Returns (is_valid, error_message, issues_list)
    """
    issues = []
    
    # Required fields check
    required_fields = [
        "applicant_name", "ssn", "income", "employment_status",
        "loan_amount", "loan_purpose", "credit_score"
    ]
    for field in required_fields:
        if field not in application or application[field] is None:
            issues.append(f"Missing required field: {field}")
    
    if issues:
        return False, "Application incomplete", issues
    
    # Data format validation
    if not validate_ssn_format(application["ssn"]):
        issues.append("Invalid SSN format")
    
    if not isinstance(application["income"], (int, float)) or application["income"] < 0:
        issues.append("Invalid income value")
    
    if not isinstance(application["loan_amount"], (int, float)) or application["loan_amount"] <= 0:
        issues.append("Invalid loan amount")
    
    # Internal consistency checks
    if application.get("dti_ratio"):
        calculated_dti = calculate_dti(application)
        if abs(calculated_dti - application["dti_ratio"]) > 0.05:
            issues.append(f"DTI ratio inconsistency: stated {application['dti_ratio']}, calculated {calculated_dti}")
    
    # Injection check on text fields
    text_fields = ["employer_name", "loan_purpose_description", "notes"]
    for field in text_fields:
        if field in application and contains_injection_pattern(application[field]):
            log_security_alert("injection_attempt", application["application_id"], field)
            issues.append(f"Invalid characters in {field}")
    
    if issues:
        return False, "Data validation failed", issues
    
    return True, "", []
```

### Output Guardrails

**Purpose:** Ensure AI recommendation is properly formatted and doesn't contain prohibited content

```python
def validate_recommendation(recommendation: dict, application_id: str) -> Tuple[bool, str]:
    """
    Validate AI recommendation format and content.
    Does NOT evaluate quality or bias - that's the Judge's job (async).
    """
    
    # Required structure
    required_sections = [
        "summary", "strength_factors", "risk_factors",
        "recommendation", "confidence", "reasoning"
    ]
    for section in required_sections:
        if section not in recommendation:
            log_error("missing_section", application_id, section)
            return False, f"Missing required section: {section}"
    
    # Recommendation must be valid value
    valid_recommendations = ["APPROVE", "DENY", "REFER"]
    if recommendation["recommendation"] not in valid_recommendations:
        log_error("invalid_recommendation", application_id, recommendation["recommendation"])
        return False, "Invalid recommendation value"
    
    # Confidence must be numeric 0-100
    if not (0 <= recommendation.get("confidence", -1) <= 100):
        log_error("invalid_confidence", application_id)
        return False, "Invalid confidence value"
    
    # Check for explicitly prohibited language
    prohibited_patterns = [
        r"\b(race|racial|ethnicity)\b",
        r"\b(religion|religious)\b",
        r"\b(national origin|country of origin)\b",
        r"\b(sex|gender|pregnant)\b",
        r"\b(marital status|married|divorced)\b",
        r"\b(public assistance|welfare)\b",
    ]
    
    text_content = json.dumps(recommendation)
    for pattern in prohibited_patterns:
        if re.search(pattern, text_content, re.IGNORECASE):
            # Don't block - flag for immediate compliance review
            log_security_alert("prohibited_factor_language", application_id, pattern)
            flag_for_immediate_review(application_id, "prohibited_factor_language")
            # Still allow through - underwriter will see it, compliance alerted
    
    return True, ""
```

**Note:** Output guardrails check format and explicitly prohibited language. They do NOT evaluate bias, quality, or explainability—that's the Judge's role, performed async.

---

## Step 4: Underwriter Experience (100% HITL)

Every recommendation goes to a human underwriter. The AI advises; the underwriter decides.

### What the Underwriter Sees

```
═══════════════════════════════════════════════════════════════
APPLICATION #2026-01-15-003847
═══════════════════════════════════════════════════════════════

APPLICANT SUMMARY
─────────────────
Name: [REDACTED]
Loan Amount: $285,000
Loan Type: 30-year Fixed Mortgage
Property: Single Family Residence

AI ASSESSMENT
─────────────
Recommendation: APPROVE
Confidence: 78%

Strength Factors:
• Stable employment (8 years current employer)
• Credit score 742 (Good)
• DTI ratio 32% (within guidelines)
• Verified income supports loan amount

Risk Factors:
• Recent credit inquiry volume (4 in 90 days)
• Limited cash reserves (2.1 months)
• Previous late payment (36 months ago)

Reasoning:
The applicant demonstrates stable employment history and income
sufficient for the requested loan amount. Credit profile is good
with minor historical blemish. Recent credit inquiries warrant
verification of intent. Recommend approval with standard conditions.

Information Gaps:
• Source of down payment funds not verified
• Second income source documentation pending

═══════════════════════════════════════════════════════════════

YOUR DECISION
─────────────
[ ] APPROVE    [ ] APPROVE WITH CONDITIONS    [ ] DENY    [ ] REFER

If conditions or denial, specify reason:
_______________________________________________________________

Override AI recommendation?  [ ] Yes  [ ] No
If yes, document reasoning:
_______________________________________________________________

[SUBMIT DECISION]
```

### Underwriter Actions

| Action | What It Means |
|--------|---------------|
| Agree with AI | Underwriter concurs, decision logged |
| Override AI | Underwriter disagrees, must document reasoning |
| Request more info | Application held, not decided |
| Escalate | Complex case, senior underwriter review |

**Override rate is a key metric.** Too low suggests rubber-stamping. Too high suggests poor AI quality. Target: 10-20%.

---

## Step 5: LLM-as-Judge Implementation (Async)

### Purpose

The Judge reviews **completed decisions** (AI recommendation + underwriter action) for:
- Quality and accuracy of AI assessment
- Potential bias indicators
- Explainability and compliance
- Pattern detection across applications

**The Judge operates after the underwriter decides.** It provides assurance, not control.

### Sampling

**100% of applications are evaluated** for CRITICAL tier. This provides:
- Complete audit trail for regulators
- Statistical basis for bias monitoring
- Full quality assurance coverage

### Judge Evaluation Prompt

```
You are a quality assurance and fair lending evaluator for a credit decision support system. You are reviewing a COMPLETED loan decision for compliance and quality purposes.

CONTEXT:
- The AI provided a recommendation to a human underwriter
- The underwriter made the final decision
- Your evaluation is for compliance monitoring and quality assurance
- Fair lending laws prohibit discrimination based on protected characteristics

REVIEW THIS COMPLETED DECISION FOR:

1. AI RECOMMENDATION QUALITY
   - Was the recommendation well-reasoned?
   - Were the cited factors legitimate credit factors?
   - Was the confidence level appropriate?

2. EXPLAINABILITY
   - Is the reasoning clear and documentable?
   - Could this explanation be provided to the applicant if required?
   - Are all factors tied to specific application data?

3. POTENTIAL BIAS INDICATORS
   - Does the reasoning reference or imply prohibited factors?
   - Are there proxy variables that could correlate with protected classes?
   - Is the recommendation consistent with similar applications?

4. UNDERWRITER DECISION
   - Did the underwriter override the AI? If so, was the override reasonable?
   - Is the final decision consistent with the stated factors?

5. DOCUMENTATION COMPLETENESS
   - Is there sufficient documentation for regulatory examination?
   - Are all required factors documented?

APPLICATION SUMMARY:
"""
{application_summary_redacted}
"""

AI RECOMMENDATION:
"""
{ai_recommendation}
"""

UNDERWRITER DECISION:
"""
{underwriter_decision}
{override_reasoning if applicable}
"""

Respond with JSON:
{
  "recommendation_quality": 1-5,
  "quality_issues": ["list"],
  "explainability": "CLEAR" | "PARTIAL" | "UNCLEAR",
  "explainability_concerns": ["list"],
  "bias_indicators_detected": true/false,
  "bias_concerns": ["list"],
  "proxy_variable_concerns": ["list"],
  "override_assessment": "REASONABLE" | "QUESTIONABLE" | "N/A",
  "documentation_complete": true/false,
  "documentation_gaps": ["list"],
  "overall_assessment": "OK" | "REVIEW" | "ESCALATE",
  "recommended_action": "none" | "quality_review" | "fair_lending_review" | "compliance_escalation",
  "summary": "brief assessment"
}
```

### Processing Judge Findings

```python
def process_credit_judge_evaluation(evaluation: dict, application_id: str):
    """
    Route Judge findings for CRITICAL tier credit decisions.
    This is async quality assurance, not real-time blocking.
    """
    
    # All evaluations logged for audit trail
    log_judge_evaluation(application_id, evaluation)
    
    # Immediate escalation triggers
    if evaluation["bias_indicators_detected"]:
        alert_fair_lending_officer(application_id, evaluation)
        queue_for_compliance_review(application_id, priority="immediate")
        return
    
    if evaluation["override_assessment"] == "QUESTIONABLE":
        queue_for_senior_review(application_id, evaluation)
    
    if evaluation["overall_assessment"] == "ESCALATE":
        queue_for_compliance_review(application_id, priority="high")
        return
    
    if evaluation["overall_assessment"] == "REVIEW":
        queue_for_quality_review(application_id, priority="normal")
        return
    
    # OK - no action needed, but logged for audit
```

---

## Step 6: Bias Monitoring (Async)

Separate from the per-application Judge review, statistical bias monitoring runs daily:

### Daily Statistical Checks

```python
def daily_bias_monitoring():
    """
    Statistical analysis of approval rates across demographic groups.
    Uses proxy variables since protected class data not collected.
    """
    
    # Geographic proxies (zip code demographics from census)
    geographic_disparity = calculate_approval_rate_by_geography()
    if geographic_disparity["max_disparity_ratio"] < 0.80:
        alert_fair_lending_team("geographic_disparity", geographic_disparity)
    
    # Income tier analysis (ensure not discriminating against lower income)
    income_analysis = calculate_approval_rate_by_income_tier()
    
    # Credit score band analysis (ensure consistent treatment)
    credit_analysis = calculate_approval_rate_by_credit_band()
    
    # Override pattern analysis (are overrides biased?)
    override_analysis = analyze_override_patterns()
    if override_analysis["disparity_detected"]:
        alert_fair_lending_team("override_disparity", override_analysis)
    
    # Generate daily report
    generate_fair_lending_daily_report(
        geographic_disparity,
        income_analysis,
        credit_analysis,
        override_analysis
    )
```

### Thresholds

| Metric | Threshold | Action |
|--------|-----------|--------|
| Approval rate disparity (any group) | <80% of majority group | Immediate investigation |
| Override rate disparity | >20% difference between groups | Review within 48 hours |
| Denial reason concentration | >50% one reason for any group | Review within 1 week |

---

## Step 7: System Prompt

```
You are CreditAssist, a credit risk assessment system supporting loan underwriters at Summit Lending.

## Your Role
You analyse loan applications and provide risk assessments to help underwriters make informed decisions. You are advisory only. The underwriter makes all final decisions.

## Critical Rules

1. NEVER reference protected characteristics:
   - Race, colour, ethnicity, national origin
   - Religion
   - Sex, gender, pregnancy status
   - Marital status
   - Age (except as legitimate credit factor per ECOA)
   - Receipt of public assistance
   - Exercise of rights under Consumer Credit Protection Act

2. Use ONLY legitimate credit factors:
   - Credit history and score
   - Income and employment stability
   - Debt-to-income ratio
   - Loan-to-value ratio
   - Cash reserves and assets
   - Payment history
   - Length of credit history

3. Explain EVERY recommendation:
   - Cite specific data from the application
   - Tie each factor to the assessment
   - Be clear about uncertainty

4. Express appropriate confidence:
   - If data is incomplete, say so
   - If factors conflict, explain the conflict
   - Never express false certainty

5. Document fairly:
   - Apply consistent standards
   - Don't assume intent or character
   - Flag concerns, don't accuse

## Output Format
Structure every assessment as:
1. Application Summary (key facts)
2. Strength Factors (positive indicators with specific data)
3. Risk Factors (concerns with specific data)
4. Recommendation (APPROVE/DENY/REFER with confidence %)
5. Reasoning (how factors support recommendation)
6. Information Gaps (what additional info would help)

## Remember
An underwriter will review your assessment and make the final decision. Be thorough, objective, and explainable.
```

---

## Step 8: Evidence Package for Regulators

### Documentation Required

| Document | Regulator Interest | Update Frequency |
|----------|-------------------|------------------|
| Model Documentation (SR 11-7) | OCC, Fed | Annual + material changes |
| Fundamental Rights Impact Assessment | EU AI Act | Annual |
| Fair Lending Analysis | CFPB, state AGs | Monthly summary, daily monitoring |
| Independent Validation Report | OCC, Fed | Annual |
| Bias Testing Results | CFPB, ECOA | Quarterly formal, daily monitoring |
| Judge Evaluation Summary | All | Monthly |
| Override Analysis | OCC, CFPB | Monthly |

### Technical Evidence

| Evidence | Purpose | Generation |
|----------|---------|------------|
| Judge evaluation logs | Quality assurance evidence | 100% of applications |
| Statistical bias reports | Fair lending compliance | Daily automated |
| Override pattern analysis | Human-AI interaction | Weekly automated |
| Decision audit trail | Accountability | Every application |
| Model performance metrics | SR 11-7 compliance | Monthly |

### Regulatory Exam Preparation

When examiners arrive, provide:
1. Complete decision audit trail for sample period
2. Judge evaluation summaries showing quality assurance
3. Statistical bias monitoring reports showing no disparate impact
4. Override analysis showing underwriters engage meaningfully
5. Documentation of any issues found and remediation taken

---

## Step 9: Incident Response

### Playbook: Fair Lending Alert

**Trigger:** Judge detects bias indicators OR statistical monitoring threshold exceeded

**Immediate (0-1 hour):**
1. Alert Fair Lending Officer, CISO, CLO
2. Identify scope (how many applications potentially affected)
3. Preserve all logs and Judge evaluations
4. Do NOT suspend system (underwriters still deciding, AI is advisory)

**Investigation (1-24 hours):**
1. Root cause analysis
2. Review Judge findings for affected period
3. Compare AI recommendations vs underwriter decisions
4. Determine if actual harm occurred (was bias in AI recommendation, underwriter decision, or both?)

**Remediation (24-72 hours):**
1. If AI issue: Update prompt, retrain, revalidate
2. If underwriter issue: Retraining, supervision
3. Re-review affected applications
4. Remediate any harmed applicants

**Notification (as required):**
1. Internal: Board risk committee
2. External: Regulators if material (coordinate with CLO)

### Key Point

Because the Judge operates async and the underwriter always decides, a Judge-detected issue means we have time to investigate properly. We're not in a position where blocking the Judge means stopping business operations.

---

## Step 10: Costs

### Implementation (One-Time)

| Item | Cost | Notes |
|------|------|-------|
| Model documentation (SR 11-7) | $150,000 | External + internal |
| Fundamental Rights Impact Assessment | $75,000 | External + legal |
| Independent model validation | $200,000 | SR 11-7 requirement |
| Security architecture | $100,000 | Zero trust implementation |
| Guardrails development | $40,000 | Input/output validation |
| Judge development | $60,000 | Prompts, calibration, testing |
| Bias monitoring system | $100,000 | Statistical monitoring |
| LOS integration | $60,000 | Underwriter workflow |
| Training | $50,000 | 200 underwriters |
| Documentation | $35,000 | Policies, playbooks |
| **Total Implementation** | **$870,000** | |

### Ongoing (Annual)

| Item | Cost | Notes |
|------|------|-------|
| Primary AI inference | $110,000 | 3K apps/day |
| Judge inference | $110,000 | 100% sampling |
| Annual model validation | $150,000 | SR 11-7 requirement |
| Bias monitoring operation | $120,000 | Analyst + tools |
| Fair lending audit | $75,000 | External annual |
| HITL overhead | $0 | Underwriters already exist |
| Security monitoring | $80,000 | SIEM + alerting |
| Compliance review staffing | $180,000 | 2 FTE reviewing Judge findings |
| Documentation updates | $40,000 | Quarterly |
| **Total Annual** | **$865,000** | |

**Cost per Application: ~$0.79**

---

## Step 11: Lessons After First Year

### What Worked

1. **100% HITL maintained accountability** — Clear that humans decide, AI advises
2. **Async Judge caught issues early** — Two potential bias patterns identified before they became problems
3. **Statistical monitoring validated Judge** — Daily stats and per-application Judge findings aligned
4. **Regulators praised the model** — OCC specifically noted quality of documentation and monitoring
5. **Separation of concerns was clear** — Guardrails, AI, underwriter, Judge each had defined roles

### Challenges

1. **Initial Judge false positive rate** — Too aggressive on bias detection, caused alert fatigue
2. **Underwriter trust took time** — Some initially rubber-stamped, required training
3. **Cost higher than projected** — Validation and compliance staffing exceeded budget
4. **Integration complexity** — LOS integration took 3 months longer than planned

### Metrics After 12 Months

| Metric | Target | Actual |
|--------|--------|--------|
| Override rate | 10-20% | 15.3% |
| Approval rate disparity (worst group) | >80% | 87% |
| Judge escalation rate | <2% | 1.4% |
| Fair lending findings | 0 | 0 |
| Underwriter satisfaction | >4.0/5.0 | 4.2/5.0 |
| Security incidents | 0 | 0 |

---

## Summary

CreditAssist demonstrates CRITICAL-tier implementation with proper control separation:

| Layer | Function | Key Point |
|-------|----------|-----------|
| Guardrails | Data validation, format checks | Inline, fast, deterministic |
| Primary AI | Risk assessment, recommendations | Advisory only |
| Underwriter (HITL) | Final decision | 100%, always accountable |
| LLM-as-Judge | Quality assurance, bias detection | Async, 100% sampling |
| Bias Monitoring | Statistical analysis | Daily automated |
| Compliance Review | Review Judge findings | Human action on findings |

**Key insight:** At CRITICAL tier, the Judge's role is even more important as an assurance mechanism. It provides the evidence trail that regulators want to see. But it never blocks or gates the underwriter's work—the underwriter always sees the AI recommendation and always decides.

**The system is advisory, not autonomous.** This is a deliberate design choice that:
- Keeps humans accountable
- Satisfies regulatory requirements
- Allows the Judge to operate async without impacting operations
- Provides comprehensive audit trail

---

*This worked example is part of the AI Security Blueprint — Discussion Draft*
