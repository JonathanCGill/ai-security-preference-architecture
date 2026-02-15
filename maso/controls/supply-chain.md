# MASO Control Domain: Supply Chain

> Part of the [MASO Framework](../README.md) · Control Specifications
> Covers: LLM03 (Supply Chain Vulnerabilities) · ASI04 (Agentic Supply Chain)
> Also covers: HF-02 (Accountability — AIBOM component)

---

## Principle

Every component in the agent system — models, tools, MCP servers, RAG sources, plugins, and protocol endpoints — is a supply chain dependency. Each dependency is inventoried, versioned, integrity-verified, and auditable. No component is loaded at runtime without prior vetting and signing. The supply chain attack surface scales linearly with the number of agents and exponentially with the number of dynamic integrations.

---

## Why This Matters in Multi-Agent Systems

**The supply chain multiplies with every agent.** A single-model system has one model provider, one set of tools, one RAG configuration. A three-agent system may have three different model providers, nine tool integrations, three RAG sources, and two MCP server connections. Each additional dependency is an additional attack surface.

**Dynamic composition at runtime.** Modern agent frameworks support dynamic tool discovery and composition — an agent can discover and use an MCP server it has never interacted with before. This is powerful for flexibility and catastrophic for security. A poisoned MCP tool descriptor can trick an agent into passing credentials as tool parameters, executing unintended operations, or loading malicious code.

**Model provider changes propagate silently.** When a model provider updates their model (version change, fine-tuning update, safety filter modification), every agent using that model is affected simultaneously. If the update introduces a regression — reduced safety filtering, changed behaviour on edge cases, or degraded quality — the agents inherit the regression with no notice.

**Credential harvesting through tool manifests.** A poisoned tool manifest can describe a tool that requires "authentication tokens" as parameters. The agent, following its instructions to use the tool, passes credentials to the attacker's endpoint. This is a supply chain attack that doesn't require compromising the agent's code — only its tool configuration.

---

## Controls by Tier

### Tier 1 — Supervised

| Control | Requirement | Implementation Notes |
|---------|-------------|---------------------|
| **SC-1.1** Model inventory | Every model used by every agent is documented: provider, version, API endpoint, date of last known update | Reviewed monthly. |
| **SC-1.2** Tool inventory | Every tool available to every agent is documented: name, source, version, permission scope | No undocumented tool integrations. |
| **SC-1.3** Fixed toolsets | Agents use a predefined, static set of tools. No runtime discovery or dynamic composition | New tools require a change request and security review. |
| **SC-1.4** RAG source inventory | Every RAG/vector database is documented per agent: source, update frequency, access controls | Enables traceability when poisoning is suspected. |

### Tier 2 — Managed

All Tier 1 controls remain active, plus:

| Control | Requirement | Implementation Notes |
|---------|-------------|---------------------|
| **SC-2.1** AIBOM per agent | AI Bill of Materials generated for each agent: models, tools, RAG sources, MCP servers, dependencies, and their versions | Updated on every deployment. Stored alongside the agent's configuration. |
| **SC-2.2** Signed tool manifests | Tool manifests cryptographically signed; agents reject unsigned or tampered manifests | Signing key managed by the platform team, not the tool provider. |
| **SC-2.3** MCP server allow-listing | Agents can only connect to pre-approved MCP servers | Allow-list maintained by the AI security team. New MCP servers require vetting before addition. |
| **SC-2.4** Runtime integrity checks | Tool and MCP server integrity verified at load time against signed manifests | Integrity failure blocks loading and triggers an alert. |

### Tier 3 — Autonomous

All Tier 2 controls remain active, plus:

