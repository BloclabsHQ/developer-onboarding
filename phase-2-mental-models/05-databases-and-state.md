# Databases & State

> Your app's data will outlive your code. Design the data right.

---

## The Mental Model

Programs are temporary. Data is permanent. The database is the most important component of your system — it's the source of truth, the bottleneck, and the hardest thing to change.

---

## SQL Fundamentals

SQL (Structured Query Language) is the lingua franca of data. Every developer must know it.

### CRUD Operations

```sql
-- Create
INSERT INTO users (name, email, created_at)
VALUES ('Cris', 'cris@example.com', NOW());

-- Read
SELECT id, name, email FROM users WHERE id = 1;
SELECT * FROM users WHERE email LIKE '%@example.com' ORDER BY created_at DESC LIMIT 10;

-- Update
UPDATE users SET name = 'Cristian' WHERE id = 1;

-- Delete
DELETE FROM users WHERE id = 1;
```

### JOINs — Connecting Tables

```sql
-- Users have many transactions
SELECT u.name, t.amount, t.chain, t.status
FROM users u
JOIN transactions t ON t.user_id = u.id
WHERE t.status = 'confirmed'
ORDER BY t.created_at DESC;
```

| Join Type | Returns |
|-----------|---------|
| INNER JOIN | Only matching rows in both tables |
| LEFT JOIN | All rows from left + matching from right |
| RIGHT JOIN | All rows from right + matching from left |
| FULL JOIN | All rows from both, NULL where no match |

### Aggregations

```sql
-- Count transactions per chain
SELECT chain, COUNT(*) as tx_count, SUM(amount) as total_volume
FROM transactions
WHERE created_at > NOW() - INTERVAL '30 days'
GROUP BY chain
HAVING COUNT(*) > 100
ORDER BY total_volume DESC;
```

---

## ACID Properties

Every relational database guarantees ACID:

| Property | Meaning | Example |
|----------|---------|---------|
| **Atomicity** | All or nothing | Transfer: debit AND credit both succeed, or neither does |
| **Consistency** | Rules are enforced | Foreign keys, constraints, checks |
| **Isolation** | Transactions don't interfere | Two users buying last item — one succeeds, one fails |
| **Durability** | Committed = permanent | Server crashes after COMMIT — data survives |

**Mental model:** ACID means you can trust the database. When it says "done," it's done.

---

## Indexes — The #1 Performance Tool

Without an index, the database scans EVERY row (full table scan). With an index, it jumps directly to the right rows.

```sql
-- Without index: scan 10M rows every time (~seconds)
SELECT * FROM transactions WHERE user_id = 123;

-- Add index: jump directly to user 123's rows (~milliseconds)
CREATE INDEX idx_transactions_user_id ON transactions(user_id);
```

### When to Index
- Columns in `WHERE` clauses
- Columns in `JOIN` conditions
- Columns in `ORDER BY`
- Columns with high selectivity (many unique values)

### When NOT to Index
- Small tables (< 1000 rows — scan is fast enough)
- Columns you rarely query on
- Columns with low selectivity (boolean fields — only 2 values)

**Trade-off:** Indexes speed up reads but slow down writes (must update the index on every insert/update).

---

## Schema Design

### Normalization (Reduce Duplication)

```sql
-- ❌ Denormalized — chain name repeated millions of times
CREATE TABLE transactions (
    id SERIAL PRIMARY KEY,
    amount DECIMAL,
    chain_name VARCHAR(50),  -- "Ethereum" stored millions of times
    chain_symbol VARCHAR(10)
);

-- ✅ Normalized — chain data stored once
CREATE TABLE chains (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50),
    symbol VARCHAR(10)
);

CREATE TABLE transactions (
    id SERIAL PRIMARY KEY,
    amount DECIMAL,
    chain_id INTEGER REFERENCES chains(id)
);
```

### When to Denormalize
- Read performance is critical and writes are rare
- Reporting/analytics queries that join many tables
- Caching layers where pre-computed data saves time

**Mental model:** Normalize by default. Denormalize intentionally, with a reason.

---

## CAP Theorem

You can have at most 2 of 3:

```
        Consistency
           /\
          /  \
         /    \
Availability ── Partition Tolerance
```

