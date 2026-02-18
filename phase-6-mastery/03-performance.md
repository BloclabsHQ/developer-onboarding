# Performance

> Don't guess. Measure. Optimize the bottleneck. Stop when it's fast enough.

---

## The Rules

1. **Make it work** first
2. **Make it right** (clean, tested, maintainable)
3. **Make it fast** (only if necessary)

Premature optimization is the root of all evil. But when you need to optimize, do it scientifically.

---

## The Process

```
1. Define "fast enough" (SLA, user expectation)
2. Measure current performance (benchmark, profile)
3. Find the bottleneck (profiler, not guessing)
4. Fix the bottleneck
5. Measure again (did it actually improve?)
6. Stop when "fast enough" is met
```

---

## Profiling

### Go — pprof
```go
import _ "net/http/pprof"

// In main():
go func() {
    http.ListenAndServe("localhost:6060", nil)
}()
```

```bash
# CPU profile
go tool pprof http://localhost:6060/debug/pprof/profile?seconds=30

# Memory profile
go tool pprof http://localhost:6060/debug/pprof/heap

# Goroutine profile (find leaks)
go tool pprof http://localhost:6060/debug/pprof/goroutine

# Interactive commands
(pprof) top 10        # Hottest functions
(pprof) web           # Visual call graph
(pprof) list funcName # Line-by-line breakdown
```

### Go — Benchmarks
```go
func BenchmarkParseTransaction(b *testing.B) {
    data := loadTestData()
    b.ResetTimer()
    for i := 0; i < b.N; i++ {
        ParseTransaction(data)
    }
}
```

```bash
go test -bench=. -benchmem ./...
# BenchmarkParseTransaction-8   50000   24356 ns/op   4096 B/op   12 allocs/op
```

### Python — py-spy
```bash
pip install py-spy
py-spy top --pid <PID>          # Live top-like view
py-spy record -o profile.svg -- python app.py  # Flame graph
```

### Database — EXPLAIN ANALYZE
```sql
EXPLAIN (ANALYZE, BUFFERS, FORMAT TEXT)
SELECT * FROM transactions
WHERE user_id = '123' AND status = 'confirmed'
ORDER BY created_at DESC LIMIT 10;
```

Read the output: `Seq Scan` = problem. `Index Scan` = good. `actual time` = real performance.

---

## Common Bottlenecks and Fixes

### N+1 Queries
```go
// ❌ N+1: 1 query for users + N queries for transactions
users := db.GetAllUsers()
for _, u := range users {
    txs := db.GetTransactions(u.ID)  // Query per user!
}

// ✅ Batch: 2 queries total
users := db.GetAllUsers()
userIDs := extractIDs(users)
txMap := db.GetTransactionsByUserIDs(userIDs)  // Single query
```

### Missing Database Index
```sql
-- Query takes 3 seconds on 5M rows
SELECT * FROM transactions WHERE chain = 'ethereum' AND status = 'pending';

-- Add composite index → 5ms
CREATE INDEX idx_tx_chain_status ON transactions(chain, status);
```

### Unnecessary Allocations
```go
// ❌ Allocates new string on every iteration
var result string
for _, s := range items {
    result += s
}

// ✅ Pre-allocated builder
var b strings.Builder
b.Grow(estimatedSize)
for _, s := range items {
    b.WriteString(s)
}
```

### Serialization
```go
// ❌ JSON marshaling is slow for hot paths
data, _ := json.Marshal(bigStruct)

// ✅ Use faster serializers for internal communication
// Protocol Buffers, MessagePack, or flatbuffers
```

### Connection Pool Exhaustion
```
Symptom: requests queue, latency spikes
Cause: too many concurrent DB connections, pool too small
Fix: increase pool size, add connection timeout, optimize long queries
```

---

## Caching Strategy

```
L1: In-process cache (fastest, per-instance, lost on restart)
L2: Distributed cache - Redis (fast, shared, survives restarts)
L3: Database (slow, authoritative)
```

### Cache-Aside Pattern
```go
func GetUser(ctx context.Context, id string) (User, error) {
    // Check cache
    if cached, ok := cache.Get("user:" + id); ok {
        return cached.(User), nil
    }
    // Miss: query database
    user, err := db.GetUser(ctx, id)
    if err != nil {
        return User{}, err
    }
    // Store in cache (5 min TTL)
    cache.Set("user:"+id, user, 5*time.Minute)
    return user, nil
}
```

---

## The 80/20 Rule

80% of performance problems come from 20% of the code. Find that 20%.

| Common Hot Spots | Why |
|-----------------|-----|
| Database queries | I/O bound, missing indexes, N+1 |
| Serialization | CPU bound, called on every request |
| External API calls | Network latency, no batching |
| Logging | Excessive logging in hot paths |
| Memory allocation | GC pressure in tight loops |

---

## Checklist

- [ ] Performance SLA defined ("p99 < 200ms")
- [ ] Profiled with real data, not guessing
- [ ] Bottleneck identified and fixed
- [ ] Improvement measured and verified
- [ ] Database queries have matching indexes
- [ ] No N+1 queries
- [ ] Hot paths are cached
- [ ] Stopped optimizing when "fast enough" is met

---

*← [Domain-Driven Design](02-domain-driven-design.md) · [Security →](04-security.md)*
