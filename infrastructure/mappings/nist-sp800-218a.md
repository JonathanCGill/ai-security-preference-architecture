# NIST SP 800-218A Mapping

**How this framework relates to NIST SP 800-218A: Secure Software Development Practices for Generative AI and Dual-Use Foundation Models.**

---

## Relationship

NIST SP 800-218A and this framework address different lifecycle phases of AI systems. SP 800-218A covers secure development — from data sourcing through model release. This framework covers secure deployment — from production through incident response.

SP 800-218A explicitly states that deployment and operation are out of its scope. This framework explicitly states that model training and pre-deployment testing are out of its scope. Together, they provide lifecycle coverage.

| Dimension | SP 800-218A | This Framework |
| --- | --- | --- |
| **Lifecycle phase** | Development through release | Deployment through incident response |
| **Primary audience** | Model producers, system producers, acquirers | Deployers, security teams, governance |
| **Control style** | SDLC practices and tasks | Runtime infrastructure controls |
| **Risk approach** | Priority levels (High/Medium/Low) | Risk tiers with proportionate controls |

Organisations that fine-tune models operate in both phases and should apply both frameworks.

---

## Mapping: SP 800-218A Practices to Framework Controls

This mapping shows where SP 800-218A practices connect to controls in this framework. Many SP 800-218A practices have no direct mapping because they address development-time concerns that are out of scope here. The mappings below identify areas of overlap, handoff, or complementary coverage.

### Prepare the Organization (PO)

| SP 800-218A Task | Priority | Framework Relevance | Framework Controls |
| --- | --- | --- | --- |
| **PO.1.1** Define security requirements for AI development infrastructure | High | Requirements defined here should flow into deployment controls. Development security requirements that don't carry through to production create gaps. | Risk Tiers → control selection |
| **PO.1.2** Define security requirements for AI software | High | Deployment-time security requirements (guardrails, monitoring, human oversight) should be specified during development. | [Controls](../../core/controls.md) — all three layers |
| **PO.1.3** Communicate requirements to third parties | Medium | Acquirers should require evidence that providers follow SP 800-218A practices. This framework's Supply Chain controls address the deployer side. | SC-01 through SC-08 (Supply Chain) |
| **PO.2.1** Define roles and responsibilities | High | Roles defined during development must extend to deployment operations — who monitors, who escalates, who decides. | [Controls](../../core/controls.md) — Human Oversight layer |
| **PO.2.2** Role-based training | High | Training should cover runtime threats (prompt injection, adversarial inputs) not just development-time vulnerabilities. | No direct mapping — organisational practice |
| **PO.3.1–3.3** Implement supporting toolchains | High | Development toolchain security feeds into deployment toolchain security. CI/CD pipeline integrity protects both phases. | SC-03 (Dependency scanning), SC-04 (Build pipeline integrity) |
| **PO.4.1** Define criteria for security checks | Medium | SP 800-218A recommends guardrails throughout the development lifecycle. This framework enforces guardrails at runtime. Same concept, different enforcement point. | GR-01 through GR-08 (Guardrails layer), LJ-01 through LJ-08 (Judge layer) |
| **PO.5.1** Separate and protect development environments | High | Development environment security is out of scope for this framework. Deployment environment security is addressed by Network & Segmentation and Sandbox controls. | NS-01 through NS-08 (Network & Segmentation), SB-01 through SB-06 (Sandbox Patterns) |
| **PO.5.3** Continuously monitor development environments | High | Development monitoring is SP 800-218A's concern. Production monitoring is this framework's concern. Same principle, different lifecycle phase. | LO-01 through LO-10 (Logging & Observability) |

### Protect Software (PS)

| SP 800-218A Task | Priority | Framework Relevance | Framework Controls |
| --- | --- | --- | --- |
| **PS.1.1** Protect code, models, and weights from unauthorised access | High | Model weight protection during development. At deployment, this maps to access controls on model endpoints and infrastructure. | IA-01 through IA-08 (Identity & Access Management), SK-01 through SK-08 (Secrets & Credentials) |
| **PS.1.2** Protect training data *(new task)* | High | Out of scope for this framework. Relevant for organisations fine-tuning models — see scope note in README. | No direct mapping — development concern |
| **PS.1.3** Protect model weights and configuration *(new task)* | High | Directly relevant for self-hosted open-weight deployments. Deployers running their own models must implement weight protection controls. | DP-01 through DP-08 (Data Protection), see also [Open-Weight Models Shift the Burden](../../insights/open-weight-models-shift-the-burden.md) |
| **PS.2.1** Provide integrity verification for releases | Medium | Deployers should verify model integrity at acquisition and before deployment. | SC-01 (Provenance verification), SC-02 (Integrity checking) |
| **PS.3.1** Archive releases with supporting data | Low | Model versioning and lineage tracking. At deployment, this maps to knowing exactly what you're running. | SC-05 (Asset inventory), LO-03 (Audit logging) |
| **PS.3.2** Collect and share provenance data (SBOM) | Medium | AI model provenance should be tracked as part of supply chain controls. This includes model cards, training data documentation, and component lineage. | SC-06 (SBOM/dependency tracking) |

