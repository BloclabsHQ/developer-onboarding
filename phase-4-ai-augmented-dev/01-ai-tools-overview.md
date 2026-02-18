# AI Tools Overview

> AI doesn't replace engineers. It amplifies them. The engineer who uses AI well ships 10x faster.

---

## The AI Development Stack

```
┌─────────────────────────────────────────────────────┐
│                   YOUR BRAIN                         │
│          (understanding, decisions, judgment)         │
├─────────────────────────────────────────────────────┤
│  Cursor / VS Code      │  Claude Code / Codex       │
│  (inline editing,      │  (CLI agents,              │
│   tab completion,      │   multi-file changes,      │
│   chat in editor)      │   autonomous tasks)        │
├─────────────────────────────────────────────────────┤
│  ChatGPT / Claude Web  │  OpenClaw                  │
│  (research, planning,  │  (agent orchestration,     │
│   architecture Q&A)    │   automation, integrations)│
└─────────────────────────────────────────────────────┘
```

---

## Cursor (Primary Editor)

**What:** VS Code fork with built-in AI. Your daily driver.

### Key Features
- **Tab Completion** — AI predicts your next line/block. Press Tab to accept.
- **⌘K (Inline Edit)** — Select code, describe what you want changed. AI rewrites it.
- **⌘L (Chat)** — Ask questions about your codebase. AI sees your open files.
- **⌘I (Composer)** — Multi-file AI editing. Describe a feature, AI implements across files.
- **@ References** — `@file`, `@codebase`, `@docs` to give AI more context.

### Workflow
```
1. Write a function signature and docstring
2. Let Tab completion fill the implementation
3. Review, adjust with ⌘K if needed
4. ⌘L to ask "does this handle edge cases?"
5. Write tests (AI helps with test generation)
```

### Setup
```bash
brew install --cask cursor
```

---

## Claude Code (CLI Agent)

**What:** Command-line AI agent that can read, write, and execute code.

### When to Use
- Multi-file refactoring
- "Implement this feature across the codebase"
- Code review from terminal
- Complex debugging sessions

### Workflow
```bash
claude "Add input validation to all API handlers in internal/user/"
claude "Write tests for the transaction service"
claude "Find and fix all SQL injection vulnerabilities"
```

---

## GitHub Copilot

**What:** AI pair programmer in VS Code/Cursor.

### When to Use
- Inline code completion
- Writing boilerplate (tests, CRUD endpoints, data transformations)
- Translating between languages

### Tips
- Write a good function name and docstring → Copilot writes the implementation
- Write one test → Copilot generates similar tests for other cases
- Comment what you want → Copilot translates to code

---

## ChatGPT / Claude Web

**What:** Conversational AI for research, planning, and architecture.

### When to Use
- Architecture design ("How should I structure a multi-chain indexer?")
- Technology evaluation ("Pros and cons of Redis vs Memcached for our use case")
- Explaining concepts ("Explain the actor model for concurrency")
- Code review ("Review this function for security issues: [paste]")
- Writing documentation

### Tips
- Provide full context (language, framework, constraints)
- Ask for trade-offs, not just solutions
- Verify claims — AI can be confidently wrong

---

## OpenClaw

**What:** Agent orchestration platform. Runs AI agents that can execute tasks autonomously.

### When to Use
- Automated code review on PRs
- Background tasks (monitoring, alerting)
- Multi-agent workflows (research → implement → test)
- Integration with Linear, GitHub, Slack

---

## Project Management: Linear

**What:** Issue tracking and project management.

### Integration with Code
- Create tickets in Linear
- Branch names include ticket ID: `feat/FAB-123-add-indexer`
- PRs auto-link to tickets
- Merging can auto-close tickets

### Workflow
```
Linear ticket → Create branch → Write code → PR → Review → Merge → Ticket closed
```

---

## When to Use What

| Task | Tool |
|------|------|
| Writing code in editor | Cursor (Tab + ⌘K) |
| Multi-file implementation | Cursor Composer (⌘I) or Claude Code |
| Architecture decisions | Claude/ChatGPT web |
| Code review assist | Cursor Chat or Claude Code |
| Research / learning | Claude/ChatGPT web |
| Boilerplate / repetitive code | Copilot Tab completion |
| Autonomous background tasks | OpenClaw |
| Bug investigation | Cursor Chat (@ references to codebase) |

---

## The Critical Rule

**AI is a power tool, not a replacement for understanding.**

You must understand what the AI generates. If you can't explain why the code works, you don't ship it. AI amplifies your skill — if your skill is zero, the amplification is zero.

```
Junior + AI = faster junior (still needs review)
Senior + AI = 10x senior (leverage)
No understanding + AI = dangerous (ships bugs confidently)
```

---

## Checklist

- [ ] Cursor installed with AI features enabled
- [ ] Know ⌘K, ⌘L, ⌘I shortcuts
- [ ] GitHub account connected (for Copilot if available)
- [ ] Linear account set up, understand the workflow
- [ ] Can use CLI tools (Claude Code) for multi-file tasks
- [ ] Understand when to use each tool

---

*← [Phase 3: Developer Communication](../phase-3-engineering/08-developer-communication.md) · [Models & When →](02-models-and-when.md)*
