# Images

Architecture diagrams for the AI Security Reference Architecture.

## Available Diagrams

### Core Architecture
| Diagram | Description |
|---------|-------------|
| [architecture-overview.svg](architecture-overview.svg) | Main architecture: Judge, HITL, monitoring flow |
| [control-layers.svg](control-layers.svg) | Five-layer defense in depth model |
| [risk-tiers.svg](risk-tiers.svg) | Four risk classification tiers |
| [two-lanes-model.svg](two-lanes-model.svg) | Innovation vs Protection lanes |

### Operational
| Diagram | Description |
|---------|-------------|
| [hitl-workflow.svg](hitl-workflow.svg) | Human-in-the-loop decision flow |
| [judge-flow.svg](judge-flow.svg) | LLM Judge evaluation flow |
| [feedback-loops.svg](feedback-loops.svg) | Continuous improvement cycle |
| [incident-response.svg](incident-response.svg) | Incident response workflow |
| [agent-patterns.svg](agent-patterns.svg) | Agent authorization patterns |

### Governance & Compliance
| Diagram | Description |
|---------|-------------|
| [governance-structure.svg](governance-structure.svg) | Committee and working groups |
| [three-lines-model.svg](three-lines-model.svg) | Three lines of defense |
| [mapping-method.svg](mapping-method.svg) | Regulatory mapping approach |
| [evidence-collection.svg](evidence-collection.svg) | Audit evidence flow |

### Implementation
| Diagram | Description |
|---------|-------------|
| [maturity-model.svg](maturity-model.svg) | Five-level maturity progression |
| [pilot-phases.svg](pilot-phases.svg) | Pilot program phases |
| [roadmap-timeline.svg](roadmap-timeline.svg) | Implementation timeline |
| [deployment-decision.svg](deployment-decision.svg) | Deployment decision tree |

### Technical Reference
| Diagram | Description |
|---------|-------------|
| [telemetry-architecture.svg](telemetry-architecture.svg) | Logging and monitoring architecture |
| [zero-trust-ai.svg](zero-trust-ai.svg) | Zero trust principles for AI |
| [monitoring-dashboard.svg](monitoring-dashboard.svg) | Dashboard layout |
| [reporting-hierarchy.svg](reporting-hierarchy.svg) | Reporting structure |

### Threats
| Diagram | Description |
|---------|-------------|
| [threat-landscape.svg](threat-landscape.svg) | Adversaries, attacks, defenses |
| [trust-boundaries.svg](trust-boundaries.svg) | Security boundaries and control points |

### Supply Chain & Provenance
| Diagram | Description |
|---------|-------------|
| [supply-chain-controls.svg](supply-chain-controls.svg) | Model, prompt, tool, RAG control points |
| [rag-ingestion-pipeline.svg](rag-ingestion-pipeline.svg) | Document ingestion with policy checks |

### Judge Architecture
| Diagram | Description |
|---------|-------------|
| [judge-pdp-architecture.svg](judge-pdp-architecture.svg) | Judge as Policy Decision Point |
| [judge-shadow-mode.svg](judge-shadow-mode.svg) | Shadow mode evaluation flow |

### Repository
| Diagram | Description |
|---------|-------------|
| [repo-structure.svg](repo-structure.svg) | Repository folder structure |

### Worked Examples
| Diagram | Description |
|---------|-------------|
| [example-aria-architecture.svg](example-aria-architecture.svg) | Customer service AI control layers |
| [example-escalation-path.svg](example-escalation-path.svg) | HITL escalation tiers |
| [example-docbot-escalation.svg](example-docbot-escalation.svg) | Simple internal tool escalation |
| [example-underwriter-workflow.svg](example-underwriter-workflow.svg) | Credit decision 100% HITL workflow |

## Usage

All diagrams are SVG format for:
- Crisp rendering at any size
- Easy embedding in markdown
- Editable in vector tools

### Embedding in Markdown

```markdown
![Architecture Overview](images/architecture-overview.svg)
```

### Converting to PNG

For platforms that don't support SVG:

```bash
# Using Inkscape
inkscape -w 1200 architecture-overview.svg -o architecture-overview.png

# Using ImageMagick
convert -density 150 architecture-overview.svg architecture-overview.png
```

## Style Guide

Diagrams use a consistent color palette:

| Color | Hex | Usage |
|-------|-----|-------|
| Dark Blue | #1e3a5f | Primary headers, Protection Lane |
| Bright Blue | #3b82f6 | Judge components, Medium tier |
| Green | #10b981 | HITL, approval, Low tier |
| Amber | #f59e0b | Warning, monitoring, High tier |
| Red | #ef4444 | Block, Critical tier |
| Purple | #8b5cf6 | Innovation Lane, Level 5 |

## Contributing

To contribute diagrams:
1. Use SVG format
2. Follow the color palette
3. Include appropriate labels
4. Test rendering on GitHub

---

*This document is part of the AI Security Reference Architecture. Version 3.0 | January 2026*
