# Systems Thinking

> Software is not lines of code. It's a system of interacting components. Think in systems.

---

## What is a System?

A system is anything with **inputs**, **processes**, and **outputs** that interact to produce behavior.

```
Inputs ──▶ [Process] ──▶ Outputs
              │
              ▼
          Side Effects
```

Your web app is a system. Your database is a system. Your team is a system. The internet is a system of systems. Learning to see systems is the most important mental upgrade you'll make.

---

## First-Order vs Second-Order Thinking

**First-order:** What happens when I do X?
**Second-order:** What happens *because* of what happens when I do X?

```
First-order:  "Add caching → faster response times"
Second-order: "Add caching → stale data → users see outdated prices → lost revenue"
Second-order: "Add caching → less DB load → can handle 10x users → revenue grows"
```

Both second-order effects are real. The difference between a junior and senior engineer is *seeing both* before deploying.

**Exercise:** For every technical decision, ask: "And then what?" three times.

---

## Feedback Loops

### Positive (Reinforcing) Feedback
More of A causes more of B, which causes more of A. Growth or collapse.

```
More users → more content → more users → more content → ...
(Twitter, Reddit, any platform)

More bugs → more rushed fixes → more bugs → ...
(Technical debt spiral)
```

### Negative (Balancing) Feedback
More of A causes less of B, stabilizing the system.

```
CPU usage high → autoscaler adds servers → CPU usage drops → autoscaler removes servers
(Kubernetes HPA)

Response time slow → users leave → less load → response time improves
(Self-regulating, but you lost users)
```

**Mental model:** When you see exponential growth or exponential decay, look for feedback loops. Understanding the loop lets you amplify good ones and break bad ones.

---

## Bottlenecks

Every system has ONE bottleneck at any given time. Fix it, and a new one appears.

```
Request ──▶ [Load Balancer] ──▶ [App Server] ──▶ [Database]
                                                     ↑
                                              BOTTLENECK
                                          (slow queries, 200ms)
```

Fix the database (add index, optimize query):

```
Request ──▶ [Load Balancer] ──▶ [App Server] ──▶ [Database]
                                     ↑
                              NEW BOTTLENECK
                          (CPU-bound processing, 150ms)
```

**Theory of Constraints:** Optimizing anything that is NOT the bottleneck is waste. Find the bottleneck first. Always.

**How to find bottlenecks:**
- Measure everything (observability — Phase 3)
- The bottleneck is the component with the highest utilization or longest wait time
- `top`, `htop`, database `EXPLAIN ANALYZE`, application profiling

---

## Queuing Theory (Simplified)

When load approaches capacity, latency explodes. Not linearly — *exponentially*.

```
Utilization:  10%  30%  50%  70%  80%  90%  95%  99%
Wait time:    0.1  0.4  1.0  2.3  4.0  9.0  19.0 99.0
              ────────────────────────────────────────▶
                     "Seems fine"        "WHY IS IT SLOW?!"
```

**This is why:**
- Your app is fine at 50% CPU but falls over at 85%
- Your database handles 1000 qps but dies at 1500 qps
- Traffic spikes cause cascading failures

**Mental model:** Always leave headroom. Design for 2-3x your expected peak. The hockey stick curve of latency is real.

---

## Emergence

Simple rules create complex behavior. No single component "controls" the system.

- **Ant colonies:** Each ant follows simple rules. No ant plans logistics. Yet the colony builds complex structures.
- **Internet:** No one "runs" the internet. TCP/IP + BGP + DNS = emergent global network.
- **Microservices:** No single service knows the full picture. The system's behavior emerges from their interactions.

**Implication:** You can't always predict system behavior by reading individual components. You must test the system as a whole. This is why integration tests matter.

---

## Coupling and Cohesion

### Tight Coupling (Bad)
Changes in A require changes in B, C, and D.

```
Service A ──calls──▶ Service B ──calls──▶ Service C
    │                    │
    └──shares DB────────┘
```

One change ripples everywhere. Deployment is a nightmare.

### Loose Coupling (Good)
Services communicate through contracts (APIs, events). Internal changes don't leak.

```
Service A ──event──▶ [Message Queue] ──event──▶ Service B
                                      ──event──▶ Service C
```

Each service can change independently. Deploy one without touching others.

### High Cohesion (Good)
Related code lives together. A module does ONE thing well.

```
✅ user_service/
    ├── create_user.go
    ├── update_user.go
    ├── user_repository.go
    └── user_test.go

❌ utils/
    ├── string_helpers.go
    ├── user_creation.go
    ├── email_sender.go
    └── random_stuff.go
```

**Mental model:** Loose coupling + high cohesion = systems that scale (in complexity, in team size, in traffic).

---

## Trade-offs Are Everywhere

There is no perfect system. Every design choice is a trade-off:

| Choice | Gain | Cost |
|--------|------|------|
| Add caching | Speed | Stale data, complexity |
| Add microservices | Team independence | Network overhead, debugging difficulty |
| Add redundancy | Availability | Cost, consistency challenges |
| Add abstraction | Flexibility | Indirection, learning curve |
| Optimize performance | Speed | Code complexity, maintainability |

**The senior engineer's question is never "Is this good?" It's "What are we trading, and is it worth it?"**

---

## Resilience Patterns

Real systems fail. Design for failure:

### Circuit Breaker
Stop calling a failing service. Let it recover.

```
Closed (normal) ──▶ failures exceed threshold ──▶ Open (reject calls)
                                                        │
                                                   wait timeout
                                                        │
                                                  Half-Open (try one)
                                                   │            │
                                                success       failure
                                                   │            │
                                                Closed         Open
```

### Retry with Backoff
Don't hammer a failing service. Wait longer between retries.

```
Attempt 1: immediately
Attempt 2: wait 1s
Attempt 3: wait 2s
Attempt 4: wait 4s
Attempt 5: give up
```

### Timeout Everything
Never wait forever. Set timeouts on every external call.

```go
ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
defer cancel()
result, err := apiCall(ctx)
```

### Graceful Degradation
When a component fails, serve reduced functionality instead of nothing.

```
Recommendation engine down?
  → Show popular items instead of personalized ones
  → Don't show a 500 error
```

---

## Exercises

1. Draw a system diagram of a web request: user → browser → DNS → CDN → load balancer → app server → database → back. Label each component and what can go wrong.

2. Identify a feedback loop in your team's workflow. Is it reinforcing or balancing? How could you amplify/dampen it?

3. Find the bottleneck in a system you work with. What evidence tells you it's the bottleneck? What would fixing it do?

4. For a technical decision you recently made, write down three second-order effects (both positive and negative).

5. Design a simple system with a circuit breaker: a service that calls an unreliable API and degrades gracefully when it's down.

---

## Recommended Reading

- *Thinking in Systems* — Donella Meadows (the classic, applies to everything)
- *The Goal* — Eliyahu Goldratt (Theory of Constraints, told as a novel)
- *Release It!* — Michael Nygard (production resilience patterns)
- *How Complex Systems Fail* — Richard Cook (short paper, essential)

---

*← [Data & Algorithms](02-data-and-algorithms.md) · [Networking Fundamentals →](04-networking-fundamentals.md)*
