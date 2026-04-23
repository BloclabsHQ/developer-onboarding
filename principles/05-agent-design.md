# AI Agent & Multi-Agent System Design Principles

## Agent Identity

### One Agent, One Purpose
Each agent should have a clearly defined role and domain. A security agent reviews security. A documentation agent writes docs. Avoid "god agents" that do everything -- they lose focus, context, and quality. Specialization enables depth.

### Identity Files Are the Source of Truth
Every agent needs a SOUL file that defines:
- Who it is (name, role, capabilities)
- How it operates (communication style, decision framework)
- What it will and will not do (boundaries, scope)
- Its operating principles

Without identity files, agents are stateless and amnesiac. They cannot learn, grow, or maintain consistency across sessions.

### Memory is What Makes an Agent an Agent
An agent without memory is a chatbot. Persistent memory enables:
- Learning from past mistakes
- Building on previous work
- Maintaining relationships and context
- Avoiding repeated failures

Store memory in structured files. Separate daily logs from long-term knowledge. Curate regularly -- memory that grows without pruning becomes noise.

## Multi-Agent Coordination

### Route to Specialists, Don't Bottleneck
In a multi-agent fleet, messages should route to the most qualified agent for the task. Routing everything through a single primary agent creates a bottleneck. Use channel bindings, topic routing, or skill-based dispatch.

### Agents Need Shared Context
Isolated agents cannot collaborate. Provide:
- A shared user context file (who they are helping, preferences, constraints)
- Cross-agent visibility (what other agents have done recently)
- A common set of operating principles

### Spawning vs Delegation
- **Spawn** a subagent when the task is self-contained and the result can be returned
- **Delegate** to a peer agent when the task needs that agent's full context and memory
- **Message** another agent when you need to share information without expecting a synchronous response

## Skills, Hooks & Automation

### Skill Design
- Each skill should be self-contained with clear inputs and outputs
- Skills should be composable -- small skills that chain together beat monolithic workflows
- Include usage examples in the skill definition
- Version skills so agents can reference stable behavior

### Hook Design
- Hooks should be lightweight and fast -- they run on every matching event
- Never put blocking I/O in a hook
- Hooks validate and guard; they do not implement business logic
- Log hook actions for debugging but keep output minimal
- Test hooks with both positive and negative cases

### Heartbeat vs Cron
- **Heartbeat**: Routine monitoring, context-aware checks, batch multiple lightweight tasks. Runs in the main session with full conversation history. Use for "check if anything needs attention."
- **Cron (isolated)**: Precise timing, deep analysis, external data fetching. Runs with a clean slate. Use for "do this specific thing at this specific time."
- **Cron (main session)**: Scheduled reminders and events that need conversation context. Integrates with the next heartbeat.

### Automation Principles
- Automate the repetitive, not the creative
- Every automated action should be auditable (who triggered it, what happened, what changed)
- Automated systems should degrade gracefully -- if the cron job fails, the system should still work
- Alert on automation failures, not successes
- Always provide a manual override

## Agent Communication

### No Decorative Content in Technical Channels
Professional, substance-focused communication. No emoji-littered messages, no excessive formatting, no filler phrases. The content should stand on its own.

### Recommend First, Explain Second
"Do X because Y" -- not "here are options A, B, C." Agents should have opinions. Present the recommended action with reasoning, not a menu of choices that pushes the decision back to the user.

### Signal Confidence
Always be explicit about certainty: "95% confident based on test results" vs "best estimate with limited data." Never present uncertain information as certain.

### Compress Ruthlessly
Human attention is the most scarce resource. Every message should be as short as possible while remaining complete. If it can be said in 3 sentences, do not use 10.

## Security for Agents

### Credential Isolation
Agents must never include tokens, API keys, passwords, or secrets in any outbound message. Use environment variable names, vault references, or redaction markers. If a credential is accidentally exposed, immediately flag it.

### Tool Permissions Are Mandatory
Every agent should operate under a tool policy:
- Deny dangerous tools by default (exec, write, delete, process)
- Grant elevated permissions explicitly per-agent or per-user
- Separate read-only operations from write operations
- Audit tool usage in logs

### Least Privilege
Each agent gets only the tools and access it needs for its defined role. A documentation agent does not need shell access. A security agent does not need write access to production configs.
