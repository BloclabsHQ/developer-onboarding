# Developer Communication

> The gap between mid-level and senior is often communication, not code.

---

## Writing Pull Requests

A PR is a conversation, not a code dump.

### Title
```
❌ "fix stuff"
❌ "update user service"

✅ "FAB-123: Add rate limiting to transaction endpoint"
✅ "Fix: Handle nil wallet in balance calculation"
```

### Description Template
```markdown
## What
Brief description of the change.

## Why
What problem does this solve? Link to ticket.

## How
Key technical decisions. Why this approach?

## Testing
How was this tested? What should reviewers check?

## Screenshots (if UI change)
Before / After
```

### PR Size
- **Small PRs get reviewed fast.** Large PRs get rubber-stamped (or ignored).
- Target: **< 400 lines changed**
- If bigger: split into logical commits or stacked PRs

---

## Commit Messages

```
feat: add Solana block indexer

Implements real-time block indexing for Solana mainnet.
Uses the getBlock RPC method with confirmed commitment level.

Closes FAB-123
```

**Format:**
```
<type>: <what changed> (< 72 chars)

<why it changed — paragraph form, wrap at 72 chars>

<ticket reference>
```

---

## Asking for Help

The difference between a 5-minute answer and a 30-minute back-and-forth:

```
❌ "It doesn't work. Can someone help?"

✅ "I'm trying to add a new endpoint to the user service (FAB-123).
    
    Expected: POST /users returns 201 with the created user
    Actual: Returns 500 with 'pq: duplicate key value violates unique constraint'
    
    I've checked:
    - The email field is unique in the schema
    - I'm generating a new UUID for each request
    - The error happens even with different email addresses
    
    Relevant code: [link to file:line]
    Error log: [paste]"
```

Include: **what you're doing, what you expected, what actually happened, what you tried.**

---

## Technical Documentation

### When to Write Docs
- Architecture decisions (ADRs — Architecture Decision Records)
- Setup guides (how to run the project locally)
- API documentation (OpenAPI/Swagger)
- Runbooks (how to handle incidents)

### ADR Format
```markdown
# ADR-001: Use PostgreSQL as Primary Database

## Status: Accepted

## Context
We need a relational database for transactional data with ACID guarantees.

## Decision
Use PostgreSQL 16 with connection pooling via pgbouncer.

## Consequences
- ✅ Strong consistency, mature ecosystem
- ✅ Team has PostgreSQL experience
- ⚠️ Vertical scaling limits (acceptable at our stage)
- ⚠️ Need to manage migrations carefully
```

---

## Incident Reports / Post-Mortems

When things break in production:

```markdown
# Incident: API Latency Spike — 2026-02-18

## Timeline
- 14:00 — Deploy v2.3.1 to production
- 14:05 — Alert: p99 latency > 5s
- 14:10 — Identified: missing database index on new query
- 14:15 — Fix: Added index, deployed hotfix
- 14:20 — Latency returned to normal

## Root Cause
New endpoint introduced a query filtering by `chain_id + status` without a composite index. Full table scan on 2M rows.

## Impact
5-second response times for 20 minutes. ~500 affected requests.

## Action Items
- [ ] Add CI check for queries without matching indexes
- [ ] Review all new queries before merge
- [ ] Add database query performance to PR checklist
```

**Blameless.** Post-mortems are about the system, not the person. "The deploy introduced..." not "Bob deployed..."

---

## Async Communication

Remote teams run on written communication. Write clearly:

- **Lead with the ask.** Don't bury the question in paragraph 3.
- **Provide context.** Don't assume the reader remembers the previous thread.
- **Be specific.** "Can you review this?" → "Can you review the auth changes in PR #42 by tomorrow?"
- **Use threads.** Don't reply to a channel-wide message in the main channel.
- **Respect time zones.** Don't expect instant responses. Write messages that can be answered async.

---

## Checklist

- [ ] PRs have descriptive titles and descriptions
- [ ] Commits follow conventional format
- [ ] Help requests include context, expected vs actual, what was tried
- [ ] Architecture decisions are documented (ADRs)
- [ ] Post-mortems are blameless and include action items
- [ ] Written communication leads with the ask

---

*← [Reading Code](07-reading-code.md) · [Phase 4: AI Tools →](../phase-4-ai-augmented-dev/01-ai-tools-overview.md)*
