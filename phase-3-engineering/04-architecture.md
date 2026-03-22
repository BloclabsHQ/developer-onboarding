# Architecture

> Good architecture makes change easy. Bad architecture makes change terrifying.

---

## The Core Principle: Dependencies Point Inward

```
┌───────────────────────────────────────────┐
│            API / Handlers                  │  Knows about App layer
│  ┌───────────────────────────────────┐    │
│  │         Application / Services     │    │  Knows about Business layer
│  │  ┌───────────────────────────┐    │    │
│  │  │     Business / Domain      │    │    │  Knows about nothing
│  │  │     (Models, Rules)        │    │    │
│  │  └───────────────────────────┘    │    │
│  └───────────────────────────────────┘    │
│            Storage / Adapters              │  Implements interfaces
└───────────────────────────────────────────┘
```

**Rules:**
- Inner layers know NOTHING about outer layers
- Outer layers depend on inner layers, never the reverse
- Business logic has zero dependencies on HTTP, database, or frameworks

---

## The Four Layers

### 1. API Layer (Handlers, Controllers)
- Receives HTTP requests, WebSocket messages, CLI commands
- Validates input format (is this valid JSON? are required fields present?)
- Calls the Application layer
- Returns formatted responses

### 2. Application Layer (Services, Use Cases)
- Orchestrates business operations
- Manages transactions
- Calls Business layer for rules, Storage layer for data
- Contains no business rules itself

### 3. Business Layer (Domain)
- Pure business logic and rules
- Domain models (User, Transaction, Wallet)
- Validation rules (is this transaction valid?)
- Zero imports of external packages

### 4. Storage Layer (Repositories, Adapters)
- Database queries, API calls, file I/O
- Implements interfaces defined by the Business layer
- Converts between domain models and storage formats

---

## Package-Oriented Design (Go)

```
project/
├── cmd/
│   └── api/
│       └── main.go              # Entry point
├── internal/
│   ├── user/                    # Domain: User
│   │   ├── model.go             # User struct, validation
│   │   ├── service.go           # Business logic
│   │   ├── repository.go        # Interface (what storage must do)
│   │   └── handler.go           # HTTP handlers
│   ├── transaction/             # Domain: Transaction
│   │   ├── model.go
│   │   ├── service.go
│   │   ├── repository.go
│   │   └── handler.go
│   └── platform/                # Shared infrastructure
│       ├── database/            # DB connection, migrations
│       ├── auth/                # Authentication middleware
│       └── logger/              # Structured logging
├── pkg/                         # Public libraries (if any)
├── migrations/                  # SQL migrations
├── Dockerfile
├── docker-compose.yml
└── go.mod
```

**Three firewalls:**
1. **Packaging** — code inside a package can see everything. Outside must use exported symbols.
2. **Layering** — API imports Services. Services import Domain. Never the reverse.
3. **Domains** — User domain doesn't import Transaction domain directly. They communicate through the Application layer.

---

## Interfaces — The Boundary

Define what you need, not what exists:

```go
// In business layer — defines the contract
type UserRepository interface {
    GetByID(ctx context.Context, id string) (User, error)
    Create(ctx context.Context, user User) error
    Update(ctx context.Context, user User) error
}

// In storage layer — implements the contract
type PostgresUserRepository struct {
    db *sql.DB
}

func (r *PostgresUserRepository) GetByID(ctx context.Context, id string) (User, error) {
    // Actual SQL query here
}
```

**Why interfaces matter:**
- Swap PostgreSQL for DynamoDB without touching business logic
- Test business logic with a fake repository
- Business layer doesn't know (or care) where data comes from

---

## Dependency Injection

Pass dependencies in, don't create them inside:

```go
// ❌ Hard-coded dependency
func NewUserService() *UserService {
    db := database.Connect()  // Creates its own DB connection
    return &UserService{repo: NewPostgresRepo(db)}
}

// ✅ Injected dependency
func NewUserService(repo UserRepository) *UserService {
    return &UserService{repo: repo}
}

// Wired up in main.go
func main() {
    db := database.Connect(config.DatabaseURL)
    repo := postgres.NewUserRepository(db)
    service := user.NewUserService(repo)
    handler := user.NewHandler(service)
    // ...
}
```

---

## When to Use Microservices vs Monolith

**Start with a monolith.** Always. Split when you have a reason.

| Signal to Split | Example |
|----------------|---------|
| Teams stepping on each other | 5 devs can't deploy independently |
| Different scaling needs | Auth: 1000 rps, Indexer: 10 rps |
| Different technology needs | Indexer in Rust, API in Go |
| Deployment independence | Need to deploy auth without touching billing |

**Do NOT split because:**
- "Microservices are modern"
- "It might need to scale someday"
- "Netflix does it" (you're not Netflix)

---

## Checklist

- [ ] Dependencies point inward
- [ ] Business logic has zero framework imports
- [ ] Interfaces define boundaries between layers
- [ ] Dependencies are injected, not created
- [ ] Each package/module has a single responsibility
- [ ] You can test business logic without a database

---

## Recommended Reading

- *A Philosophy of Software Design* — John Ousterhout
- *Clean Architecture* — Robert C. Martin
- [Ardanlabs Service Wiki](https://github.com/ardanlabs/service/wiki) — Go architecture (our reference)
- [Ardanlabs Design Guidelines](https://github.com/ardanlabs/service/wiki/design-guidelines) — design philosophy
- [Ardanlabs DDD](https://github.com/ardanlabs/service/wiki/Domain-Driven,-Data-Oriented-Design) — domain-driven, data-oriented design
- [Ardanlabs Project Structure](https://github.com/ardanlabs/service/wiki/project-structure) — package layout

---

*← [Testing](03-testing.md) · [API Design →](05-api-design.md)*
