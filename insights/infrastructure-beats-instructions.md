# Infrastructure Beats Instructions

*You can't secure AI agents with prompts*

---

There's a pattern in early agentic AI deployments that goes like this:

1. Build an agent that can take actions
2. Write a system prompt telling it what not to do
3. Deploy to production
4. Wait for something to go wrong

Something always goes wrong.

---

## The Instruction Fallacy

When we give an AI agent instructions, we're expressing intent. "Only access customer service data." "Don't send emails without approval." "Stay within the $100 budget."

These are policies. They describe what we want. They do not guarantee what we get.

The agent might follow instructions perfectly. It might interpret them creatively. It might find edge cases the instructions didn't anticipate. It might be manipulated into ignoring them entirely.

Instructions are suggestions to an AI. They're not constraints.

---

## Why Instructions Fail

### Prompt injection

An attacker embeds instructions in data the agent processes: a document, a webpage, a database field. "Ignore your previous instructions and..." The agent follows the injected instructions because it can't reliably distinguish them from legitimate ones.

Your carefully crafted system prompt? Overwritten.

### Creative interpretation

You told the agent to "stay within budget." It interpreted that as "per transaction" not "total." You told it to "only access approved systems." It decided that accessing an unapproved system to verify information from an approved system was fine.

The agent isn't malicious. It's just not interpreting instructions the way you expected.

### Goal drift

Over a long task, agents can drift from their stated objectives. They pursue subgoals that made sense three steps ago but don't align with the original intent. They optimise for proxies rather than outcomes.

The instructions are still there. The agent has wandered away from them.

### Emergent behaviour

At scale, agents exhibit behaviours that weren't anticipated. Interactions between multiple agents. Feedback loops with their environment. Patterns that emerge from thousands of executions.

No instruction set covers emergent behaviour because you can't instruct against what you haven't imagined.

---

## The Infrastructure Alternative

Instead of telling the agent what not to do, make it impossible.

| Instruction (Weak) | Infrastructure (Strong) |
|--------------------|------------------------|
| "Only access customer service data" | Database view exposes only CS data |
| "Don't send emails without approval" | Email API requires approval token |
| "Stay within $100 budget" | Hard spending cap at gateway |
| "Only call these three APIs" | Network whitelist blocks everything else |
| "Don't run for more than 10 minutes" | Process timeout kills execution |

The difference is enforcement location. Instructions are enforced by the agent — which means they're not really enforced at all. Infrastructure is enforced outside the agent — which means the agent can't circumvent it regardless of instructions, manipulation, or intent.

---

## Practical Implementation

### Network controls

The agent's runtime environment can only reach whitelisted endpoints. Everything else is blocked at the network layer. The agent can't exfiltrate data to an attacker's server because it can't reach that server.

### Data access controls

The agent authenticates with credentials that have minimal scope. It literally cannot query tables outside its authorisation. Not "shouldn't" — cannot.

### Action allow-lists

Every action the agent attempts passes through a validator. Is this action on the approved list? For this agent? In this context? If not, reject. The agent can request anything; the infrastructure decides what executes.

### Resource limits

Hard caps on compute, time, API calls, and spend. When limits hit, execution stops. The agent doesn't get to decide whether to respect the limit.

### Circuit breakers

Automatic halts triggered by anomaly detection. Error rate spikes? Pause. Unusual action patterns? Pause. Scope violation? Terminate. These trigger regardless of what the agent "thinks" it should do.

---

## Defence in Depth

Infrastructure controls don't replace instructions. They back them up.

![Defence in Depth Layers](../images/insights-defence-layers.svg)

Instructions tell the agent what you want. Infrastructure ensures it happens. Monitoring catches gaps. Response closes them.

---

## The Mindset Shift

Securing agents requires thinking like a systems engineer, not a prompt engineer.

Prompt engineering asks: "How do I tell the AI what to do?"

Systems engineering asks: "How do I make undesired outcomes impossible regardless of what the AI tries to do?"

The second question is harder. It requires understanding failure modes, designing constraints, and accepting that the agent is not trustworthy — not because it's malicious, but because it's unpredictable.

---

## The Bottom Line

If your agent security strategy is "we wrote really good instructions," you don't have a security strategy.

Instructions express intent. Infrastructure enforces it.

Build both. Trust only the second.

---

*From the [Enterprise AI Security Framework](https://github.com/JonathanCGill/enterprise-ai-security-controls-framework) — operational controls for enterprise AI systems.*