### Produce Well-Secured Software (PW)

| SP 800-218A Task | Priority | Framework Relevance | Framework Controls |
| --- | --- | --- | --- |
| **PW.1.1** AI-specific threat modelling | High | Development-time threat models should inform deployment-time controls. Threats identified here (prompt injection, data poisoning, supply chain attacks) map directly to this framework's control domains. | [Risk Tiers](../../core/risk-tiers.md), threat model templates in [Templates](../../extensions/templates/) |
| **PW.3.1** Analyse training data integrity *(new practice)* | High | Out of scope for this framework except for organisations fine-tuning. | No direct mapping — development concern |
| **PW.3.3** Include adversarial samples in training | Medium | Development-time robustness testing. At deployment, adversarial resilience is enforced through guardrails and the Judge layer. | GR controls (input validation), LJ controls (adversarial detection) |
| **PW.4.4** Verify acquired AI components | High | Directly relevant. Deployers acquiring models or components should verify integrity, provenance, and security before deployment. | SC-01 through SC-04 (Supply Chain) |
| **PW.5.1** Secure coding for inputs and outputs | High | **Key overlap.** SP 800-218A wants input/output handling built into code. This framework enforces it at the infrastructure layer. Both are needed — defence-in-depth means code-level and infrastructure-level controls should coexist. | GR-01 (Input filtering), GR-02 (Output filtering), NS-03 (API gateway controls), see also [Infrastructure Beats Instructions](../../insights/infrastructure-beats-instructions.md) |
| **PW.7.2** Scan models for malware and vulnerabilities | High | Pre-deployment scanning is SP 800-218A's concern. Runtime monitoring for anomalous behaviour is this framework's concern. | LO-01 through LO-10 (Logging & Observability), see also [Behavioral Anomaly Detection](../../insights/behavioral-anomaly-detection.md) |
| **PW.8.1–8.2** Test AI models for vulnerabilities | High | Pre-deployment testing. This framework addresses post-deployment testing through red-teaming guidance and continuous evaluation. | Testing guidance in [Templates](../../extensions/templates/), LJ controls (ongoing evaluation) |

### Respond to Vulnerabilities (RV)

| SP 800-218A Task | Priority | Framework Relevance | Framework Controls |
| --- | --- | --- | --- |
| **RV.1.1** Gather vulnerability information | Medium | Development-time vulnerability tracking feeds into deployment-time incident response. Vulnerabilities discovered in production should be reported back to model producers. | IR-01 through IR-08 (Incident Response), LO-05 (Alert generation) |
| **RV.1.2** Assess and prioritise vulnerabilities | Medium | Same risk-based prioritisation applies at deployment. This framework's risk tiers inform severity assessment. | [Risk Tiers](../../core/risk-tiers.md), IR-02 (Severity classification) |
| **RV.2.2** Assess and update response options | Medium | Deployment-time response options include guardrail updates, Judge criteria changes, model rollback, and circuit breakers. | IR controls, TA-01 through TA-06 (Tool Access — for restricting capabilities), SS-01 through SS-05 (Session & Scope) |
| **RV.3.3** Review and update the vulnerability response process | Low | Post-incident review should improve both development and deployment controls. Feedback loop between the two frameworks. | IR-07 (Post-incident review), IR-08 (Lessons learned) |

---

## Key Handoff Points

These are the areas where SP 800-218A practices hand off to this framework's controls. Gaps at these handoff points create blind spots.

| Handoff | SP 800-218A Responsibility | This Framework's Responsibility |
| --- | --- | --- |
| **Model release** | Verify integrity, scan for vulnerabilities, provide provenance data | Verify received model, deploy with runtime controls, monitor in production |
| **Input/output handling** | Build secure handling into code | Enforce at infrastructure layer with guardrails |
| **Threat model** | Identify AI-specific threats during development | Implement controls proportionate to identified threats at deployment |
| **Vulnerability response** | Fix vulnerabilities in model code and training | Mitigate at runtime through guardrail updates, Judge criteria, circuit breakers |
| **Environment security** | Protect development environments | Protect deployment environments (network, sandbox, access controls) |

---

## Control ID Reference

The control IDs referenced above use the domain prefix scheme from this framework's [Infrastructure Controls](../):

| Prefix | Domain |
| --- | --- |
| IA | Identity & Access Management |
| LO | Logging & Observability |
| NS | Network & Segmentation |
| DP | Data Protection |
| SK | Secrets & Credentials |
| SC | Supply Chain |
| IR | Incident Response |
| TA | Tool Access |
| SS | Session & Scope |
| DC | Delegation Chains |
| SB | Sandbox Patterns |

---

*Enterprise AI Security Controls Framework, 2026 (Jonathan Gill).*
