# From Strategy to Production

*The guided path from "we have a business problem" to "we have a governed AI system in production" — and how to keep it honest after launch.*

> Part of [AI Runtime Behaviour Security](../)

---

## What This Section Does

The rest of this framework tells you **how to secure AI systems**. This section tells you **how to get there** — from the first conversation about a business problem through to a system running safely in production with continuous monitoring.

Ten articles, in order. Each one answers a specific question that must be resolved before the next step makes sense. Skip one and the downstream decisions are built on assumptions instead of evidence.

---

## The Progression

![AI Strategy Landscape](../../images/strategy-landscape.svg)

### Phase 1: Is This the Right Problem — and the Right Solution?

Before committing to AI, confirm that the problem is real and that AI is genuinely the right answer.

| # | Article | Question It Answers |
|---|---------|-------------------|
| 1 | [Business Alignment](business-alignment.md) | Is this a real business problem worth solving — and how will we know it worked? |
| 2 | [Use Case Filter](use-case-filter.md) | Is AI the right tool? If not, what is — rules, RPA, search, traditional ML? |

Most AI failures start here. The problem is vague, the success criteria are missing, or AI was chosen because it's available rather than because it's appropriate. The [Use Case Filter](use-case-filter.md) provides a structured seven-question decision flow with five exits. The first exit that fits is the answer — don't keep going just because you want to reach the AI options.

**Gate:** You have a confirmed business problem with measurable success criteria, and the filter has determined that AI (of a specific kind) is the right approach. If the filter pointed elsewhere, exit to standard SDLC.

---

### Phase 2: Can We Actually Build and Run This?

AI is the right answer. Now confirm that the organisation has the platform, data, skills, and maturity to deliver it safely.

| # | Article | Question It Answers |
|---|---------|-------------------|
| 3 | [Platform and Patterns](platform-and-patterns.md) | Which approved platforms, trusted models, and proven patterns will this run on? |
| 4 | [Data Reality](data-reality.md) | Does the data exist, is it accessible, is it clean enough, and is it legal to use? |
| 5 | [Human Factors](human-factors.md) | Do we have the skills to build, operate, and oversee this — and what happens when humans over-trust the AI? |
| 6 | [Progression](progression.md) | Are we trying to jump from "no AI" to "autonomous AI" without the intermediate steps? |

[Platform and Patterns](platform-and-patterns.md) constrains the solution to approved infrastructure — standardised platforms, curated models, proven reference architectures, and consistent data access. This is what makes governance affordable: every system built on the same patterns gets the same logging, the same monitoring, and the same operational playbooks.

[Data Reality](data-reality.md) is the constraint most often ignored. Your data determines your strategy more than your ambition does.

[Human Factors](human-factors.md) is the constraint nobody wants to audit. If the HITL reviewers don't have the domain expertise to catch AI errors, the oversight is theatre.

[Progression](progression.md) prevents the most common strategic failure: skipping from low-risk experiments to high-risk autonomous systems without building the operational competence in between.

**Gate:** You have an approved platform and pattern, confirmed data readiness, identified skill gaps with remediation plans, and a progression position that supports the intended level of AI autonomy.

---

### Phase 3: What Exactly Are We Building — and What Are the Risks?

Define the system precisely enough to classify its risk and design proportionate controls.

| # | Article | Question It Answers |
|---|---------|-------------------|
| 7 | [Framework Tensions](framework-tensions.md) | Where will the framework help, where is it silent, and where will it actively constrain our choices? |
| 8 | [Use Case Definition](use-case-definition.md) | What decisions will the AI make, what data does it touch, who sees the output, and what happens when it's wrong? |

[Use Case Definition](use-case-definition.md) asks ten questions that directly determine every downstream control requirement. Decision authority, reversibility, data sensitivity, audience, scale, regulatory context — each answer feeds the risk classification. "TBD" in the definition means "unknown" in the risk profile, which means "wrong" in the control specification.

[Framework Tensions](framework-tensions.md) is honest about where the framework supports strategy, where it's silent (retirement processes, competitive dynamics), and where it actively constrains choices (edge cases, high-autonomy deployments). Understanding these tensions prevents surprises during implementation.

