# AI Ethics & Limits

> AI is the most powerful tool you'll use. With power comes responsibility.

---

## What AI Gets Wrong

### Hallucinations
AI can generate code that looks correct, compiles, and is completely wrong. It invents API methods that don't exist, references packages that were never published, and cites documentation that was never written.

**Rule:** If AI references a library, function, or API you don't recognize — verify it exists before using it.

### Confident Incorrectness
AI never says "I don't know." It generates an answer every time, even when it shouldn't. The more confident it sounds, the more dangerous it is when wrong.

**Rule:** Confidence of the AI ≠ correctness of the answer.

### Outdated Knowledge
Models are trained on data up to a cutoff date. They may suggest deprecated APIs, old patterns, or libraries with known vulnerabilities.

**Rule:** Check the current docs. `npm audit`, `go vet`, and `cargo audit` catch known issues AI might introduce.

### Subtle Bugs
AI-generated code often works for the happy path but fails on edge cases. Off-by-one errors, race conditions, incorrect error handling — the bugs AI introduces are the sneaky kind.

**Rule:** Test AI-generated code as rigorously as human-written code. More rigorously, because you didn't write it and may not fully understand it.

---

## Intellectual Property

### Don't Paste Proprietary Code into Public AI
```
❌ Pasting internal API code into ChatGPT free tier
❌ Sharing production database schemas with public AI tools
❌ Uploading client data to AI for analysis

✅ Using AI tools with enterprise agreements (data not used for training)
✅ Asking about general patterns without proprietary details
✅ Using AI with your own locally-hosted models
```

### Open Source Licensing
AI may generate code that's similar to open-source projects. This could create licensing issues.

**Rule:** If AI generates a large block of code that looks copied, check if it matches an existing open-source project. Respect the license.

### Attribution
When AI helps significantly with a solution, it's good practice to note it:
```
// Generated with AI assistance, reviewed and tested.
```

---

## Security Concerns

### Never Trust AI with Secrets
```
❌ "Here's my API key, can you help me configure..."
❌ "The database password is X, how do I..."

✅ "I have an API key stored in an environment variable called API_KEY..."
✅ "Using a database connection configured via DATABASE_URL..."
```

### AI-Generated Security Code
Be extra cautious with AI-generated:
- Authentication / authorization logic
- Cryptographic operations
- Input sanitization
- SQL queries (injection risk)

These areas require expert review, not just AI review.

---

## The Skill Amplifier Model

```
Your skill level    × AI amplification    = Output quality

Expert (10)         × AI (5x)             = 50 (excellent)
Intermediate (5)    × AI (5x)             = 25 (good)
Beginner (1)        × AI (5x)             = 5  (mediocre)
No understanding(0) × AI (5x)             = 0  (dangerous)
```

AI amplifies what you already know. It cannot replace understanding. A developer who doesn't understand concurrency will ship race conditions faster with AI, not fewer.

**The path:**
1. Learn the fundamentals (this program)
2. Build understanding through practice
3. THEN use AI to amplify your capabilities
4. Continue learning — AI doesn't replace growth

---

## Responsible AI Use at BlocLabs

### DO
- Use AI to learn faster (explain concepts, generate examples)
- Use AI to write boilerplate and tests
- Use AI for code review (as a supplement, not replacement)
- Use AI to explore design alternatives
- Verify and test all AI output

### DON'T
- Ship code you don't understand
- Paste proprietary code into public AI tools
- Use AI to bypass code review ("AI wrote it, it must be fine")
- Stop learning because "AI can do it"
- Share credentials, keys, or PII with AI tools

---

## The Future

AI capabilities are growing rapidly. Models will get better, faster, and cheaper. But the fundamentals don't change:

- Understanding the problem > generating the solution
- Correct > fast
- Tested > untested
- Understood > generated

The developers who thrive will be those who use AI as a power tool while maintaining deep understanding of their craft.

---

## Checklist

- [ ] Understand AI limitations (hallucinations, outdated knowledge, subtle bugs)
- [ ] Never paste proprietary code into public AI tools
- [ ] Never share credentials with AI
- [ ] Test AI-generated code rigorously
- [ ] Can explain every line of AI-generated code before shipping
- [ ] Use AI to amplify skills, not replace understanding

---

*← [Code Review with AI](04-code-review-with-ai.md) · [Phase 5: Go at BlocLabs →](../phase-5-bloclabs-stack/01-go-at-bloclabs.md)*
