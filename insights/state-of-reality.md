# State of Reality: What the Data Actually Shows

> *"The world cannot be understood without numbers. And it cannot be understood with numbers alone."*
> — Hans Rosling, Factfulness

This document grounds the Enterprise AI Security Framework in observed data rather than projected fear. The threat is real. It is also specific, measurable, and disproportionately concentrated in predictable failure modes.

---

## The Incident Landscape

AI-related security incidents are growing fast, but the data demands precision about *what kind* of incidents and *where* they occur.

### Scale

- **233 reported AI incidents in 2024**, a 56.4% increase year-on-year (Stanford AI Index 2025).
- **2025 is on track to surpass all prior years combined** in breach volume (Adversa AI, 2025 Edition).
- **13% of organizations reported breaches of AI models or applications** in the IBM 2025 Cost of a Data Breach study — the first year this was measured. Of those compromised, **97% lacked basic AI access controls**.
- **1 in 5 organizations suffered a shadow AI breach**, costing an average of **$670,000 more** than traditional incidents (IBM, 2025).

### Where the Failures Actually Are

The Adversa AI 2025 Incidents Report analyzed 17 real-world case studies. The findings are instructive for what they reveal about *failure concentration*:

| Finding | Implication |
| --- | --- |
| **35% of incidents caused by simple prompts** — no code, no exploits | Most attacks require no sophistication. Basic input validation is missing. |
| **Generative AI present in ~75% of incidents** | GenAI is the dominant attack surface, but not the only one. |
| **Agentic AI caused the most severe failures** — crypto theft, cross-tenant data leaks, unauthorized transactions | Severity scales with autonomy. The more an AI system can *do*, the worse the failure when it misbehaves. |
| **Technology sector accounts for 35.3%** of documented incidents, but finance, telecom, and retail are increasingly affected | No industry is immune; concentration is shifting. |

### The Mundane Majority

The AIAAIC Repository and AI Incident Database collectively track over 1,300 incidents. The dominant categories are not sophisticated adversarial attacks. They are:

- **Deepfakes and synthetic media** — used for fraud, disinformation, non-consensual imagery. These are *misuse of AI*, not *failure of AI systems*. They are out of scope for this framework.
- **Hallucination and misinformation** — AI systems presenting fabricated citations, non-existent case law, or incorrect medical/financial advice. This is the most common failure mode in enterprise custom AI.
- **Data leakage via prompts** — employees pasting confidential data into public AI tools (shadow AI). Samsung's developer incident remains the canonical example.
- **Prompt injection** — the #1 vulnerability in OWASP's LLM Top 10 for 2025, and the only attack vector that exploits the fundamental architecture of LLMs rather than an implementation flaw.

### What Is *Not* Happening at Scale (Yet)

Proportionality matters. Some threats are real but their observed frequency in production enterprise systems remains low:

- **Training data poisoning** — documented in research, rarely confirmed in enterprise production incidents.
- **Model extraction / model theft** — technically feasible, not yet a common enterprise attack vector.
- **Sophisticated multi-step adversarial attacks** — most real-world breaches use simple techniques against systems with no controls at all.

This does not mean these threats can be ignored. It means control investment should be proportionate to observed frequency and impact, not hypothetical severity alone.

---

## The Governance Gap

The gap between AI adoption and AI security is the single most important data point in this space:

- **63% of breached organizations** either don't have an AI governance policy or are still developing one (IBM, 2025).
- **Only 34%** of organizations with governance policies perform regular audits for unsanctioned AI (IBM, 2025).
- **Only 20% of firms** feel confident securing generative AI, despite 72% integrating AI into business functions.
- **Only 66%** of organizations regularly test their GenAI-powered products.
- **Over 50%** of enterprise AI app adoption is estimated to be shadow AI — tools employees adopted without security review.

The primary risk is not sophisticated attack. It is **the absence of basic controls on systems already in production**.

---

## What This Means for Control Selection

The data supports a clear prioritization:

**Immediate priority (addresses >80% of observed incidents):**
1. Know what AI systems you have (shadow AI discovery)
2. Implement input/output validation (addresses prompt injection and data leakage)
3. Enforce access controls on AI systems (97% of breached organizations lacked these)
4. Implement human review for high-stakes outputs (addresses hallucination in consequential decisions)

**Second priority (addresses emerging and escalating threats):**
5. Async behavioral monitoring / LLM-as-Judge (detects novel failures guardrails miss)
6. Agentic controls — scope limitation, tool-call validation, least-privilege (severity scales with autonomy)
7. Supply chain verification for AI components

**Third priority (addresses future and theoretical threats):**
8. Training data provenance and integrity
9. Model extraction countermeasures
10. Multi-agent coordination security

This prioritization is not permanent. As the threat landscape matures and agentic AI adoption accelerates, the ordering will shift. Review annually against current incident data.

---

## Sources

| Source | What It Provides | Link |
| --- | --- | --- |
| Stanford AI Index 2025 | Annual incident count, trend data, policy tracking | [hai.stanford.edu](https://aiindex.stanford.edu/report/) |
| IBM Cost of a Data Breach 2025 | Breach costs, shadow AI data, governance gaps | [newsroom.ibm.com](https://newsroom.ibm.com/) |
| Adversa AI 2025 Incidents Report | Real-world case studies, failure categorization | [adversa.ai](https://adversa.ai/top-ai-security-incidents-report-2025-edition/) |
| OWASP Top 10 for LLM Applications 2025 | Vulnerability taxonomy, ranked by expert assessment | [genai.owasp.org](https://genai.owasp.org/) |
| OWASP Top 10 for Agentic Applications | Agentic-specific risks | [genai.owasp.org](https://genai.owasp.org/) |
| AI Incident Database (AIID) | Crowdsourced incident tracking, 1,300+ incidents | [incidentdatabase.ai](https://incidentdatabase.ai/) |
| AIAAIC Repository | Independent incident and controversy tracking | [aiaaic.org](https://www.aiaaic.org/aiaaic-repository) |
| Reco AI & Cloud Security: 2025 Year in Review | Enterprise breach case studies, SaaS/AI intersection | [reco.ai](https://www.reco.ai/blog/ai-and-cloud-security-breaches-2025) |

---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
