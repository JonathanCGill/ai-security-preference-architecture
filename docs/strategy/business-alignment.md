# Business Alignment

*How to match AI investments to business problems — and why most mismatches are strategic, not technical.*

> Part of [AI Strategy](./)

---

## The Alignment Problem Nobody Talks About

Most AI strategy documents start with technology: models, platforms, architectures. Then they look for business problems to attach them to.

This is backwards.

Business strategy asks: **What problems do we need to solve, and what's the best way to solve them?** AI is one possible answer. Often it's not the best one. The organisations that succeed with AI are the ones that start with the problem and work backwards to the solution — including the possibility that the solution doesn't involve AI at all.

The framework's [first control](../insights/the-first-control.md) makes this point for security. The same principle applies to strategy: the most effective AI decision you'll make is often choosing *not* to use AI for a particular problem.

---

## What Already Exists

Business-IT alignment is not a new problem. Decades of models, maturity assessments, and strategy tools already exist — and AI strategy should build on them, not ignore them. This section covers what's out there, what works, and how to use it without drowning in frameworks.

### The Foundational Theory

**Henderson & Venkatraman's Strategic Alignment Model (SAM, 1993)** is where all of this starts. It maps four domains — business strategy, IT strategy, organisational infrastructure, and IT infrastructure — and shows that alignment can flow in multiple directions. Business strategy can drive IT (the traditional view), but IT capabilities can also reshape business strategy. This second direction matters for AI: a new capability like large language models doesn't just serve existing strategy — it enables strategies that weren't previously possible.

SAM is conceptual, not operational. You won't run it as a workshop exercise. But the insight that **alignment is bidirectional** — business shapes AI strategy, and AI capabilities reshape business strategy — is the single most important idea in this space. Organisations that treat AI strategy as purely top-down ("here's our business strategy, now find AI use cases") miss the opportunities that only become visible when you understand what AI can actually do.

**Luftman's Strategic Alignment Maturity Model (SAMM)** makes SAM practical. It scores alignment across six dimensions — communications, value measurement, governance, partnership, technology scope, and skills — at five maturity levels. Unlike most alignment models, SAMM is survey-based and produces actionable results: you get a score, you see which dimensions are weak, and you build a plan. It's still actively used — recent studies in healthcare (2024) and government (2025) used it to diagnose alignment gaps, consistently finding that governance and communications are the weakest dimensions.

If you want to know how well your organisation aligns IT to business strategy *before* layering AI on top, SAMM is a good starting point. There's no point building an AI strategy on an IT-business alignment that doesn't work.

### Strategy Execution Tools

These are the day-to-day tools practitioners use to connect strategy to action. None are AI-specific, but all are used to manage AI initiatives in practice:

| Tool | What It Does | AI Strategy Use |
|------|-------------|-----------------|
| **Balanced Scorecard (BSC)** | Translates strategy into objectives across four perspectives: Financial, Customer, Internal Process, Learning & Growth. Strategy Maps show the cause-and-effect chain between them. | Maps AI investments to business outcomes across all four perspectives — not just cost savings. Forces you to articulate *how* an AI investment leads to a financial result. |
| **OKRs** | Cascades objectives and measurable key results from company level to teams. Quarterly cycle. | Bridges strategy and execution — company-level OKR ("reduce customer churn by 15%") cascades to AI team OKR ("deploy churn prediction model with 80% accuracy by Q2"). |
| **TOGAF** | Enterprise architecture framework. Aligns IT infrastructure with business needs through a structured development method. | Being extended with AI architecture phases — business architecture identifies where AI fits, data architecture ensures governance, technology architecture maps the platform. |
| **SAFe Lean Portfolio Management** | Connects strategy to execution at scale through value stream funding, portfolio Kanban, and lean business cases. | Provides the portfolio governance layer — how AI initiatives compete for funding, how they're prioritised against non-AI work, and how progress is tracked. |
| **Value Stream Mapping** | Maps information and material flow through a process to identify waste and improvement opportunities. | Reveals where AI creates the most impact *before* you invest. The Lean Enterprise Institute calls it "your missing AI superpower" — and they're right. |

**BSC and OKRs are complementary, not competing.** BSC frames the strategic picture; OKRs drive quarterly execution against it. Most mature organisations use both — BSC for "what does success look like across all dimensions" and OKRs for "what are we doing this quarter to get there."

### AI-Specific Alignment Models

The consulting firms and standards bodies have produced AI-specific frameworks. The useful ones share a common message: **start with the business strategy, not the technology.**