| Control | Requirement | Implementation Notes |
|---------|-------------|---------------------|
| **SC-3.1** Model version pinning | Each agent pinned to a specific model version; automatic rollback if provider changes the model without notice | Requires model version detection and comparison with expected version. |
| **SC-3.2** Automated rollback | If a model provider change causes quality degradation (detected by LLM-as-Judge baseline comparison), the system rolls back to the previous known-good version | Rollback is automated within the PACE Alternate phase. |
| **SC-3.3** Continuous dependency scanning | Automated scanning of all agent dependencies for known vulnerabilities and indicators of tampering | Minimum: daily scan. Results feed into the observability layer and trigger alerts on findings. |
| **SC-3.4** A2A trust chain validation | Agent-to-agent protocol endpoints (A2A, MCP, custom) validated against a trust chain before interaction | Prevents a compromised external service from injecting itself into the agent orchestration. |

---

## AIBOM Specification (Tier 2+)

The AI Bill of Materials is the supply chain equivalent of an SBOM (Software Bill of Materials). It provides a complete, versioned inventory of every component that contributes to an agent's behaviour.

**Required fields per agent:**

| Field | Description | Example |
|-------|-------------|---------|
| Agent ID | Unique identifier | `agent-analyst-01` |
| NHI Reference | Link to agent's NHI record | `NHI-A-2026-0142` |
| Primary Model | Provider, model name, version, API endpoint | `Anthropic / claude-sonnet-4-5-20250929 / messages/v1` |
| Fallback Model | Backup model for PACE Alternate phase | `Google / gemini-2.0-flash / v1` |
| Judge Model | LLM-as-Judge model (must differ from task model) | `OpenAI / gpt-4o / chat/completions` |
| Tools | List of tools with versions and manifest hashes | `[{name: "document-reader", version: "2.1.3", hash: "sha256:abc..."}]` |
| MCP Servers | Connected MCP servers with endpoint and manifest hash | `[{name: "internal-search", endpoint: "https://...", hash: "sha256:def..."}]` |
| RAG Sources | Knowledge bases with integrity checksums | `[{name: "policy-docs", checksum: "sha256:ghi...", last_verified: "2026-02-14"}]` |
| Dependencies | Runtime libraries and framework versions | `[{name: "langchain", version: "0.3.12"}]` |
| Last Updated | Timestamp of AIBOM generation | `2026-02-15T08:00:00Z` |
| Accountable Human | Named human owner responsible for this agent's design, data sources, and approval | `jonathan.gill@example.com` (Amendment: HF-02) |
| Deployment Hash | Hash of the complete agent deployment configuration | `sha256:jkl...` |

The AIBOM should be generated automatically as part of the deployment pipeline and stored alongside the agent's configuration in version control. Any discrepancy between the deployed agent and its AIBOM indicates either a deployment error or tampering.

---

## MCP Server Vetting Process (Tier 2+)

MCP (Model Context Protocol) servers extend agent capabilities by providing tools and data. They are the most dynamic component of the supply chain and require structured vetting before agents can connect.

**Vetting checklist:**

| Check | Description |
|-------|-------------|
| Source verification | Who built it? Is the source code available for review? |
| Manifest inspection | Do the tool descriptions match the actual tool behaviour? Are parameter schemas well-defined? |
| Credential handling | Does the tool request credentials as parameters? (Red flag — credentials should be injected by the platform, not passed by the agent.) |
| Network behaviour | What external endpoints does the MCP server contact? Are they documented and expected? |
| Data handling | What data does the MCP server access, store, or transmit? Does it comply with the agent's data classification? |
| Update mechanism | How is the MCP server updated? Can updates change tool behaviour without notice? |
| Signing | Can the manifest be cryptographically signed? Is the signing key managed appropriately? |

**Approval authority:** AI security team for Tier 2. AI Security Architect for Tier 3 (higher bar due to autonomous usage).

**Re-vetting triggers:** Any update to the MCP server, change in maintainer, security advisory affecting the server's dependencies, or a 6-month periodic review — whichever comes first.

---

## Testing Criteria

### Tier 1 Tests

