# PostgreSQL

> Our primary database. Know it deeply.

---

## Why PostgreSQL

- ACID transactions (data integrity guaranteed)
- Rich data types (JSONB, arrays, UUIDs, timestamps with time zones)
- Advanced indexing (B-tree, GIN, GiST, BRIN)
- Mature, battle-tested, open source
- Extensions (PostGIS, pg_trgm, pgcrypto)

---

## Schema Design Principles

### Use Appropriate Types
```sql
CREATE TABLE transactions (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id     UUID NOT NULL REFERENCES users(id),
    amount      NUMERIC(20, 8) NOT NULL,   -- Exact decimals, not FLOAT
    chain       TEXT NOT NULL,
    status      TEXT NOT NULL DEFAULT 'pending',
    tx_hash     TEXT,
    block_number BIGINT,
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at  TIMESTAMPTZ NOT NULL DEFAULT NOW(),

    CONSTRAINT positive_amount CHECK (amount > 0),
    CONSTRAINT valid_status CHECK (status IN ('pending', 'confirmed', 'failed'))
);
```

### Rules
- **UUID for IDs** — no sequential integers (security, distributed systems)
- **NUMERIC for money** — never FLOAT (rounding errors)
- **TIMESTAMPTZ** — always with time zone (never TIMESTAMP)
- **TEXT over VARCHAR** — PostgreSQL treats them identically, TEXT is simpler
- **NOT NULL by default** — add NULL only when truly optional
- **CHECK constraints** — enforce business rules at the database level

---

## Indexes

### When to Create
```sql
-- Columns in WHERE clauses
CREATE INDEX idx_transactions_user_id ON transactions(user_id);

-- Columns in JOINs
CREATE INDEX idx_transactions_chain ON transactions(chain);

-- Composite for multi-column queries
CREATE INDEX idx_transactions_user_status ON transactions(user_id, status);

-- Partial index for specific queries
CREATE INDEX idx_pending_transactions ON transactions(created_at)
    WHERE status = 'pending';
```

### Index Types
| Type | Use Case |
|------|----------|
| B-tree (default) | Equality, range, sorting |
| GIN | Full-text search, JSONB, arrays |
| GiST | Geometric, range types |
| BRIN | Large tables with natural ordering (time-series) |

---

## Query Optimization

### EXPLAIN ANALYZE
```sql
EXPLAIN ANALYZE
SELECT * FROM transactions
WHERE user_id = '123' AND status = 'confirmed'
ORDER BY created_at DESC
LIMIT 10;
```

**Read the output:**
- `Seq Scan` → no index used (bad for large tables)
- `Index Scan` → good, using an index
- `Bitmap Index Scan` → good, using index for filtering
- `actual time=X..Y` → real execution time
- `rows=N` → actual rows processed

### Common Anti-Patterns
```sql
-- ❌ SELECT * (fetches unnecessary columns)
SELECT * FROM transactions WHERE user_id = '123';

-- ✅ Select only needed columns
SELECT id, amount, status, created_at
FROM transactions WHERE user_id = '123';

-- ❌ No LIMIT on large tables
SELECT * FROM transactions ORDER BY created_at DESC;

-- ✅ Always LIMIT
SELECT id, amount FROM transactions ORDER BY created_at DESC LIMIT 25;

-- ❌ Function on indexed column (can't use index)
SELECT * FROM users WHERE LOWER(email) = 'cris@example.com';

-- ✅ Functional index or store normalized
CREATE INDEX idx_users_email_lower ON users(LOWER(email));
```

---

## Migrations

```bash
# Using golang-migrate
migrate create -ext sql -dir migrations -seq add_transactions_table
```

```sql
-- migrations/000001_add_transactions_table.up.sql
CREATE TABLE transactions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    -- ...
);

-- migrations/000001_add_transactions_table.down.sql
DROP TABLE IF EXISTS transactions;
```

**Rules:**
- Every schema change is a migration
- Always write both UP and DOWN
- Test DOWN migrations before deploying
- Never edit an applied migration
- Migrations are append-only

---

## Connection Pooling

```
Without pool: each request opens/closes connection (~50ms overhead)
With pool: connections reused, near-zero overhead
```

Use `pgxpool` (Go), `asyncpg` (Python), or PgBouncer (external pooler).

```go
pool, err := pgxpool.New(context.Background(), databaseURL)
// pool manages connections, reuses them, handles limits
```

**Production settings:**
- `max_connections`: 100 (PostgreSQL default) — don't go much higher
- Pool size: 10-25 per service instance
- Connection timeout: 5 seconds
- Idle timeout: 30 minutes

---

## Useful Commands

```bash
psql -d mydb                    # Connect
\dt                             # List tables
\d transactions                 # Describe table
\di                             # List indexes
\x                              # Toggle expanded display
\timing                         # Show query timing
```

---

## Resources

- [PostgreSQL Documentation](https://www.postgresql.org/docs/current/)
- [Use The Index, Luke](https://use-the-index-luke.com/)
- [pgexercises.com](https://pgexercises.com/) — interactive SQL practice

---

*← [Python for Developers](04-python-for-devs.md) · [AWS & Cloudflare →](06-aws-and-cloudflare.md)*
