# Technical Controls for AI Security

Network, infrastructure, and platform controls that enforce AI security at the technical layer.

> **See also:** [Bypass Prevention](bypass-prevention.md) for the full taxonomy of bypass techniques and defence strategies.

---

## Why Technical Controls Matter

Policy and process controls fail when:
- Users don't follow them
- Attackers ignore them
- Insiders circumvent them

Technical controls **enforce** security regardless of user behaviour. If the network blocks access to unauthorised AI, users can't use it — no policy required.

| Control Type | Relies On | Failure Mode |
|--------------|-----------|--------------|
| Policy | User compliance | Users ignore or forget |
| Process | Consistent execution | Process bypassed under pressure |
| Technical | Infrastructure enforcement | Misconfiguration, but otherwise reliable |

**Principle:** Wherever possible, enforce security technically rather than relying on user behaviour.

---

## Control Architecture Overview

![Technical Controls Architecture](../../images/technical-controls-architecture.svg)

---

## 1. Network-Level Controls

### 1.1 Firewall Rules

Block access to unauthorised AI services at the network layer.

**Outbound rules (egress):**

| Rule | Action | Purpose |
|------|--------|---------|
| Block `api.openai.com` | DENY | Prevent direct OpenAI access (route through gateway) |
| Block `api.anthropic.com` | DENY | Prevent direct Anthropic access |
| Block `*.openai.azure.com` | DENY | Prevent direct Azure OpenAI access |
| Block known AI service IPs | DENY | Catch IP-based access attempts |
| Allow approved AI gateway | ALLOW | Force all AI traffic through controlled path |

**Implementation considerations:**

| Challenge | Solution |
|-----------|----------|
| AI providers use CDN/dynamic IPs | Use domain-based filtering (FQDN), not just IP |
| HTTPS inspection required | TLS interception with appropriate certificates |
| Cloud workloads | Cloud firewall rules (security groups, NSGs) |
| Remote workers | Enforce via VPN/SASE, or endpoint controls |

**Example: AWS Security Group**

```
# Deny direct access to AI providers from application subnet
# Force traffic through AI Gateway

Outbound rules:
- Allow: AI Gateway security group, port 443
- Deny: 0.0.0.0/0, port 443 (default deny outbound HTTPS)
- Allow: Internal services as needed
```

### 1.2 DNS Controls

Block resolution of unauthorised AI domains.

**DNS sinkhole / RPZ (Response Policy Zone):**

```
# Block resolution of unauthorised AI services
*.openai.com        CNAME   blocked.internal
*.anthropic.com     CNAME   blocked.internal  
*.ai.google.dev     CNAME   blocked.internal
*.cohere.ai         CNAME   blocked.internal
chat.openai.com     CNAME   blocked.internal
claude.ai           CNAME   blocked.internal
gemini.google.com   CNAME   blocked.internal
```

**Redirect to internal alternatives:**

```
# Redirect to approved internal gateway
api.openai.com      CNAME   ai-gateway.internal.company.com
```

**Considerations:**

| Challenge | Solution |
|-----------|----------|
| DNS-over-HTTPS (DoH) bypasses | Block DoH providers, enforce internal DNS |
| Users change DNS settings | Endpoint policy enforcement, block external DNS |
| Cloud workloads | Use cloud DNS services with policy |

### 1.3 Network Segmentation

Isolate AI systems from sensitive data unless explicitly connected.

![Network Zones](../../images/network-zones.svg)

**Zero Trust principles for AI:**

| Principle | AI Application |
|-----------|----------------|
| Never trust, always verify | Every AI request authenticated and authorised |
| Least privilege | AI systems access only data they need |
| Assume breach | AI zone isolated so compromise doesn't spread |
| Verify explicitly | Every action validated, not just session start |

---

## 2. Proxy and Gateway Controls

### 2.1 Forward Proxy

Intercept and control all outbound traffic, including to AI services.

**Proxy capabilities for AI:**

| Capability | Purpose |
|------------|---------|
| **URL filtering** | Block unauthorised AI endpoints |
| **TLS inspection** | Examine HTTPS content for policy violations |
| **Content inspection** | Detect sensitive data in AI requests |
| **User attribution** | Log who is making AI requests |
| **Bandwidth control** | Limit AI traffic volume |

