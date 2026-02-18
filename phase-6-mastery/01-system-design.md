# System Design

> Designing systems that work at scale, under failure, and over time.

---

## The Framework

Every system design follows this pattern:

```
1. Requirements    → What does the system DO?
2. Estimation      → How much load? How much data?
3. High-Level Design → Components and how they connect
4. Deep Dives      → Zoom into critical components
5. Trade-offs      → What are we giving up?
```

---

## Building Blocks

### Load Balancer
Distributes traffic across servers. If one server dies, traffic routes to others.
```
Client → [Load Balancer] → Server 1
                         → Server 2
                         → Server 3
```

### API Gateway
Single entry point. Handles auth, rate limiting, routing.

### Application Servers
Stateless processes that handle business logic. Scale by adding more.

### Database
Where state lives. The hardest part to scale.
- **Read replicas** — scale reads by copying to read-only servers
- **Sharding** — split data across databases (by user ID, region, etc.)
- **Connection pooling** — reuse database connections

### Cache
Reduce database load. Store frequently accessed data in memory.
```
Request → Cache HIT? → Return cached data
              MISS → Query DB → Cache result → Return
```

### Message Queue
Decouple producers from consumers. Handle spikes gracefully.
```
Producer → [SQS/Kafka] → Consumer
```
Producer doesn't wait for consumer. Queue absorbs bursts.

### CDN
Serve static assets from edge servers close to users.

---

## Scaling Strategies

### Vertical Scaling (Scale Up)
Bigger machine. More CPU, more RAM. Simple but has limits.

### Horizontal Scaling (Scale Out)
More machines. Requires stateless design. Theoretically unlimited.

### When to Scale What

| Bottleneck | Strategy |
|------------|----------|
| CPU-bound processing | Horizontal + more instances |
| Database reads | Read replicas + caching |
| Database writes | Sharding + write-behind caching |
| Network I/O | CDN + edge computing |
| Storage | Object storage (S3) + tiered storage |

---

## Example: Blockchain Indexer Architecture

```
┌──────────────┐     ┌──────────────┐
│  Blockchain   │     │  Blockchain   │
│  Node (EVM)   │     │  Node (SOL)   │
└──────┬───────┘     └──────┬───────┘
       │                     │
       ▼                     ▼
┌──────────────────────────────────┐
│         Ingestion Service         │
│   (receives blocks, validates)    │
└──────────────┬───────────────────┘
               │
               ▼
┌──────────────────────────────────┐
│         Message Queue (SQS)       │
│   (buffers blocks for processing) │
└──────────────┬───────────────────┘
               │
               ▼
┌──────────────────────────────────┐
│         Processing Service        │
│   (parse, transform, enrich)      │
└──────────────┬───────────────────┘
               │
               ▼
┌──────────────────────────────────┐
│    PostgreSQL (indexed data)      │
│    Redis (latest block cache)     │
└──────────────┬───────────────────┘
               │
               ▼
┌──────────────────────────────────┐
│         API Service               │
│   (serves indexed data to users)  │
└──────────────────────────────────┘
```

**Why this design:**
- **Queue** decouples ingestion from processing (blocks come fast, processing takes time)
- **Separate services** scale independently (more processors if backlogged)
- **Cache** reduces database load for hot data (latest blocks)
- **Stateless services** scale horizontally

---

## Consistency Models

| Model | Guarantee | Use Case |
|-------|-----------|----------|
| **Strong consistency** | Read always returns latest write | Financial transactions |
| **Eventual consistency** | Read may return stale data temporarily | Social media feeds |
| **Causal consistency** | Related events are ordered | Chat messages |

**Rule:** Default to strong consistency (PostgreSQL). Use eventual consistency only when you've proven strong consistency is too slow.

---

## Failure Modes

Every component will fail. Design for it:

| Failure | Mitigation |
|---------|------------|
| Server crash | Multiple instances behind load balancer |
| Database down | Read replicas, connection retry, circuit breaker |
| Network partition | Timeout, retry, graceful degradation |
| Cascading failure | Circuit breaker, bulkhead pattern |
| Data loss | Backups, replication, write-ahead log |

---

## Resources

- *Designing Data-Intensive Applications* — Martin Kleppmann (the bible)
- [System Design Primer](https://github.com/donnemartin/system-design-primer) — free, comprehensive
- *Building Microservices* — Sam Newman
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)

---

*← [Phase 5: Workflow](../phase-5-bloclabs-stack/08-workflow.md) · [Domain-Driven Design →](02-domain-driven-design.md)*
