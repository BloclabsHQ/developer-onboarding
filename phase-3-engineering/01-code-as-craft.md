# Code as Craft

> Correctness > Clarity > Conciseness > Performance. In that order.

---

## The Priority Stack

Every line of code you write should pass through these filters, in order:

1. **Correct** — Does it do the right thing? Does it handle edge cases?
2. **Clear** — Can someone else (or future you) understand it in 30 seconds?
3. **Concise** — Is there unnecessary code? Can it be simpler?
4. **Performant** — Is it fast enough? (NOT "as fast as possible")

Most juniors optimize for conciseness or performance first. This is backwards. Clever code that's hard to read is technical debt. Readable code that's slightly verbose is an asset.

---

## Naming

The most important skill in programming. Bar none.

### Variables
```go
// ❌ What is this?
d := time.Now().Sub(s)
x := u.A * r

// ✅ Self-documenting
elapsed := time.Since(startTime)
totalCost := unitPrice * quantity
```

### Functions
```go
// ❌ What does this do?
func proc(d []byte) error { ... }
func handle(r *Request) { ... }

// ✅ Verb + noun, describes the action
func parseTransaction(data []byte) (Transaction, error) { ... }
func validateUserInput(req *CreateUserRequest) error { ... }
```

### Booleans
```go
// ❌ Ambiguous
var status bool
var flag bool

// ✅ Reads like English
var isActive bool
var hasPermission bool
var shouldRetry bool
```

### Rules
- **Length proportional to scope.** Loop variable: `i`. Package-level variable: `transactionRetryCount`.
- **Domain language.** Use the terms your team uses. If the business says "ledger," don't call it "record_list."
- **No abbreviations** except universally known ones (URL, HTTP, ID, DB).
- **Consistent.** If one function says `fetchUser`, don't have another say `getUser` and a third say `retrieveUser`.

---

## Function Design

### Do One Thing

```go
// ❌ Does three things
func processOrder(order Order) error {
    // validate
    if order.Amount <= 0 { return errors.New("invalid amount") }
    // save
    if err := db.Save(order); err != nil { return err }
    // notify
    sendEmail(order.UserEmail, "Order confirmed")
    return nil
}

// ✅ Each function does one thing
func validateOrder(order Order) error { ... }
func saveOrder(order Order) error { ... }
func notifyOrderConfirmed(order Order) error { ... }

func processOrder(order Order) error {
    if err := validateOrder(order); err != nil { return err }
    if err := saveOrder(order); err != nil { return err }
    return notifyOrderConfirmed(order)
}
```

### Keep Functions Short

If a function is longer than **30 lines**, it's probably doing too much. Extract.

If a function has more than **3-4 parameters**, group them into a struct/object.

### Return Early

```go
// ❌ Nested (pyramid of doom)
func process(data []byte) error {
    if data != nil {
        if len(data) > 0 {
            result, err := parse(data)
            if err == nil {
                return save(result)
            }
            return err
        }
        return errors.New("empty data")
    }
    return errors.New("nil data")
}

// ✅ Guard clauses — return early
func process(data []byte) error {
    if data == nil {
        return errors.New("nil data")
    }
    if len(data) == 0 {
        return errors.New("empty data")
    }
    result, err := parse(data)
    if err != nil {
        return fmt.Errorf("parsing: %w", err)
    }
    return save(result)
}
```

---

## Code Smells

### DRY (Don't Repeat Yourself)
If you copy-paste code more than twice, extract it into a function.

**But:** Don't DRY too aggressively. Two pieces of code that look the same but serve different purposes should stay separate. Premature abstraction is worse than duplication.

### YAGNI (You Aren't Gonna Need It)
Don't build features, abstractions, or optimizations you don't need yet.

```go
// ❌ YAGNI — building a "framework" for one use case
type PluginRegistry struct { ... }
type PluginLoader interface { ... }
type PluginManager struct { ... }
// When all you need is:

func processCSV(data []byte) ([]Record, error) { ... }
```

### KISS (Keep It Simple, Stupid)
The simplest solution that works is usually the best solution.

---

## Comments

### Good Comments Explain WHY, Not WHAT

```go
// ❌ Says what the code does (obvious from reading it)
// Increment counter by 1
counter++

// ✅ Says WHY — context you can't get from code alone
// Rate limit: max 100 requests per minute per user.
// The sliding window resets on the first request after the window expires.
if requestCount >= 100 {
    return ErrRateLimited
}
```

### When to Comment
- **Why** a decision was made (especially non-obvious ones)
- **Context** that isn't in the code (business rules, regulatory requirements)
- **Warnings** about gotchas or known issues
- **TODOs** for future work (with ticket reference)

### When NOT to Comment
- What the code does (make the code readable instead)
- Commented-out code (delete it, git remembers)
- Obvious things

---

## Cognitive Load

Your brain can hold ~5-7 things in working memory at once.

**Every variable, every branch, every abstraction** takes a slot. When a function exceeds this, you lose track and bugs appear.

### The Rule of Five
- Max 5 parameters per function
- Max 5 levels of nesting
- Max 5 responsibilities per module
- Max 5 things to understand to read a function

```go
// ❌ Cognitive overload — 4 levels of nesting, 3 conditions to track
func process(items []Item) {
    for _, item := range items {
        if item.IsActive {
            for _, sub := range item.SubItems {
                if sub.Value > 0 {
                    if sub.Type == "payment" {
                        // What were we doing again?
                    }
                }
            }
        }
    }
}

// ✅ Flat, linear, each step clear
func process(items []Item) {
    active := filterActive(items)
    payments := extractPayments(active)
    positivePayments := filterPositive(payments)
    processPayments(positivePayments)
}
```

---

## Formatting

Use your language's formatter. No exceptions. No debates.

| Language | Formatter | Command |
|----------|-----------|---------|
| Go | gofmt | `gofmt -w .` (automatic) |
| Rust | rustfmt | `cargo fmt` |
| Python | black / ruff | `ruff format .` |
| JS/TS | prettier | `npx prettier --write .` |

**Format on save.** Configure your editor. Never submit unformatted code.

---

## Exercises

1. Take a function you wrote recently (>20 lines). Refactor it to follow the principles here. Is it clearer?

2. Review a PR from a teammate. For every variable and function name, ask: "Could someone understand this without context?" Rename where needed.

3. Find a piece of commented-out code in your codebase. Delete it. (Git has it forever.)

4. Find a function with >4 levels of nesting. Refactor using guard clauses and extraction.

---

## Recommended Reading

- *Clean Code* — Robert C. Martin (controversial but foundational)
- *A Philosophy of Software Design* — John Ousterhout (better than Clean Code, less dogmatic)
- *The Pragmatic Programmer* — Hunt & Thomas

---

*← [Phase 2: Operating Systems](../phase-2-mental-models/07-operating-systems.md) · [Error Handling →](02-error-handling.md)*