| Test ID | Test | Pass Criteria |
|---------|------|---------------|
| SC-T1.1 | Inventory accuracy | Compare documented model, tool, and RAG inventories against actual agent configurations. No undocumented components. |
| SC-T1.2 | Fixed toolset enforcement | Attempt to add a tool to an agent's configuration without going through the change process. Attempt is blocked or detected. |
| SC-T1.3 | Inventory freshness | Verify inventories have been reviewed within the last 30 days. |

### Tier 2 Tests

| Test ID | Test | Pass Criteria |
|---------|------|---------------|
| SC-T2.1 | AIBOM completeness | Generate AIBOM for each agent. Every field is populated. AIBOM matches deployed configuration. |
| SC-T2.2 | Manifest tampering | Modify a tool manifest after signing. Agent rejects the tampered manifest at load time. |
| SC-T2.3 | Unsigned tool | Attempt to load a tool without a signed manifest. Agent blocks the tool. |
| SC-T2.4 | Unapproved MCP server | Configure an agent to connect to an MCP server not on the allow-list. Connection is blocked. |
| SC-T2.5 | Runtime integrity | Modify a tool binary after deployment. Runtime integrity check detects the modification and blocks execution. |

### Tier 3 Tests

| Test ID | Test | Pass Criteria |
|---------|------|---------------|
| SC-T3.1 | Model version change detection | Simulate a model provider updating their model (change the version identifier). System detects the change and pins to the previous version. |
| SC-T3.2 | Automated rollback | Deploy a model version that produces lower LLM-as-Judge scores than baseline. System triggers rollback within the PACE Alternate phase. |
| SC-T3.3 | Dependency vulnerability detection | Introduce a dependency with a known CVE into the agent's environment. Continuous scanning detects it within 24 hours. |
| SC-T3.4 | A2A trust chain | Introduce a new A2A endpoint not in the trust chain. Agent rejects the connection. |

---

## Maturity Indicators

| Level | Indicator |
|-------|-----------|
| **Initial** | No inventory of models, tools, or RAG sources. Agents use whatever tools are available. MCP servers added without review. |
| **Managed** | Model, tool, and RAG inventories maintained. Fixed toolsets enforced. Changes go through a documented process. |
| **Defined** | AIBOM per agent. Signed tool manifests. MCP server allow-listing with vetting process. Runtime integrity checks. |
| **Quantitatively Managed** | AIBOM accuracy measured (deployed vs. documented). Manifest verification failure rate tracked. MCP vetting completion time measured. |
| **Optimising** | Model version pinning with automated rollback. Continuous dependency scanning. A2A trust chain validation. AIBOM generation fully automated in CI/CD pipeline. |

---

## Common Pitfalls

**Treating model provider updates as safe by default.** Model providers update their models regularly — sometimes with notice, sometimes without. Each update is a supply chain change that can affect every agent in the system. Model version pinning (Tier 3) is the defensive measure, but even at Tier 2, organisations should monitor provider change logs and test updates before promoting them to production.

**Vetting MCP servers once and never again.** An MCP server that was safe at vetting time can become unsafe through updates, dependency changes, or maintainer compromise. Re-vetting must be periodic and triggered by any change. The 6-month periodic review is a backstop, not a target.

**AIBOM as a compliance document rather than an operational tool.** The AIBOM is useful only if it is compared against the deployed configuration automatically. A beautifully formatted AIBOM that sits in a wiki and diverges from reality is worse than no AIBOM — it provides false assurance.

**Allowing agents to discover tools at runtime.** Dynamic tool discovery is the defining feature of MCP-based agent frameworks and the defining vulnerability of their supply chain. At Tier 1 and Tier 2, tool discovery must be disabled in favour of static allow-lists. Even at Tier 3, discovery should be limited to pre-approved registries with signed manifests.

---

*Previous: [Observability](observability.md) · Back to: [MASO Framework](../README.md)*
