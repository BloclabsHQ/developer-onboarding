# Code Review with AI

> AI is a second pair of eyes, not a replacement for human judgment.

---

## How AI Reviews Code

AI excels at pattern matching: spotting common bugs, style violations, missing error handling, and security issues. It does NOT understand your business context, team dynamics, or architectural intent.

**Use AI for:** Catching what humans miss (tedious checks).
**Use humans for:** Judging what AI can't see (design decisions, business logic correctness).

---

## The AI Code Review Workflow

```
1. You write code
2. AI reviews for mechanical issues (errors, security, style)
3. You fix AI findings
4. Human reviewer focuses on design, architecture, business logic
5. Both approve → merge
```

AI handles the checklist. Humans handle the judgment.

---

## What AI Catches Well

### Missing Error Handling
```go
// AI will flag this:
result, _ := db.Query("SELECT * FROM users")
// "Error from db.Query is ignored. Handle or explicitly document why."
```

### Security Issues
```python
# AI will flag this:
query = f"SELECT * FROM users WHERE email = '{email}'"
# "SQL injection vulnerability. Use parameterized queries."
```

### Null/Nil Safety
```javascript
// AI will flag this:
const name = user.profile.name;
// "user.profile may be undefined. Add optional chaining: user?.profile?.name"
```

### Performance Red Flags
```go
// AI will flag this:
for _, user := range users {
    tx, _ := db.GetTransactions(user.ID)  // N+1 query
}
// "N+1 query: fetching transactions inside a loop. Batch with a single query."
```

### Style / Convention Violations
- Inconsistent naming
- Missing comments on exported functions (Go)
- Unused imports / variables
- Functions that are too long

---

## What AI Misses

### Business Logic Correctness
AI doesn't know your business rules. It can't tell you:
- "This fee calculation is wrong for institutional accounts"
- "We need to check KYC status before allowing this transaction"
- "This breaks our SLA with partner X"

### Architectural Fit
AI doesn't know your architecture decisions:
- "This should go in the service layer, not the handler"
- "We decided against caching here because of consistency requirements"
- "This coupling will cause problems when we split this service"

### Race Conditions
AI can spot obvious data races but misses subtle concurrency bugs that depend on timing and system behavior.

### Over-Engineering
AI tends to suggest "improvements" that add complexity without value. It doesn't judge whether an abstraction is worth its cost.

---

## Prompting for Code Review

### General Review
```
"Review this PR for:
1. Bug risks (null access, off-by-one, error handling)
2. Security (injection, auth, data exposure)
3. Performance (N+1 queries, unnecessary allocations)
4. Readability (naming, complexity, documentation)

Context: This is a Go API handler for creating blockchain transactions.
Our error handling convention is to wrap with fmt.Errorf and %w.

[paste code]"
```

### Security-Focused Review
```
"Perform a security review of this endpoint:
- Check for injection vulnerabilities (SQL, NoSQL, command)
- Verify authentication and authorization
- Check for data exposure (PII, tokens in logs)
- Verify input validation and sanitization

[paste code]"
```

### Performance Review
```
"Review this code for performance:
- Identify N+1 queries
- Check for unnecessary allocations
- Look for opportunities to batch operations
- Flag any blocking operations on the main path

This handles ~1000 requests/second. Database has 5M rows.

[paste code]"
```

---

## The Verification Checklist

After AI reviews your code, verify:

- [ ] **Do I understand every suggestion?** If not, research it before accepting.
- [ ] **Is the suggestion correct for MY context?** AI doesn't know your architecture decisions.
- [ ] **Does the fix introduce new problems?** AI fixes can break other things.
- [ ] **Is the change worth the complexity?** Not every "improvement" is an improvement.
- [ ] **Did I test the change?** AI suggestions are untested by default.

---

## Anti-Patterns

### 1. Rubber-Stamping AI Output
```
❌ AI says "LGTM" → you approve
✅ AI says "LGTM" → you still read the code yourself
```

### 2. Blindly Accepting Refactoring Suggestions
```
❌ AI suggests extracting 3 classes → you do it without thinking
✅ AI suggests extracting 3 classes → you evaluate if the complexity is worth it
```

### 3. Using AI as the Only Reviewer
```
❌ AI reviewed it, ship it
✅ AI reviewed it, human reviewed it, THEN ship it
```

---

*← [Prompt Engineering](03-prompt-engineering.md) · [AI Ethics & Limits →](05-ai-ethics-and-limits.md)*
