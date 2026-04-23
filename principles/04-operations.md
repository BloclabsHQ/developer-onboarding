# Operations, Reliability & Security Principles

## Observability

### Three Pillars
1. **Health**: Is the system alive? (liveness probes, heartbeats)
2. **Availability**: Can users complete their work? (readiness probes, SLIs)
3. **Debuggability**: When something breaks, can you find out why? (structured logs, traces, metrics)

### Monitoring Standards
- Structured logs over printf-style logging
- Percentiles over averages (p50, p95, p99)
- Alert on symptoms, not causes
- RED metrics for every service: Rate, Errors, Duration
- Every alert must have a runbook

## Reliability

### Error Budgets
100% uptime is the wrong target -- it means zero velocity. Define an error budget (e.g., 99.9% = 43 minutes/month of allowed downtime). Use the budget to balance reliability with feature velocity. When the budget is spent, freeze deployments and fix reliability.

### Graceful Degradation
Systems should degrade gracefully, not fail catastrophically. When a dependency is down:
- Serve stale data rather than no data
- Disable non-critical features rather than the whole product
- Queue writes for later rather than rejecting them

### The Testing Pyramid
- **Unit tests**: Domain logic, pure functions -- many, fast, reliable, cheap
- **Integration tests**: API contracts, data flows -- some, medium speed
- **End-to-end tests**: Critical user paths only -- few, slow, brittle, expensive

## Security

### Trust Nothing From the Network
Every API response is potentially malformed, tampered, outdated, or malicious. Validate everything. Parse defensively. Never interpolate user input into queries, commands, or templates without sanitization.

### Secrets Management
- Environment variables or secret managers for credentials. Never in code, config files, or logs.
- Rotate credentials on a schedule, not just after incidents
- Principle of least privilege: every service gets only the access it needs
- Audit access logs regularly

### Defense in Depth
No single security control is sufficient. Layer defenses:
- Input validation at the boundary
- Authentication and authorization at every layer
- Encryption in transit and at rest
- Rate limiting and abuse detection
- Monitoring and alerting on anomalies

## Performance

### The 16ms Budget
60fps = 16.67ms per frame. UI work must complete within this budget. Never block the main thread with I/O, computation, or synchronous network calls.

### Memory is Not Free
Profile with real tools before optimizing. Common culprits:
- Unbounded caches and queues
- Retain cycles in closures and callbacks
- Images loaded at full resolution instead of display size
- Leaked event listeners and subscriptions

### Battery and Resources are UX
Resource consumption directly affects user experience. Prefer:
- Push notifications over polling
- Significant location changes over continuous GPS
- Batch network requests over individual calls
- Reduced animation in low-power modes