**Gate:** A complete use case definition (no TBDs), a scored risk profile with a confirmed tier, and a control specification that matches the tier.

---

### Phase 4: Build, Deploy, Govern — and Keep Governing

Implement the system and its controls together, deploy with appropriate caution, and establish the monitoring that keeps the system honest over time.

| # | Article | Question It Answers |
|---|---------|-------------------|
| 9 | [From Idea to Production](idea-to-production.md) | What are the stages, gates, owners, and outputs from first commit to ongoing governance? |
| 10 | [The Thread](the-thread.md) | How does the whole lifecycle connect — and how do monitoring signals feed back into reassessment? |

[From Idea to Production](idea-to-production.md) defines eight stages with explicit outputs and gates: strategic alignment, use case definition, tool selection, risk classification, control design, build and test, deploy and operate, ongoing governance. It's the operational process that turns the earlier articles into action.

[The Thread](the-thread.md) adds what most lifecycles miss: the **return loop**. Monitoring signals get triaged as operational (fix in place) or strategic (return to an earlier phase). Scope creep returns you to definition. Risk profile changes return you to classification. Technology mismatches return you to the filter. Without this loop, a system that was correctly classified at launch becomes an incorrectly classified system over time.

**Gate:** There is no final gate. Ongoing governance is continuous. The system is monitored, the use case definition is maintained as a living document, and the return loop triggers reassessment when reality diverges from design.

---

## The Full Sequence

![From Strategy to Production — Full Sequence](../../images/strategy-full-sequence.svg)

---

## Where Organisations Are

Most organisations sit in one of four positions on the AI adoption curve. The articles in this section apply differently depending on where you are:

| Position | Description | Where to Start | Key Risk |
|----------|-------------|---------------|----------|
| **Exploring** | Pilots, experiments, proving value | Articles 1–2: confirm the problem is real and AI is appropriate | Innovation theatre — pilots that never become production |
| **Scaling** | Proven use cases being rolled out | Articles 3–6: standardise platforms and validate readiness | Scaling without controls — operational surprises at volume |
| **Embedding** | AI integrated into core processes | Articles 7–10: rigorous definition, classification, and governance | Dependency without resilience — single points of failure |
| **Transforming** | Business model dependent on AI | All articles, with emphasis on The Thread | Regulatory exposure — the governance loop is the safety net |

---

## Key Principles

Five principles run through every article:

**1. Strategy must survive contact with constraints.**
An AI strategy that ignores data quality, team skills, platform limitations, or regulatory requirements isn't a strategy. It's a wish list.

**2. Not every problem is an AI problem.**
The [first control](../insights/the-first-control.md) is choosing the right tool. The [Use Case Filter](use-case-filter.md) makes that discipline operational. Sometimes the right tool is a rules engine, and that's the correct answer.

**3. Standardise the how, not the what.**
Teams get freedom to solve novel business problems. They don't get freedom to invent novel infrastructure. [Platform and Patterns](platform-and-patterns.md) explains why this makes governance affordable.

**4. Start where you are, not where you want to be.**
The most dangerous strategies assume capabilities the organisation doesn't have. [Progression](progression.md) builds competence through progressive risk, not leap-of-faith deployments.

**5. The lifecycle is a loop, not a line.**
A system that was correctly classified at launch and never reassessed is, over time, an incorrectly classified system. [The Thread](the-thread.md) makes the return loop explicit.

---

## Relationship to the Framework

This section references framework components throughout but does not repeat them. You'll need familiarity with:

- [Risk Tiers](../core/risk-tiers.md) — how the framework classifies risk
- [Controls](../core/controls.md) — what controls exist per tier
- [Fast Lane](../FAST-LANE.md) — the pre-approved low-risk path
- [PACE Resilience](../PACE-RESILIENCE.md) — the degradation model
- [Governance Operating Model](../extensions/regulatory/ai-governance-operating-model.md) — the organisational structure

---

*AI Runtime Behaviour Security, 2026 (Jonathan Gill).*