**Proxy rules example:**

```
# Allow approved AI via gateway
ALLOW  ai-gateway.internal.company.com  *

# Block direct access to AI providers
BLOCK  *.openai.com          "Use approved AI gateway"
BLOCK  *.anthropic.com       "Use approved AI gateway"
BLOCK  *.ai.google.dev       "Use approved AI gateway"
BLOCK  chat.openai.com       "Consumer AI not permitted"
BLOCK  claude.ai             "Consumer AI not permitted"

# Block AI browser extensions
BLOCK  *  User-Agent:*ChatGPT*
BLOCK  *  User-Agent:*Claude*
```

### 2.2 AI Gateway

A dedicated gateway for all AI traffic — the core enforcement point.

![AI Gateway Architecture](../../images/ai-gateway-architecture.svg)

**Gateway capabilities:**

| Capability | Implementation |
|------------|----------------|
| **Authentication** | SSO integration, API key management, mTLS |
| **Authorisation** | Role-based model access, use-case restrictions |
| **Rate limiting** | Per-user, per-application, per-model limits |
| **Input guardrails** | Block injection, enforce content policy |
| **Output guardrails** | Filter PII, block harmful content |
| **DLP integration** | Scan for sensitive data before sending to model |
| **Routing** | Direct requests to appropriate model/provider |
| **Logging** | Full interaction capture for audit and Judge |
| **Cost tracking** | Attribute costs to teams/projects |

**Commercial AI gateway options:**

| Product | Strengths |
|---------|-----------|
| **AWS Bedrock Guardrails** | Native AWS integration, managed service |
| **Azure AI Content Safety** | Native Azure integration |
| **Databricks AI Gateway** | MLflow integration, unified AI management |
| **Kong AI Gateway** | Open source base, extensible |
| **Portkey** | Multi-provider, observability focus |
| **LiteLLM Proxy** | Open source, provider abstraction |
| **Cloudflare AI Gateway** | Edge deployment, caching |

### 2.3 API Gateway Integration

If you have an existing API gateway (Kong, Apigee, AWS API Gateway, etc.), extend it for AI by adding these plugins/policies to `/v1/ai/*` routes:

| Plugin/Policy | Purpose |
|---------------|---------|
| AI authentication policy | Validate AI-specific credentials |
| AI rate limiting (token-aware) | Limit by tokens, not just requests |
| AI request transformation | Inject system prompts, sanitise inputs |
| AI guardrail plugin | Input validation, injection detection |
| AI logging plugin | Full content capture for audit |
| AI response transformation | Output filtering, PII redaction |
| AI cost tracking plugin | Attribute costs to users/projects |

---

## 3. Web Application Firewall (WAF)

### 3.1 WAF for AI Applications

Protect AI-powered web applications from attack.

**AI-specific WAF rules:**

| Rule Category | Purpose | Example |
|---------------|---------|---------|
| **Prompt injection signatures** | Block known injection patterns | `IGNORE PREVIOUS`, `<system>`, `[INST]` |
| **Encoding detection** | Detect obfuscated payloads | Base64 in unexpected fields |
| **Payload size limits** | Prevent context stuffing | Max prompt length |
| **Rate limiting** | Prevent abuse | Requests per minute per user |
| **Bot detection** | Block automated attacks | CAPTCHA, fingerprinting |

**Example: AWS WAF rules for AI**

```json
{
  "Name": "AIPromptInjectionRule",
  "Priority": 1,
  "Statement": {
    "OrStatement": {
      "Statements": [
        {
          "ByteMatchStatement": {
            "SearchString": "ignore previous instructions",
            "FieldToMatch": { "Body": {} },
            "TextTransformations": [{ "Priority": 0, "Type": "LOWERCASE" }],
            "PositionalConstraint": "CONTAINS"
          }
        },
        {
          "ByteMatchStatement": {
            "SearchString": "disregard above",
            "FieldToMatch": { "Body": {} },
            "TextTransformations": [{ "Priority": 0, "Type": "LOWERCASE" }],
            "PositionalConstraint": "CONTAINS"
          }
        },
        {
          "RegexMatchStatement": {
            "RegexString": "\\[\\s*(INST|SYS|SYSTEM)\\s*\\]",
            "FieldToMatch": { "Body": {} },
            "TextTransformations": [{ "Priority": 0, "Type": "NONE" }]
          }
        }
      ]
    }
  },
  "Action": { "Block": {} }
}
```

