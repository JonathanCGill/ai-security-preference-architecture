# Contributing to Enterprise AI Security Framework

Thank you for your interest in contributing to this project. This document provides guidelines for contributions.

---

## How to Contribute

### Reporting Issues

If you find an error, omission, or have a suggestion:

1. **Check existing issues** — Someone may have already reported it
2. **Open a new issue** — Use the appropriate template
3. **Be specific** — Include the document, section, and what you think should change
4. **Provide context** — Why does this matter? What's the impact?

### Submitting Changes

1. **Fork the repository**
2. **Create a branch** — `feature/your-feature-name` or `fix/issue-number`
3. **Make your changes** — Follow the style guide below
4. **Test your changes** — Ensure links work, markdown renders correctly
5. **Submit a pull request** — Reference any related issues

### What We're Looking For

**Highly valued contributions:**

| Type | Examples |
|------|----------|
| **Production experience** | Real-world implementation lessons, cost data, failure modes |
| **Regulatory insight** | Regulatory feedback, compliance interpretations, examination findings |
| **Platform expertise** | Implementation guides for specific platforms |
| **Academic research** | Empirical validation, formal analysis |
| **Corrections** | Factual errors, outdated information, broken links |

**Also welcome:**

- Clarifications and improved explanations
- Additional examples and use cases
- Translations (discuss first)
- Tooling and automation

---

## Style Guide

### Writing Style

- **Clear and direct** — Avoid jargon where possible
- **Practical** — Focus on what practitioners need
- **Honest** — Distinguish between proven and theoretical
- **Consistent** — Match the tone of existing content

### Markdown Conventions

```markdown
# Document Title (H1 - one per document)

## Major Section (H2)

### Subsection (H3)

**Bold** for emphasis on key terms
*Italic* for introducing new concepts
`Code` for technical terms, control IDs, file names

| Column 1 | Column 2 |
|----------|----------|
| Data | Data |

- Bullet lists for unordered items
1. Numbered lists for sequences

> Blockquotes for important callouts
```

### Control Naming

- General AI controls: `AI.x.x` (e.g., AI.7.1 Input Guardrails)
- Agentic controls: `AG.x.x` (e.g., AG.2.2 Circuit Breakers)
- Always include the control name after the ID

### File Naming

- Lowercase with hyphens: `my-document-name.md`
- Numbered prefixes for ordered content: `01-introduction.md`
- SVG for diagrams: `diagram-name.svg`

---

## Review Process

1. **Automated checks** — Links, markdown formatting
2. **Maintainer review** — Content accuracy, style consistency
3. **Feedback** — We may request changes or clarification
4. **Merge** — Once approved, your contribution will be merged

### Review Criteria

| Criterion | What We Check |
|-----------|---------------|
| **Accuracy** | Is the content factually correct? |
| **Relevance** | Does it belong in this framework? |
| **Clarity** | Is it easy to understand? |
| **Consistency** | Does it match existing style and structure? |
| **Completeness** | Is it thorough enough to be useful? |

---

## Types of Contributions

### Documentation Improvements

- Fix typos, grammar, unclear explanations
- Add examples or clarifications
- Update outdated information
- Improve cross-references

### New Content

Before starting significant new content:

1. **Open an issue** to discuss the idea
2. **Get feedback** on scope and approach
3. **Coordinate** to avoid duplicate effort

Areas where new content is welcome:

- Platform-specific implementation guides
- Industry-specific guidance (beyond banking)
- Regional regulatory mappings
- Worked examples and case studies

### Diagrams and Visualisations

- SVG format preferred
- Consistent colour palette (see existing diagrams)
- Clear, readable at reasonable zoom
- Include alt text descriptions

---

## Code of Conduct

This project follows a standard code of conduct. In summary:

- **Be respectful** — Treat others as you want to be treated
- **Be constructive** — Focus on improving the content
- **Be patient** — Maintainers are volunteers
- **Be inclusive** — Welcome diverse perspectives

See [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) for details.

---

## Recognition

Contributors will be recognised in:

- Git commit history
- Contributors list (for significant contributions)
- Release notes (for major contributions)

---

## Questions?

If you're unsure about anything:

1. Check the existing documentation
2. Open an issue with your question
3. Tag it with `question`

We're happy to help guide your contribution.

---

*Thank you for helping improve AI security governance.*