**McKinsey's "Rewired" framework (2023)** identifies six dimensions for AI transformation: strategy, talent, operating model, technology, data, and adoption/scaling. The key finding is that organisations must be competent across *all six* — weakness in any one undermines the rest. Their 2025 survey found that only 6% of organisations qualify as "AI high performers." The gap wasn't technology. It was talent, operating model, and scaling.

**Gartner's AI Strategy & Roadmap** emphasises bidirectional alignment — changes in business strategy should update the AI strategy, and AI capabilities should inform business strategy. They also provide an AI Maturity Model across seven pillars (strategy, product, governance, engineering, data, operating models, culture) with five levels. Their sobering finding: only 1 in 5 AI initiatives achieves ROI.

**Deloitte's AI Strategy & Governance** framework starts from three principles: begin with the business "north star," develop an enterprise-wide AI strategy (not disconnected use cases), and balance efficiency targets with value-creation targets. Their 2026 survey found that organisations with an enterprise-wide AI strategy are 3x more likely to report transformative results.

### AI Governance and Maturity Standards

For organisations that need formal governance structures:

| Standard | What It Provides |
|----------|-----------------|
| **NIST AI Risk Management Framework (AI RMF)** | Four functions — Govern, Map, Measure, Manage — for AI risk management. The primary voluntary standard in the U.S. Updated for generative AI in 2024. |
| **ISO/IEC 42001:2023** | The first certifiable AI management system standard. PDCA cycle. Aligns with ISO 27001 (security) and ISO 27701 (privacy), so organisations with existing certifications can fold AI governance into their audit cadence. |
| **MITRE AI Maturity Model** | Six pillars, 20 dimensions, five readiness levels. Particularly strong in government and defence contexts. Provides a structured self-assessment for AI readiness. |

These are governance tools, not strategy tools. They help you manage AI responsibly — they don't tell you which AI to build. But if your organisation lacks AI governance, these are where to start, and they connect to the strategic layer through risk appetite and capability assessment.

### How These Fit Together

These frameworks are **complementary, not competing.** A mature organisation might use:

1. **BSC + OKRs** to frame business strategy and drive execution
2. **TOGAF or SAFe LPM** to connect strategy to architecture and portfolio management
3. **McKinsey Rewired or Gartner AI Roadmap** to structure the AI transformation programme
4. **Gartner or MITRE AI Maturity Model** to assess readiness and track progress
5. **NIST AI RMF or ISO/IEC 42001** for governance infrastructure
6. **Value Stream Mapping** to find where AI creates genuine value

Most organisations don't need all of these. The point is that **strategic alignment is a solved problem at the model level.** The hard part isn't finding the right framework — it's doing the honest assessment work the frameworks require.

Which brings us to this framework's contribution. The models above tell you how to align strategy. They don't tell you how to evaluate whether a specific AI initiative is the right thing to build, given your actual constraints. That's what the four questions below are for.

---

## The Alignment Model

![Business-AI Alignment Model](../images/strategy-alignment-model.svg)

Alignment requires honest answers to four questions, in order:

| # | Question | What It Reveals |
|---|----------|-----------------|
| 1 | **What business problem are we solving?** | Whether there's a real problem, or a technology looking for a purpose |
| 2 | **Does AI provide a genuine advantage over alternatives?** | Whether AI is the right tool, or just the fashionable one |
| 3 | **Can we deliver it?** Given our data, skills, and constraints | Whether the plan is feasible, or aspirational |
| 4 | **Can we operate it safely?** Given the risk profile and required controls | Whether we can sustain it, not just launch it |

Most organisations skip questions 2-4. They identify a problem (question 1), assume AI is the answer, and discover the constraints during implementation — when changing direction is expensive.

---

## Question 1: What Business Problem Are We Solving?

This sounds obvious. It isn't. In practice, AI initiatives often start from one of these positions:

| Starting Position | The Problem | Example |
|-------------------|-------------|---------|
| **Technology push** | "We should be using AI" — no specific problem identified | Board directive to "implement AI" across the business |
| **Vendor pull** | Software vendor adds AI features; business adopts them without analysing need | CRM vendor adds "AI-powered insights"; sales team enables it by default |
| **Competitor reaction** | Competitor announces AI initiative; organisation responds without understanding their own context | Competitor launches AI chatbot; your CEO asks "where's ours?" |
| **Solution in search of a problem** | Team builds impressive prototype; now needs a business case | ML team builds document classifier; searches for documents to classify |

