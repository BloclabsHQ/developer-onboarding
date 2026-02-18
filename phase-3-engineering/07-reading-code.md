# Reading Code

> Senior engineers spend 80% of their time reading code. This is the skill that matters most.

---

## Why Reading > Writing

You'll inherit codebases. You'll review PRs. You'll debug code you didn't write. You'll integrate with libraries and APIs. In all these cases, you need to read and understand code quickly.

Writing code from scratch is the minority of your job. Reading existing code is the majority.

---

## How to Navigate a New Codebase

### 1. Start with the Entry Point
Every program starts somewhere:

```
Go:     cmd/api/main.go or main.go
Python: app.py, manage.py, __main__.py
JS/TS:  src/index.ts, server.ts, app.ts
```

Read main(). What does it initialize? What does it start? Follow the startup sequence.

### 2. Find the Router / Entry Points
```go
// What URLs does this app handle?
router.GET("/users/:id", handler.GetUser)
router.POST("/users", handler.CreateUser)
router.GET("/transactions", handler.ListTransactions)
```

This gives you the map of the application.

### 3. Trace One Request End-to-End
Pick the simplest endpoint. Follow it from HTTP handler → service → database → response. This reveals the architecture.

### 4. Read the Tests
Tests are executable documentation. They show expected behavior, edge cases, and how the code is intended to be used.

### 5. Check the README and docs/
Often outdated but still useful for intent and high-level architecture.

---

## Reading PRs Effectively

### Before Looking at Code
- Read the PR description — what problem does this solve?
- Check the linked ticket/issue — what was requested?
- Look at the file list — how many files changed? Which areas?

### While Reviewing
- **Start with the test files** — tests tell you what the change does
- **Read top-down** — public API first, implementation details second
- **Check error handling** — most bugs hide here
- **Look for missing cases** — what about nil? What about empty? What about concurrent access?

### Providing Feedback
```
❌ "This looks wrong"
❌ "I would do it differently"
✅ "This will panic if `user` is nil on line 42. Add a nil check."
✅ "Consider using a map here — the nested loop is O(n²), a map lookup would be O(n)."
```

Be specific. Be constructive. Suggest fixes, not just problems.

---

## Code Archaeology

### git blame — Who Changed This and Why
```bash
git blame src/user/service.go
# Shows who changed each line and when
```

### git log — History of a File
```bash
git log --oneline src/user/service.go
# Shows all commits that touched this file

git log -p src/user/service.go
# Shows the actual changes in each commit
```

### git log -S — Find When Something Was Added/Removed
```bash
git log -S "rateLimit" --oneline
# Find when "rateLimit" was introduced
```

---

## Reading Patterns

### Trace Data Flow
Follow the data: where does it enter? How is it transformed? Where does it end up?

```
HTTP request body → decode JSON → validate → transform → save to DB → encode response
```

### Identify the Boundaries
Where does one module end and another begin? What are the interfaces between them?

### Spot the Abstractions
What's being hidden? Why? Is the abstraction helping or just adding indirection?

---

## Exercises

1. Clone a popular open-source Go project (e.g., [chi](https://github.com/go-chi/chi)). Find the entry point. Trace a request from router to handler.

2. Review 3 PRs on a BlocLabs repo this week. For each, write one specific, constructive comment.

3. Use `git blame` on a file you work with. Find a line you don't understand. Read the commit that introduced it. Does the commit message explain why?

4. Clone a codebase you've never seen. Set a 30-minute timer. How much can you understand?

---

*← [Observability](06-observability.md) · [Developer Communication →](08-developer-communication.md)*