### 3.2 WAF Limitations for AI

| Limitation | Why | Mitigation |
|------------|-----|------------|
| Can't understand semantic meaning | WAF sees syntax, not intent | Layer with ML-based guardrails |
| Doesn't see decrypted content by default | TLS termination needed | Terminate TLS at WAF or use inline inspection |
| Static rules miss novel attacks | Known patterns only | Regular rule updates, anomaly detection |
| High false positive risk | AI prompts look "weird" to traditional WAF | Tune rules, use AI-aware rule sets |

**Recommendation:** WAF is a useful layer but not sufficient alone. Use WAF for:
- Known attack signatures
- Rate limiting
- Bot protection
- Basic input validation

Use AI-specific guardrails for:
- Semantic analysis
- Context-aware filtering
- ML-based detection

---

## 4. Data Loss Prevention (DLP)

### 4.1 DLP for AI Traffic

Prevent sensitive data from leaving via AI channels.

**DLP inspection points:**

![DLP Inspection Points](../../images/dlp-inspection-points.svg)

**DLP rules for AI:**

| Data Type | Detection Method | Action |
|-----------|-----------------|--------|
| Credit card numbers | Regex + Luhn check | Block, alert |
| SSN / National ID | Regex + format validation | Block, alert |
| API keys / secrets | Entropy analysis, known patterns | Block, alert |
| Source code | File type, keywords, patterns | Warn, log |
| Customer PII | NER, pattern matching | Warn, redact, or block based on policy |
| Internal documents | Classification labels, metadata | Block, alert |
| Health information | HIPAA patterns, medical terms | Block, alert |

**Example: Microsoft Purview DLP for AI**

```
Policy: Block sensitive data to AI services

Conditions:
- Content contains: Credit Card Number (high confidence)
- Content contains: SSN (high confidence)  
- Content contains: Document marked "Confidential"

Locations:
- Devices (browser to AI sites)
- Exchange (email containing AI prompts)
- Cloud apps (AI SaaS)

Actions:
- Block with override (user can justify and proceed)
- Notify user
- Alert security team
- Log for audit
```

### 4.2 AI-Specific DLP Challenges

| Challenge | Solution |
|-----------|----------|
| **Context in prompts** | Scan prompt content, not just attachments |
| **Data in system prompts** | Inspect system prompt injection |
| **RAG context** | Scan retrieved chunks before model |
| **Data returned by tools** | Inspect tool outputs in agent workflows |
| **Sensitive output** | Scan model responses, not just inputs |
| **Encoded data** | Decode before scanning |

---

## 5. Endpoint Controls

### 5.1 Endpoint Detection and Response (EDR)

Monitor endpoints for AI-related risks.

**EDR rules for AI:**

| Detection | Indicator |
|-----------|-----------|
| **Unauthorised AI applications** | Process names, network connections |
| **Browser AI extensions** | Extension IDs, web requests |
| **Data staging for AI** | Large text file creation, clipboard activity |
| **API key exposure** | Key patterns in files, memory |
| **Shadow AI usage** | Connections to known AI endpoints |

### 5.2 Browser Controls

Control AI access from browsers.

| Control | Implementation |
|---------|----------------|
| **Block AI websites** | Proxy/DNS block consumer AI sites |
| **Block AI extensions** | Browser extension whitelist/blacklist |
| **Isolate AI browsing** | Separate browser profile for approved AI |
| **Clipboard monitoring** | DLP on copy/paste to AI |

**Example: Chrome Enterprise policy**

```json
{
  "ExtensionInstallBlocklist": ["*"],
  "ExtensionInstallAllowlist": [
    "approved-ai-extension-id"
  ],
  "URLBlocklist": [
    "chat.openai.com",
    "claude.ai",
    "gemini.google.com",
    "poe.com",
    "character.ai"
  ],
  "URLAllowlist": [
    "ai-portal.company.com"
  ]
}
```

### 5.3 Mobile Device Management (MDM)

Control AI on mobile devices.

