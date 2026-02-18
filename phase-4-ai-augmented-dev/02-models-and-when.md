# Models & When to Use Them

> Not all AI models are equal. Use the right model for the right task.

---

## The Model Landscape

| Model | Strengths | Cost | Best For |
|-------|-----------|------|----------|
| **Claude Opus** | Deep reasoning, complex architecture, nuanced code review | $$$ | Architecture decisions, security review, complex debugging |
| **Claude Sonnet** | Fast, capable, good balance | $$ | Daily coding, PR reviews, implementation |
| **Claude Haiku** | Very fast, cheap | $ | Simple tasks, formatting, boilerplate |
| **GPT-4o** | Strong reasoning, multimodal | $$$ | Complex analysis, image understanding |
| **GPT-4o Mini** | Fast, cheap, capable | $ | Quick tasks, simple generation |
| **Gemini Pro** | Large context window, Google integration | $$ | Long document analysis, research |

---

## Decision Framework

```
Is this a critical architecture/security decision?
    → Use Opus or GPT-4o (best reasoning)

Is this daily coding work (implementation, tests, refactoring)?
    → Use Sonnet or GPT-4o Mini (fast + capable)

Is this simple boilerplate, formatting, or translation?
    → Use Haiku or GPT-4o Mini (fast + cheap)

Do I need to analyze a very long document or codebase?
    → Use Gemini Pro or Claude (large context)

Am I just asking a quick question?
    → Whatever's fastest (Haiku, GPT-4o Mini)
```

---

## Context Windows

The context window is how much text the model can "see" at once.

| Model | Context Window |
|-------|---------------|
| Claude Opus/Sonnet | 200K tokens (~150K words) |
| GPT-4o | 128K tokens (~96K words) |
| Gemini Pro | 1M+ tokens (~750K words) |

**1 token ≈ 4 characters ≈ 0.75 words**

### Implications
- Small file edits → any model works
- Analyzing an entire codebase → need large context (Claude, Gemini)
- If you exceed the context window, the model "forgets" earlier content

---

## Cost Awareness

AI costs money. Be smart about it.

| Task | Model | Why |
|------|-------|-----|
| Tab completion (100x/day) | Haiku / small model | Cheap, fast, called constantly |
| Chat in editor (10x/day) | Sonnet | Balance of quality and cost |
| Architecture review (1x/week) | Opus | Worth the cost for critical decisions |
| Generating test boilerplate | Haiku | Simple pattern, no deep reasoning needed |
| Security audit | Opus | Can't afford to miss vulnerabilities |

**Rule:** Don't use Opus for tasks Haiku can handle. Don't use Haiku for tasks that need Opus.

---

## Quality vs Speed Trade-off

```
Quality
  ▲
  │  ● Opus
  │
  │       ● Sonnet / GPT-4o
  │
  │            ● GPT-4o Mini
  │
  │                 ● Haiku
  │
  └────────────────────────▶ Speed / Cost
```

For most daily work, Sonnet-tier is the sweet spot. You only go higher for decisions that have lasting impact.

---

## Tips for Each Model

### Opus / GPT-4o (Heavy thinking)
- Give it the full context — architecture docs, constraints, requirements
- Ask it to think step by step
- Use for: "Design the data model for X considering Y constraints"
- Use for: "Review this code for security vulnerabilities and explain each finding"

### Sonnet / GPT-4o Mini (Daily driver)
- Be specific about what you want
- Provide examples of the pattern you want followed
- Use for: "Implement this function following the same pattern as [example]"
- Use for: "Write unit tests for this service covering edge cases"

### Haiku (Quick tasks)
- Simple, direct prompts
- Don't ask for complex reasoning
- Use for: "Convert this JSON to a Go struct"
- Use for: "Add error handling to each of these functions"

---

## Checklist

- [ ] Understand the model tiers and their strengths
- [ ] Can choose the right model for a given task
- [ ] Aware of context window limits
- [ ] Cost-conscious — don't use expensive models for simple tasks
- [ ] Know when to escalate to a more powerful model

---

*← [AI Tools Overview](01-ai-tools-overview.md) · [Prompt Engineering →](03-prompt-engineering.md)*
