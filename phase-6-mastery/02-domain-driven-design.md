# Domain-Driven Design

> Model the software after the business, not the other way around.

---

## Core Idea

DDD says: the most important thing in your software is the **domain model** — the business concepts, rules, and relationships. Everything else (HTTP, databases, frameworks) is infrastructure that serves the domain.

---

## Ubiquitous Language

Everyone — developers, product, business — uses the same terms.

```
❌ Developer says "record", product says "transaction", business says "transfer"
✅ Everyone says "transaction" — in code, docs, conversations, and database tables
```

If the code says `Transaction`, the database table is `transactions`, the API returns `transaction`, and the CEO talks about "transactions." One language. No translation layer.

---

## Strategic DDD — Bounded Contexts

Large systems have multiple domains. Each domain is a **bounded context** with its own models and language.

```
┌─────────────────┐     ┌─────────────────┐
│   Indexing       │     │   Payments       │
│   Context        │     │   Context        │
│                  │     │                  │
│   Block          │     │   Transaction    │
│   Transaction    │ ──▶ │   Wallet         │
│   Chain          │     │   Fee            │
│   Event          │     │   Receipt        │
└─────────────────┘     └─────────────────┘
         │
         ▼
┌─────────────────┐
│   Analytics      │
│   Context        │
│                  │
│   Metric         │
│   Report         │
│   TimeRange      │
└─────────────────┘
```

**Key insight:** "Transaction" means different things in different contexts. In Indexing, it's raw blockchain data. In Payments, it's a user's payment. Don't force one model to serve both.

---

## Tactical DDD — Building Blocks

### Entities
Have identity. Two users with the same name are different users.
```go
type User struct {
    ID    uuid.UUID  // Identity
    Name  string
    Email string
}
```

### Value Objects
Defined by their attributes. Two amounts of $100 are identical.
```go
type Money struct {
    Amount   decimal.Decimal
    Currency string
}
// Money{100, "USD"} == Money{100, "USD"}
```

### Aggregates
A cluster of entities/value objects treated as a unit. The aggregate root controls access.
```go
type Wallet struct {                  // Aggregate root
    ID           uuid.UUID
    UserID       uuid.UUID
    Balances     []Balance            // Part of aggregate
    Transactions []WalletTransaction  // Part of aggregate
}

// All modifications go through Wallet methods
func (w *Wallet) Deposit(amount Money) error {
    if amount.Amount.LessThanOrEqual(decimal.Zero) {
        return ErrInvalidAmount
    }
    // Update balance, record transaction
    return nil
}
```

### Domain Events
Something that happened in the domain. Past tense.
```go
type TransactionConfirmed struct {
    TransactionID uuid.UUID
    Chain         string
    BlockNumber   uint64
    Timestamp     time.Time
}

type UserCreated struct {
    UserID    uuid.UUID
    Email     string
    CreatedAt time.Time
}
```

Events decouple domains. Indexing publishes `TransactionConfirmed`. Payments and Analytics consume it independently.

### Repositories
Interface for persistence. Domain defines the contract; infrastructure implements it.
```go
type WalletRepository interface {
    GetByID(ctx context.Context, id uuid.UUID) (Wallet, error)
    Save(ctx context.Context, wallet Wallet) error
}
```

---

## How We Apply DDD at BlocLabs

1. **Identify bounded contexts** — Indexing, Payments, Auth, Analytics
2. **Define ubiquitous language** per context — terms used in code AND conversation
3. **Aggregates enforce business rules** — validation happens in the domain, not in handlers
4. **Events connect contexts** — contexts communicate through events, not direct calls
5. **Repositories abstract storage** — domain doesn't know about PostgreSQL

---

## When DDD Is Overkill

- Simple CRUD apps (no complex business rules)
- Prototypes (you're still discovering the domain)
- Tiny services with a single responsibility

**Use DDD when:** Business logic is complex, multiple teams work on the system, or the domain is the competitive advantage (like FabricBloc).

---

## Resources

- *Domain-Driven Design* — Eric Evans (the original, dense but essential)
- *Implementing Domain-Driven Design* — Vaughn Vernon (more practical)
- *Learning Domain-Driven Design* — Vlad Khononov (modern, approachable)
- [DDD Reference](https://www.domainlanguage.com/ddd/reference/) — Eric Evans' summary card

---

*← [System Design](01-system-design.md) · [Performance →](03-performance.md)*
