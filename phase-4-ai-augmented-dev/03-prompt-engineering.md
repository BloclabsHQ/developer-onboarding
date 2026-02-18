# Prompt Engineering for Code

> The quality of AI output is directly proportional to the quality of your prompt.

---

## The Framework: Context → Task → Constraints → Format

Every good prompt has four parts:

```
CONTEXT:     What's the situation? What does the AI need to know?
TASK:        What exactly do you want it to do?
CONSTRAINTS: What rules must it follow?
FORMAT:      How should the output look?
```

### Example

```
❌ "Write a function to process transactions"

✅ "I'm building a Go API service for a blockchain indexer.

    Write a function `ProcessTransaction` that:
    - Takes a raw transaction byte slice and chain identifier
    - Validates the transaction format based on the chain (EVM, Solana)
    - Returns a structured Transaction model or a typed error
    
    Constraints:
    - Follow Go error handling conventions (return error, don't panic)
    - Use the existing Transaction model in our codebase (attached)
    - Handle EVM and Solana chains, return UnsupportedChainError for others
    
    Include unit tests with table-driven test pattern."
```

---

## Key Techniques

### 1. Show, Don't Tell
Instead of describing the pattern, show an example:

```
"Follow this pattern for the new endpoint:

```go
func (h *Handler) GetUser(w http.ResponseWriter, r *http.Request) {
    id := chi.URLParam(r, "id")
    user, err := h.service.GetByID(r.Context(), id)
    if err != nil {
        h.respondError(w, err)
        return
    }
    h.respond(w, http.StatusOK, user)
}
```

Now create a similar handler for GetTransaction."
```

### 2. Specify the Language and Stack
```
✅ "In Go, using the chi router and sqlx for database access..."
✅ "In TypeScript with React 18, using TanStack Query for data fetching..."
❌ "Write a web endpoint..." (which language? which framework?)
```

### 3. Define Error Handling Expectations
```
"Handle these error cases:
- User not found → return 404 with NotFoundError
- Invalid input → return 400 with validation details
- Database error → log the error, return 500 with generic message"
```

### 4. Ask for Trade-offs
```
✅ "What are the pros and cons of using Redis vs in-memory caching here?"
✅ "Show me two approaches and explain when each is better."
❌ "What's the best way to cache this?" (no context for "best")
```

### 5. Iterate, Don't Start Over
```
"Good, but modify it to:
- Use a context timeout of 5 seconds
- Return a custom error type instead of a string error
- Add a retry with exponential backoff for transient failures"
```

Build on previous output. Don't re-explain everything.

---

## Common Mistakes

### 1. Too Vague
```
❌ "Make this better"
✅ "Refactor this to reduce nesting by using guard clauses, and extract the validation logic into a separate function"
```

### 2. No Context
```
❌ "Why is this slow?"
✅ "This PostgreSQL query takes 3 seconds on a table with 5M rows. Here's the query and the EXPLAIN ANALYZE output: [paste]. Why is it slow and how do I fix it?"
```

### 3. Asking for Everything at Once
```
❌ "Build me a complete user authentication system with JWT, refresh tokens, rate limiting, password reset, email verification, and OAuth"

✅ "Let's build user authentication step by step.
    Step 1: JWT token generation and validation.
    Here's our existing User model: [paste]
    We're using Go with chi router."
```

Break complex tasks into steps. Get each step right before moving on.

### 4. Not Reviewing Output
The biggest mistake: accepting AI output without understanding it. Every line of generated code should be reviewed as if a junior developer wrote it.

---

## Prompt Templates

### Code Review
```
"Review this Go function for:
1. Correctness (does it handle all cases?)
2. Error handling (are errors properly wrapped and returned?)
3. Performance (any obvious inefficiencies?)
4. Security (any injection or validation issues?)

[paste code]"
```

### Implementation
```
"Implement [feature] in [language] following these patterns:
- [reference to existing code pattern]
- [specific constraints]
- Include tests using [test framework]
- Handle errors following our convention: [describe]"
```

### Debugging
```
"I'm seeing [symptom] when [action].
Expected: [what should happen]
Actual: [what's happening]

Environment: [Go 1.22, PostgreSQL 16, macOS]
Relevant code: [paste]
Error message: [paste]
What I've tried: [list]"
```

### Architecture
```
"I need to design [system/feature].

Context:
- Current stack: [describe]
- Scale: [users, requests/sec, data volume]
- Team: [size, experience]
- Constraints: [budget, timeline, existing systems]

What architecture would you recommend and why?
Consider: scalability, maintainability, team capability."
```

---

## Exercises

1. Take a vague prompt ("make this code better") and rewrite it using the Context → Task → Constraints → Format framework.

2. Give AI a function with a known bug. See if your prompt helps it find the bug. Refine your prompt until it does.

3. Ask AI to implement something you already built. Compare its approach with yours. What's different? What's better?

4. Practice the "show, don't tell" technique: give AI one example, ask it to generate 5 more following the same pattern.

---

*← [Models & When](02-models-and-when.md) · [Code Review with AI →](04-code-review-with-ai.md)*
