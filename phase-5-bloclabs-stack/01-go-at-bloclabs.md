# Go at BlocLabs

> Go is our primary backend language. Simple, fast, and built for the problems we solve.

---

## Why Go

- **Simple** — small language, easy to read, hard to write "clever" code
- **Fast** — compiles to native code, low memory footprint
- **Concurrent** — goroutines and channels built into the language
- **Standard library** — HTTP server, JSON, crypto, testing — all built in
- **Single binary** — compile once, deploy anywhere, no runtime dependencies

---

## Project Structure (Ardanlabs-Inspired)

```
service/
├── cmd/
│   └── api/
│       └── main.go              # Entry point, wiring
├── internal/
│   ├── domain/
│   │   ├── user/
│   │   │   ├── model.go         # Domain types
│   │   │   ├── service.go       # Business logic
│   │   │   └── repository.go    # Interface
│   │   └── transaction/
│   │       ├── model.go
│   │       ├── service.go
│   │       └── repository.go
│   ├── handler/
│   │   ├── user.go              # HTTP handlers
│   │   └── transaction.go
│   ├── storage/
│   │   └── postgres/
│   │       ├── user.go          # Repository implementation
│   │       └── transaction.go
│   └── platform/
│       ├── database/            # DB connection
│       ├── auth/                # Auth middleware
│       └── web/                 # HTTP helpers
├── migrations/
├── Dockerfile
├── Makefile
└── go.mod
```

---

## Error Handling — The Go Way

```go
// Always check errors
result, err := doSomething()
if err != nil {
    return fmt.Errorf("doing something for user %s: %w", userID, err)
}

// Custom error types for domain errors
type NotFoundError struct {
    Entity string
    ID     string
}

func (e *NotFoundError) Error() string {
    return fmt.Sprintf("%s %s not found", e.Entity, e.ID)
}

// Checking error types
var nf *NotFoundError
if errors.As(err, &nf) {
    // Return 404
}
```

---

## Concurrency Patterns

### Goroutines + WaitGroup
```go
var wg sync.WaitGroup
for _, chain := range chains {
    wg.Add(1)
    go func(c Chain) {
        defer wg.Done()
        indexChain(c)
    }(chain)
}
wg.Wait()
```

### Context for Cancellation
```go
ctx, cancel := context.WithTimeout(context.Background(), 30*time.Second)
defer cancel()

result, err := longRunningOp(ctx)
if err != nil {
    if errors.Is(err, context.DeadlineExceeded) {
        log.Warn("operation timed out")
    }
    return err
}
```

### Channels for Communication
```go
results := make(chan BlockData, len(chains))
errs := make(chan error, len(chains))

for _, chain := range chains {
    go func(c Chain) {
        data, err := fetchBlock(c)
        if err != nil {
            errs <- err
            return
        }
        results <- data
    }(chain)
}
```

---

## Testing in Go

### Table-Driven Tests
```go
func TestValidateEmail(t *testing.T) {
    tests := []struct {
        name  string
        email string
        valid bool
    }{
        {"valid email", "user@example.com", true},
        {"no domain", "user@", false},
        {"empty", "", false},
        {"no at sign", "userexample.com", false},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            err := ValidateEmail(tt.email)
            if tt.valid && err != nil {
                t.Errorf("expected valid, got error: %v", err)
            }
            if !tt.valid && err == nil {
                t.Errorf("expected error for %q", tt.email)
            }
        })
    }
}
```

### Running Tests
```bash
go test ./...                    # All tests
go test -v ./internal/user/...   # Verbose, specific package
go test -race ./...              # Detect race conditions
go test -cover ./...             # Coverage report
go test -bench=. ./...           # Benchmarks
```

---

## Common Packages

| Package | Purpose |
|---------|---------|
| `net/http` | HTTP server (or chi/echo for routing) |
| `encoding/json` | JSON encoding/decoding |
| `database/sql` + `pgx` | PostgreSQL |
| `context` | Cancellation, timeouts, request-scoped values |
| `fmt` | Formatting and error wrapping |
| `errors` | Error comparison and wrapping |
| `slog` | Structured logging (Go 1.21+) |
| `testing` | Unit and benchmark tests |
| `sync` | Mutexes, WaitGroups, atomic operations |

---

## Go Code Review Checklist

- [ ] Every error is handled (no `_` on error returns)
- [ ] Errors are wrapped with context (`fmt.Errorf("...: %w", err)`)
- [ ] `context.Context` is the first parameter where appropriate
- [ ] No goroutine leaks (goroutines have a way to exit)
- [ ] Race detector passes (`go test -race`)
- [ ] `defer` for cleanup (close files, connections, mutexes)
- [ ] Exported functions have doc comments
- [ ] `gofmt` / `goimports` applied
- [ ] No global mutable state

---

## Resources

- [Effective Go](https://go.dev/doc/effective_go)
- [Go Blog](https://go.dev/blog/)
- [Ardanlabs Service Wiki](https://github.com/ardanlabs/service/wiki) — our architecture reference
- [Standard Go Project Layout](https://github.com/golang-standards/project-layout)
- [Go Code Review Comments](https://go.dev/wiki/CodeReviewComments)

---

*← [Phase 4: AI Ethics](../phase-4-ai-augmented-dev/05-ai-ethics-and-limits.md) · [Rust at BlocLabs →](02-rust-at-bloclabs.md)*