- **Consistency:** Every read gets the latest write
- **Availability:** Every request gets a response
- **Partition Tolerance:** System works despite network failures

**In practice:** Network partitions WILL happen. So you choose between:
- **CP** (Consistent + Partition-tolerant): PostgreSQL, traditional databases. May refuse requests during partition.
- **AP** (Available + Partition-tolerant): DynamoDB, Cassandra. Always responds, but data may be stale.

**BlocLabs default:** PostgreSQL (CP). We prefer consistency — wrong data is worse than slow data.

---

## Caching

```
Request → Check Cache → HIT?
                         │
                    YES──┘──NO
                    │         │
                Return     Query DB
                cached     Cache result
                data       Return data
```

### When to Cache
- Data that's read often, written rarely
- Expensive computations
- API responses from third parties
- Session data

### Cache Invalidation
> "There are only two hard things in Computer Science: cache invalidation and naming things." — Phil Karlton

Strategies:
- **TTL (Time To Live):** Expire after N seconds. Simple, eventual consistency.
- **Write-through:** Update cache when DB updates. Consistent, slower writes.
- **Write-behind:** Update cache first, DB later. Fast, risk of data loss.
- **Cache-aside:** App manages cache manually. Most common.

### Redis

The most common cache:

```bash
# Set with 5 minute TTL
SET user:123:profile '{"name":"Cris"}' EX 300

# Get
GET user:123:profile

# Delete (invalidate)
DEL user:123:profile
```

---

## SQL vs NoSQL

| Feature | SQL (PostgreSQL) | NoSQL (MongoDB, DynamoDB) |
|---------|-----------------|--------------------------|
| Schema | Strict, defined upfront | Flexible, schema-less |
| Relationships | JOINs, foreign keys | Embedded docs, denormalized |
| Transactions | Full ACID | Limited (varies) |
| Scaling | Vertical (bigger machine) | Horizontal (more machines) |
| Query language | SQL (standard) | Varies per database |
| Best for | Complex queries, relationships, consistency | Simple lookups, high write throughput, flexible schema |

**BlocLabs default:** PostgreSQL. NoSQL when we need specific patterns (DynamoDB for high-throughput key-value, Redis for caching).

---

## Connection Pooling

Opening a database connection is expensive (~50ms). Don't open one per request.

```
Without pooling:
Request 1 → open connection → query → close connection    (50ms overhead)
Request 2 → open connection → query → close connection    (50ms overhead)

With pooling:
Startup → open 10 connections
Request 1 → borrow connection → query → return connection (0ms overhead)
Request 2 → borrow connection → query → return connection (0ms overhead)
```

Every language has connection pool libraries. Use them. Always.

---

## Migrations

Schema changes are managed with migrations — versioned SQL scripts:

```
migrations/
├── 001_create_users.sql
├── 002_create_transactions.sql
├── 003_add_email_to_users.sql
└── 004_add_index_on_transactions_chain.sql
```

**Rules:**
- Migrations are append-only (never edit an applied migration)
- Always test rollback (`DOWN` migration)
- Never drop columns in production without a deprecation period
- Use tools: `golang-migrate`, `alembic` (Python), `knex` (JS)

---

## Exercises

1. Design a schema for a blockchain transaction tracker: users, wallets, chains, transactions. Write the CREATE TABLE statements with proper indexes.

2. Write a query that finds the top 5 users by transaction volume in the last 30 days, broken down by chain.

3. Set up Redis locally (`brew install redis`), write a script that caches API responses with a 5-minute TTL.

4. Use `EXPLAIN ANALYZE` on a slow query. Add an index and measure the improvement.

5. Write UP and DOWN migrations for adding a `status` column to a transactions table.

---

## Recommended Reading

- [Use The Index, Luke](https://use-the-index-luke.com/) — SQL indexing deep dive (free)
- [PostgreSQL Tutorial](https://www.postgresqltutorial.com/) — practical learning
- *Designing Data-Intensive Applications* — Martin Kleppmann (the bible)

---

*← [Networking Fundamentals](04-networking-fundamentals.md) · [Problem Solving →](06-problem-solving.md)*
