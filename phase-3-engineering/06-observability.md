# Observability

> If you can't see it, you can't fix it. If you can't measure it, you can't improve it.

---

## The Three Pillars

### 1. Logs — What Happened
```go
// ❌ Unstructured — impossible to search at scale
fmt.Println("user created")
log.Printf("error: %v", err)

// ✅ Structured — searchable, filterable, parseable
logger.Info("user created",
    "user_id", user.ID,
    "email", user.Email,
    "duration_ms", elapsed.Milliseconds(),
)

logger.Error("database query failed",
    "query", "GetUserByID",
    "user_id", userID,
    "error", err.Error(),
    "duration_ms", elapsed.Milliseconds(),
)
```

**Log levels:**
- **ERROR** — something broke, needs attention
- **WARN** — something unexpected, but handled
- **INFO** — significant business events (user created, payment processed)
- **DEBUG** — detailed technical info (SQL queries, request/response bodies)

**Rules:**
- Structured JSON in production
- Include request ID in every log (trace a request across services)
- Never log secrets (tokens, passwords, PII)
- DEBUG off in production (too much noise/volume)

### 2. Metrics — How Much / How Fast
```
http_requests_total{method="GET", path="/users", status="200"}  → Counter
http_request_duration_seconds{method="GET", path="/users"}       → Histogram
active_connections                                                → Gauge
db_query_duration_seconds{query="GetUserByID"}                   → Histogram
```

**Four golden signals:**
1. **Latency** — how long requests take (p50, p95, p99)
2. **Traffic** — requests per second
3. **Errors** — error rate (5xx / total)
4. **Saturation** — how full is the system (CPU, memory, connections)

### 3. Traces — Where Time Goes
A trace follows a single request across services:

```
[Client] ─── 200ms ───▶ [API Gateway] ─── 5ms ───▶ [Auth Service]
                              │
                              ├── 150ms ───▶ [User Service]
                              │                    │
                              │                    └── 120ms ───▶ [Database]
                              │
                              └── 10ms ────▶ [Cache]
```

From this trace: 120ms of 200ms is the database query. That's where to optimize.

---

## Alerting

Don't alert on everything. Alert on what needs human action.

**Good alerts:**
- Error rate > 5% for 5 minutes
- p99 latency > 2 seconds for 10 minutes
- Disk usage > 85%
- Zero successful requests for 2 minutes

**Bad alerts:**
- Any single error (noise)
- CPU > 70% (expected during peaks)
- Every deployment (not actionable)

---

## The Mental Model

```
Logs    → "What went wrong?" (debugging after the fact)
Metrics → "Is something wrong?" (dashboards, alerting)
Traces  → "Where is it slow?" (performance investigation)
```

Use all three together. Metrics tell you THAT something is wrong. Logs tell you WHAT happened. Traces tell you WHERE the time went.

---

## Tools

| Category | Tools |
|----------|-------|
| Logging | ELK Stack, Datadog, CloudWatch Logs |
| Metrics | Prometheus + Grafana, Datadog, CloudWatch |
| Tracing | Jaeger, Zipkin, Datadog APM |
| All-in-one | Datadog, New Relic, Grafana Cloud |

---

## Checklist

- [ ] Structured logging with levels (not fmt.Println)
- [ ] Request ID in every log entry
- [ ] No secrets in logs
- [ ] Four golden signals measured (latency, traffic, errors, saturation)
- [ ] Dashboards for key metrics
- [ ] Alerts for actionable conditions only
- [ ] Traces for cross-service requests

---

*← [API Design](05-api-design.md) · [Reading Code →](07-reading-code.md)*
