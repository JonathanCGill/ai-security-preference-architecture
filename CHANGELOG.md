# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [Unreleased]

### Planned
- Cost model with production data
- Platform-specific implementation guides (detailed)
- Case studies from production deployments

---

## [0.6.0] - 2026-02-08

### Changed
- **Renamed: AI Security Blueprint → Enterprise AI Security Framework**
  - Better reflects the content scope (governance, compliance, org structure)
  - "Blueprint" implied buildable artifacts; "Framework" is accurate
  
### Added
- **IMPLEMENTATION_GUIDE.md** — New practical guide with working code
  - Input guardrails (regex + Bedrock + NeMo examples)
  - Output guardrails (PII, forbidden phrases, structured validation)
  - LLM-as-Judge (prompts, sampling strategies, async processing)
  - Human-in-the-loop queue (Redis implementation, FastAPI endpoints)
  - Telemetry and logging (structured logs, Prometheus metrics)
  - Complete request flow example
  - Test suite templates (unit tests, red team inputs)
  - ~1,500 lines of copy-paste-ready Python

### Rationale
Reality check revealed the framework was thought leadership, not a buildable blueprint. Now there are two clear paths:
- **Implementors**: Start with IMPLEMENTATION_GUIDE.md (code)
- **Architects/Governance**: Use the full Framework (strategy)

---

## [0.5.0] - 2026-02-07

### Changed
- **Major restructure: Core + Extensions model**
- New `/core/` folder with 5 essential documents:
  - README.md — Overview and quick start
  - risk-tiers.md — Classification and control selection
  - controls.md — Guardrails, Judge, HITL combined
  - agentic.md — Agent-specific controls
  - checklist.md — Implementation tracking
- New `/extensions/` folder for reference material:
  - regulatory/ — ISO 42001, EU AI Act, banking
  - technical/ — Bypass prevention, infrastructure, metrics
  - templates/ — Playbooks, assessments
  - examples/ — Worked examples
- Root README now serves as navigation hub
- Previous detailed documents preserved in extensions

### Rationale
Framework had grown to 48 files. Core + Extensions model provides clear "start here" path (5 docs) while preserving depth for those who need it.

---

## [0.4.1] - 2026-02-06

### Added
- Bypass Prevention document — comprehensive guide to preventing and detecting control circumvention across 5 bypass categories (guardrails, intent, agentic, architectural, process)
- Technical Controls document — network, firewall, WAF, AI gateway, DLP, proxy, endpoint, cloud, and IAM controls for infrastructure-level enforcement
- 14 new SVG diagrams:
  - bypass-taxonomy.svg — 5 bypass categories visual
  - defence-in-depth.svg — 8-layer control stack
  - technical-controls-architecture.svg — infrastructure overview
  - ai-gateway-architecture.svg — gateway internals
  - network-zones.svg — network segmentation
  - agent-sandbox.svg — infrastructure constraints
  - action-validator-flow.svg — action validation pipeline
  - tool-output-sanitiser.svg — tool output handling
  - canary-testing.svg — control verification programme
  - dlp-inspection-points.svg — 4 DLP layers
  - casb-ai-classification.svg — sanctioned/tolerated/blocked apps
  - bypass-learning-loop.svg — continuous improvement cycle
  - infra-vs-instruction.svg — enforcement comparison
  - multi-layer-input-validation.svg — input processing pipeline

### Changed
- Updated bypass-prevention.md and technical-controls.md to reference SVG diagrams instead of ASCII art
- Clarified lifecycle scope in README — framework is operationally focused (deployment → operation → incident response), not full AI/ML lifecycle

---

## [0.4.0] - 2026-02-05

### Added
- AI Incident Response Playbook — 10 playbooks for AI-specific incidents
- Vendor Assessment Questionnaire — comprehensive due diligence template
- Operational Metrics document — KPIs, dashboards, alerting thresholds
- Data Retention Guidance — requirements by tier and jurisdiction
- Templates README — index of all templates
- Standard repo files: CODE_OF_CONDUCT.md, GOVERNANCE.md, LICENSE (MIT), SECURITY.md

### Changed
- Updated README with Templates section and new document links
- Moved "Threats" section to "Threats and Risks" with expanded content

---

## [0.3.0] - 2026-02-05

### Added
- Novel AI Risks document — 12 risks unique to AI systems
- Support Systems Risk document — operational risks that matter most
- Banking Cyber Risks document — top 10 banking risks through AI lens
- Feeder systems analysis with diagram
- 10 new controls: AI.3.4, AI.5.4, AI.6.4, AI.6.5, AI.7.4, AI.8.5, AI.9.5, AI.10.6, AI.13.4
- Support systems risk heat map SVG
- Banking AI feeder systems diagram SVG
- Model card template
- Reference materials (glossary, bibliography)
- Future work roadmap

### Changed
- Strengthened AI.4.2 (Testing) with statistical testing for non-determinism
- Strengthened AI.6.2 (Model Validation) with bias testing and continuous validation
- Strengthened AI.6.3 (Model Monitoring) with degradation detection
- Strengthened AI.7.1 (Input Guardrails) with semantic analysis and RAG filtering
- Strengthened AI.7.2 (Output Guardrails) with grounding checks
- Strengthened AI.7.3 (Guardrail Maintenance) with semantic adversarial testing
- Strengthened AI.8.1 (Judge Evaluation) with hallucination and override detection
- Strengthened AI.8.2 (Sampling Strategy) with baseline integration
- Strengthened AI.9.1 (HITL) with automation bias mitigation
- Strengthened AI.11.1 (Logging) with full context capture
- Strengthened AI.13.1 (Vendor Assessment) with training data practices
- Strengthened AI.14.1 (Training) with cognitive bias training
- Strengthened AG.2.3 (Scope Enforcement) with outcome boundaries
- Updated README with new documentation links

### Fixed
- XML entity escaping in SVG files (ampersand encoding)

---

## [0.2.0] - 2026-01-15

### Added
- Agentic Controls (AG.1-AG.4) for autonomous AI systems
- AG.2.5 Tool Protocol Security for MCP, function calling
- ISO 42001 alignment document
- EU AI Act crosswalk
- Platform integration guide (Bedrock, Databricks, Foundry)
- Control selection guide
- Tube map visualisation
- Multiple architecture diagrams

### Changed
- Expanded risk tier definitions
- Enhanced HITL model documentation
- Improved Judge model selection guidance

---

## [0.1.0] - 2025-12-01

### Added
- Initial framework release
- Three-layer control model (Guardrails, Judge, HITL)
- AI control families AI.1-AI.16
- Risk tiering framework (CRITICAL/HIGH/MEDIUM/LOW)
- LLM-as-Judge pattern and operating model
- HITL operating model
- ISO 27001 alignment
- OWASP LLM Top 10 threat mapping
- Implementation guide
- Maturity model
- Example implementations (customer service, document assistant, credit decision)

---

## Categories

- `Added` for new features
- `Changed` for changes in existing functionality
- `Deprecated` for soon-to-be removed features
- `Removed` for now removed features
- `Fixed` for any bug fixes
- `Security` for vulnerability fixes