None of these are business problems. They're impulses. A business problem sounds like:

- "Customer service resolution takes 14 minutes on average and costs £8.50 per interaction. We need to reduce this by 30% without sacrificing satisfaction."
- "Regulatory reporting requires 200 hours per quarter of manual data compilation. We need to reduce the labour requirement and the error rate."
- "Fraud detection misses 15% of cases under £500. We need to improve detection at the low-value end without increasing false positives above 5%."

Each of these can be evaluated against multiple solutions — AI, automation, process redesign, additional staffing. AI may or may not be the best answer.

### The "Is This Really an AI Problem?" Test

| Characteristic | Suggests AI | Suggests Not AI |
|---------------|-------------|-----------------|
| Requires pattern recognition across unstructured data | Yes | |
| Can be solved with clear business rules | | Yes — use rules engine |
| Needs natural language understanding | Yes | |
| Has a well-defined lookup/retrieval pattern | | Yes — use search/database |
| Requires reasoning over ambiguous inputs | Yes | |
| Needs exact, reproducible results every time | | Yes — use deterministic logic |
| Operates at scale where human review of every case is impractical | Yes | |
| Has clear decision trees with bounded inputs | | Yes — use workflow automation |
| Benefits from continuous learning and adaptation | Yes | |
| Requires perfect accuracy with legal liability | | Possibly not — see risk tier |

**The honest answer is often "some AI, some not."** A customer service system might use AI for initial query understanding and routing, but deterministic logic for account actions. The strategy should be precise about which components benefit from AI, not assume the entire process should be AI-driven.

---

## Question 2: Does AI Provide a Genuine Advantage?

Assuming the problem is real, does AI solve it better than alternatives?

### The Alternatives Test

For any proposed AI use case, identify at least two non-AI alternatives and compare honestly:

| Factor | AI Solution | Alternative A | Alternative B |
|--------|-------------|---------------|---------------|
| **Accuracy** | How accurate? What's the error mode? | | |
| **Cost** | Build + operate + controls | | |
| **Time to value** | Including data prep, training, testing | | |
| **Maintenance** | Model drift, retraining, guardrail tuning | | |
| **Risk** | What can go wrong? What's the blast radius? | | |
| **Controls required** | What tier? What control overhead? | | |
| **Skills required** | Do we have them? Can we get them? | | |
| **Explainability** | Can we explain decisions to regulators/customers? | | |

**Real-world example — document classification:**

| Factor | AI Classifier | Rules-Based | Human Review |
|--------|---------------|-------------|--------------|
| Accuracy | 92% (with ongoing drift) | 85% (stable) | 98% (but slow) |
| Cost (year 1) | £180K (build + infra + controls) | £40K (development) | £350K (12 FTE) |
| Cost (year 2+) | £60K (run + retrain) | £15K (maintenance) | £360K (staff + inflation) |
| Time to value | 4-6 months | 2 months | Immediate |
| Risk profile | MEDIUM — needs Judge, logging | LOW — deterministic | LOW — human decisions |
| Skills required | ML engineers, data scientists | Developers, domain experts | Domain experts only |

In this example, the AI solution has a clear cost advantage over human review at scale, but the rules-based approach is cheaper and faster for year 1. The strategic question is volume: at what document volume does the AI investment pay back? If it's 10,000 documents per year, rules-based wins. If it's 500,000, AI wins.

**The framework's risk tiers are relevant here.** A MEDIUM-tier AI deployment requires Judge evaluation, logging, and periodic human review. These controls have real cost. A LOW-tier rules-based system needs basic guardrails and logging. The control overhead should be part of the cost comparison, not an afterthought.

---

## Question 3: Can We Deliver It?

This is where most strategies break. The answer depends on three constraints that organisations routinely underestimate:

### Constraint 1: Data

Covered in detail in [Data Reality](data-reality.md). The summary:

- **Do we have the data?** Not "could we get it" — do we have it now, accessible, in usable form?
- **Is it good enough?** Not theoretically — has anyone actually looked at it?
- **Can we use it?** Legally, contractually, ethically?

### Constraint 2: Skills

Covered in detail in [Human Factors](human-factors.md). The summary:

- **Do we have the technical skills** to build, deploy, and maintain this?
- **Do we have the domain skills** to validate outputs and operate controls?
- **Can the operating team learn** what they need in the time available?

### Constraint 3: Organisational Readiness