| Control | Purpose |
|---------|---------|
| **App whitelist** | Only approved AI apps installable |
| **Network restrictions** | VPN required for AI access |
| **Copy/paste restrictions** | Limit data movement to AI apps |
| **Managed browser** | Control web-based AI access |

---

## 6. Cloud Security Controls

### 6.1 Cloud AI Service Controls

Control access to cloud AI services.

**AWS:**

| Control | Implementation |
|---------|----------------|
| **IAM policies** | Restrict Bedrock access to approved roles |
| **SCPs** | Organisation-wide Bedrock restrictions |
| **VPC endpoints** | Private access to Bedrock, no internet |
| **CloudTrail** | Log all Bedrock API calls |
| **Guardrails** | Bedrock native content filtering |

**Example: AWS SCP for Bedrock**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyUnapprovedModels",
      "Effect": "Deny",
      "Action": "bedrock:InvokeModel",
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "bedrock:ModelId": [
            "anthropic.claude-3-sonnet-*",
            "amazon.titan-text-*"
          ]
        }
      }
    },
    {
      "Sid": "RequireGuardrails",
      "Effect": "Deny",
      "Action": "bedrock:InvokeModel",
      "Resource": "*",
      "Condition": {
        "Null": {
          "bedrock:GuardrailId": "true"
        }
      }
    }
  ]
}
```

**Azure:**

| Control | Implementation |
|---------|----------------|
| **Azure RBAC** | Restrict OpenAI service access |
| **Azure Policy** | Enforce configuration standards |
| **Private endpoints** | No public internet access |
| **Diagnostic settings** | Log all API calls |
| **Content filtering** | Azure AI Content Safety |

**GCP:**

| Control | Implementation |
|---------|----------------|
| **IAM** | Restrict Vertex AI access |
| **Org policies** | Enforce project-level restrictions |
| **VPC Service Controls** | Perimeter around AI services |
| **Audit logs** | Log all AI API calls |

### 6.2 Cloud Access Security Broker (CASB)

Control SaaS AI usage.

**CASB capabilities for AI:**

| Capability | AI Application |
|------------|----------------|
| **Discovery** | Find shadow AI SaaS usage |
| **Access control** | Block unsanctioned AI apps |
| **DLP** | Inspect data going to AI SaaS |
| **Threat protection** | Detect anomalous AI usage |
| **Compliance** | Report on AI data handling |

**Sanctioned vs. unsanctioned AI apps:**

![CASB AI App Classification](../../images/casb-ai-classification.svg)

---

## 7. Identity and Access Management

### 7.1 Authentication for AI

| Control | Implementation |
|---------|----------------|
| **SSO integration** | AI gateway integrates with corporate IdP |
| **MFA** | Required for AI access, especially sensitive use cases |
| **Service accounts** | Dedicated accounts for AI applications, not shared |
| **API key management** | Centralised, rotated, scoped, audited |
| **mTLS** | Certificate-based auth for service-to-AI communication |

### 7.2 Authorisation for AI

| Level | What It Controls |
|-------|-----------------|
| **Platform access** | Who can use AI services at all |
| **Model access** | Which models a user/app can invoke |
| **Capability access** | What features (chat, embeddings, fine-tuning) |
| **Data access** | What data AI can access on behalf of user |
| **Action access** | What actions AI agents can take |

**Example: RBAC for AI**

```yaml
roles:
  ai-user-basic:
    permissions:
      - model:invoke:tier-1  # Basic models only
      - data:read:public     # Public data only
    limits:
      - requests_per_day: 100
      
  ai-user-advanced:
    permissions:
      - model:invoke:tier-1
      - model:invoke:tier-2  # Advanced models
      - data:read:internal   # Internal data
    limits:
      - requests_per_day: 1000
      
  ai-developer:
    permissions:
      - model:invoke:*       # All models
      - data:read:*          # All data (in dev)
      - guardrail:configure  # Can configure guardrails
    limits:
      - requests_per_day: 10000
      
  ai-admin:
    permissions:
      - model:*              # Full model control
      - guardrail:*          # Full guardrail control
      - audit:read           # Access audit logs