This is the constraint nobody measures:

| Readiness Factor | Question | Why It Matters |
|------------------|----------|----------------|
| **Governance** | Do we have a functioning AI governance process? | Without governance, risk classification doesn't happen, controls don't get implemented |
| **Risk appetite** | Has leadership articulated what level of AI risk is acceptable? | Without stated appetite, every deployment is a negotiation |
| **Change capacity** | Can the organisation absorb another change programme? | AI projects compete for the same people as everything else |
| **Process maturity** | Are the business processes we're augmenting well-defined and documented? | You can't automate a process nobody understands |
| **Measurement** | Do we measure the baseline we're trying to improve? | Without a baseline, you can't demonstrate value or detect degradation |
| **Incident response** | Do we know what to do when an AI system misbehaves? | The [AI incident playbook](../extensions/templates/ai-incident-playbook.md) exists. Has anyone read it? |

### Real-World Scenario: The Governance Gap

A mid-sized financial services firm decides to build an AI-powered fraud detection system. The business case is strong — current manual review misses 15% of low-value fraud.

**What they have:**
- 3 years of labelled transaction data
- A data science team of 4 people
- Board-level support and budget

**What they don't have:**
- An AI governance committee (or any AI governance process)
- A risk classification methodology
- Anyone in security who understands AI-specific risks
- An incident response playbook for AI failures
- A human review process for AI-flagged transactions

The data and technical capability exist. The organisational readiness doesn't. This system would be classified as HIGH or CRITICAL under the framework's [risk tiers](../core/risk-tiers.md) — it makes automated decisions about financial transactions. At those tiers, the framework requires 20-100% Judge evaluation, 1-4 hour review SLAs, expert human reviewers, and 3-7 year immutable logging.

**Strategic options:**

| Option | Approach | Timeline | Risk |
|--------|----------|----------|------|
| **A: Build governance first** | Establish AI governance, then build the system | 6-9 months to first deployment | Slower time to value; board may lose patience |
| **B: Start low, build up** | Deploy as decision *support* (MEDIUM tier), build governance in parallel, upgrade to automated decisions later | 3 months to support tool, 9 months to automation | Lower initial risk; longer to full value |
| **C: Build and govern simultaneously** | Start both workstreams immediately | 4-6 months | Risk of governance being shaped to fit the system rather than the other way round |
| **D: Outsource** | Use a vendor solution with built-in controls | 2-3 months | Vendor dependency; less customisation; governance still needed |

Option B is most commonly the right answer, because it generates value while building capability. The framework's tiered approach directly supports this — the same system can start at MEDIUM (decision support, human reviews every output) and graduate to HIGH or CRITICAL (automated decisions) as controls mature. This is covered in [Progression](progression.md).

---

## Question 4: Can We Operate It Safely?

"Can we build it?" and "can we run it?" are different questions.

### Build vs. Run Costs

The AI industry dramatically underrepresents operational costs:

| Cost Category | Build Phase | Run Phase (Annual) |
|---------------|-------------|---------------------|
| Model development/fine-tuning | High | Low (periodic retraining) |
| Infrastructure | Setup | Ongoing compute, storage |
| Guardrails | Configuration | Tuning, updating, false positive management |
| Judge evaluation | Prompt development | Compute (per-interaction cost at scale) |
| Human review | Queue design | Staff time (this never goes away) |
| Monitoring | Dashboard setup | Alert management, investigation |
| Compliance | Initial risk assessment | Ongoing evidence, audits, regulatory engagement |
| Incident response | Playbook creation | Incident investigation, remediation |

**The framework's control requirements scale with risk tier.** This means the operational cost of a CRITICAL-tier system is substantially higher than a LOW-tier system — not just because of the technology, but because of the human processes required to support it.

### The Human Review Reality

The governance operating model's [staffing model](../extensions/regulatory/ai-governance-operating-model.md) provides the formula:

```
FTE = (Volume × Sample Rate × Review Time) / Working Hours
```

For a CRITICAL-tier system processing 50,000 interactions per day with 100% Judge evaluation and 5% human review:

- 50,000 × 5% = 2,500 human reviews per day
- At 10 minutes per review = 25,000 minutes = ~416 hours per day
- At 7.5 productive hours per person = **55 FTE**

That's the operational reality of a CRITICAL-tier high-volume system. Most strategies don't model this.

For a MEDIUM-tier system processing 50,000 interactions per day with 10% Judge evaluation and 1% human review:

- 50,000 × 1% = 500 human reviews per day
- At 5 minutes per review = 2,500 minutes = ~42 hours per day
- At 7.5 productive hours per person = **6 FTE**

The difference between MEDIUM and CRITICAL isn't just a label. It's a 9x staffing difference for human oversight alone.

**Strategic implication:** The risk tier determines operational cost. If the strategy requires a CRITICAL-tier deployment but the budget assumes MEDIUM-tier operations, the strategy will fail. Either reduce the risk tier (by reducing autonomy, limiting data access, or keeping humans in the loop) or fund the controls properly.

---

## Aligning Products to Constraints

The framework creates constraints. Business strategy creates ambitions. Alignment means accepting three realities:

### Reality 1: Constraints Are Not Optional

If a use case is classified as HIGH or CRITICAL, the controls are non-negotiable. This isn't bureaucracy — it's risk management. The controls exist because the [incident data](../insights/state-of-reality.md) shows what happens without them.

However, constraints can be managed by adjusting the deployment:

| Adjustment | Effect on Risk Tier | Example |
|------------|---------------------|---------|
| Remove write access | Tier may decrease | AI recommends actions but doesn't execute them |
| Keep internal only | Tier may decrease | Internal tool instead of customer-facing |
| Add human review before action | Tier may decrease | Human approves every AI decision before execution |
| Reduce data sensitivity | Tier may decrease | Use aggregated data instead of individual records |
| Limit scope | Tier may decrease | Handle specific query types, not all queries |

Each of these reduces capability. Strategy is about finding the combination that delivers sufficient value at acceptable risk.

### Reality 2: Constraints Can Be Changed

The framework is not permanent. If a strategic priority genuinely requires changing a control requirement, that's a governance decision. The process:

1. **Document the strategic case.** Why does this use case need to exceed current constraints?
2. **Identify the specific constraint.** Which control, at which tier?
3. **Propose compensating controls.** What else will you do to manage the risk?
4. **Get governance approval.** The AI governance committee (or equivalent) decides.
5. **Monitor closely.** The exception is time-bound and subject to review.

This is how mature organisations handle the tension between innovation and control. It's not about ignoring the framework — it's about evolving it with evidence.

### Reality 3: Some Constraints Are External

Regulatory requirements are not negotiable within the organisation. If the EU AI Act classifies your use case as high-risk, you need the controls the Act specifies — regardless of what this framework says. The framework's [regulatory crosswalks](../extensions/regulatory/) help identify where external constraints apply.

---

## Common Strategic Mistakes

| Mistake | What Happens | Better Approach |
|---------|-------------|-----------------|
| **AI strategy without business strategy** | Technology investment without clear business outcome | Start with business problem, evaluate AI as one option |
| **Ignoring control costs in business case** | Business case looks attractive until operational costs are included | Model full lifecycle cost including controls by tier |
| **Assuming data exists and is ready** | Project stalls at data preparation | Assess data reality *before* committing to approach |
| **Pilot that can't scale** | Successful pilot in controlled conditions; fails at scale because controls weren't designed for volume | Design controls for target scale from the start |
| **Building before governance exists** | System deployed without risk classification or controls | Establish minimum governance before first deployment ([Quick Start](../QUICK_START.md)) |
| **Copying competitor strategy** | Different data, different skills, different risk appetite — different outcome | Assess your own constraints honestly |
| **"We'll add security later"** | Security retrofit is always more expensive and less effective | Include security controls in initial design |

---

## A Strategic Decision Template

For each proposed AI initiative, answer honestly:

| Question | Answer | Evidence |
|----------|--------|----------|
| What specific business problem does this solve? | | Measurable current state |
| What are two non-AI alternatives? | | Costed comparison |
| What data does it require and do we have it? | | Data audit results |
| What risk tier would this be classified at? | | [Risk Tiers](../core/risk-tiers.md) |
| What controls does that tier require? | | [Control Matrix](../core/risk-tiers.md#control-matrix) |
| What's the operational cost of those controls? | | Staffing model, infrastructure |
| Do we have the skills to build and operate this? | | [Human Factors](human-factors.md) assessment |
| What's the fallback if it fails? | | [PACE](../PACE-RESILIENCE.md) |
| Who will operate this day-to-day? | | Named team, not "TBD" |
| What does success look like, measurably? | | KPIs with baselines |

If any answer is "we don't know yet" — that's your next step, not building the system.

---

*AI Runtime Behaviour Security, 2026 (Jonathan Gill).*