```

### 7.3 Context-Aware Access

AI access decisions based on context, not just identity.

| Context Factor | Example Policy |
|----------------|----------------|
| **Location** | Block AI access from high-risk countries |
| **Device** | Only managed devices can access sensitive AI |
| **Time** | Restrict AI access outside business hours |
| **Network** | Different access from corp network vs. VPN vs. public |
| **Risk score** | Reduce AI access for users with elevated risk |
| **Data sensitivity** | Higher auth requirements for sensitive data + AI |

---

## 8. Logging and Monitoring Infrastructure

### 8.1 AI-Specific Logging

| Log Source | What to Capture |
|------------|-----------------|
| **AI Gateway** | Full request/response, user, model, timing, cost |
| **Firewall** | Blocked AI access attempts |
| **Proxy** | AI endpoint access patterns |
| **DLP** | Sensitive data in AI traffic |
| **CASB** | SaaS AI usage |
| **Endpoint** | Local AI app usage |

### 8.2 SIEM Integration

Feed AI logs to SIEM for correlation and alerting.

**Example: Splunk queries for AI security**

```spl
# Shadow AI detection
index=proxy dest_category="ai-services" NOT dest="ai-gateway.company.com"
| stats count by src_user, dest
| where count > 10

# Unusual AI usage volume
index=ai_gateway 
| timechart span=1h count by user
| anomalydetection count
| where isAnomaly=1

# Sensitive data in AI requests
index=dlp action="ai-block" 
| stats count by user, data_type
| sort -count

# Failed AI access attempts
index=ai_gateway status=403
| stats count by user, reason
| where count > 5
```

### 8.3 Alerting

| Alert | Condition | Response |
|-------|-----------|----------|
| **Shadow AI detected** | Traffic to blocked AI endpoints | Investigate, educate user |
| **DLP trigger** | Sensitive data to AI | Block, investigate |
| **Unusual volume** | >3x normal AI usage | Investigate for abuse |
| **Auth failures** | Multiple failed AI auth | Potential attack |
| **Guardrail bypass attempt** | Known injection pattern | Block, investigate |

---

## 9. Implementation Priorities

### Phase 1: Foundation (Weeks 1-4)

| Control | Purpose | Effort |
|---------|---------|--------|
| DNS blocking of consumer AI | Stop obvious shadow AI | Low |
| AI Gateway deployment | Central control point | Medium |
| Basic logging | Visibility | Low |
| API key centralisation | Credential control | Medium |

### Phase 2: Enforcement (Weeks 5-8)

| Control | Purpose | Effort |
|---------|---------|--------|
| Proxy rules for AI | Force traffic through gateway | Medium |
| DLP for AI traffic | Protect sensitive data | Medium |
| WAF rules for AI apps | Protect AI-powered apps | Medium |
| RBAC for AI | Control who can do what | Medium |

### Phase 3: Advanced (Weeks 9-12)

| Control | Purpose | Effort |
|---------|---------|--------|
| Network segmentation | Isolate AI systems | High |
| CASB integration | Control SaaS AI | Medium |
| SIEM correlation | Detect complex attacks | Medium |
| Context-aware access | Adaptive security | High |

### Phase 4: Optimisation (Ongoing)

| Control | Purpose | Effort |
|---------|---------|--------|
| Rule tuning | Reduce false positives | Ongoing |
| Coverage expansion | Catch gaps | Ongoing |
| Automation | Reduce manual effort | Ongoing |
| Metrics and reporting | Demonstrate value | Ongoing |

---

## 10. Control Mapping to Bypass Categories

| Bypass Category | Technical Controls |
|-----------------|-------------------|
| **Guardrail bypass** | WAF, AI Gateway guardrails, DLP |
| **Direct API access** | Network block, proxy enforcement, no user API keys |
| **Shadow AI** | DNS blocking, proxy blocking, CASB, endpoint monitoring |
| **Data exfiltration** | DLP, network monitoring, egress filtering |
| **Insider config change** | IAM, privileged access management, change logging |
| **Credential theft** | API key vault, rotation, mTLS, no embedded keys |

---

## Summary

1. **Block at the network** — Deny by default, allow approved AI only
2. **Force through gateway** — Single control point for all AI traffic
3. **Inspect everything** — TLS inspection, DLP, content scanning
4. **Log everything** — Full visibility for detection and forensics
5. **Layer controls** — Network + gateway + WAF + DLP + endpoint
6. **Automate enforcement** — Don't rely on user compliance

---

*AI Security Blueprint — Technical Controls*
