# Development Workflow

> How we work day to day. Tools, processes, conventions.

---

## The Flow

```
1. Pick a ticket in Linear
2. Create a branch: feat/FAB-123-description
3. Write code + tests
4. Push, open PR
5. AI review + human review
6. Merge to main
7. Auto-deploy to staging
8. Promote to production
```

---

## Linear (Project Management)

### Ticket Lifecycle
```
Backlog → Todo → In Progress → In Review → Done
```

### Creating Good Tickets
```
Title: Clear, action-oriented
  ✅ "Add rate limiting to transaction endpoint"
  ❌ "Fix stuff"

Description:
  - What needs to happen
  - Why (business context)
  - Acceptance criteria
  - Technical notes (if relevant)
```

### Linking to Code
- Branch name includes ticket ID: `feat/FAB-123-add-rate-limit`
- PR description references ticket: `Closes FAB-123`
- Linear auto-updates ticket status when PR merges

---

## GitHub (Code)

### Branch Naming
```
feat/FAB-123-add-solana-indexer     # New feature
fix/FAB-456-nil-pointer-balance     # Bug fix
chore/update-go-dependencies        # Maintenance
docs/api-endpoint-documentation     # Documentation
refactor/extract-validation-logic   # Refactoring
```

### PR Process
1. **Author:** Open PR with description, link to ticket
2. **CI:** Automated tests, lint, build
3. **AI Review:** Cursor / Claude Code checks for common issues
4. **Human Review:** Teammate reviews design, logic, edge cases
5. **Author:** Address feedback, update PR
6. **Reviewer:** Approve
7. **Author:** Squash merge to main

### PR Review Expectations
- **Reviewer responds within 4 hours** (business hours)
- **Author addresses feedback within 8 hours**
- **No PRs open longer than 3 days** (split if too big)

### Merge Strategy
- **Squash merge** for feature branches (clean history)
- **Merge commit** for release branches (preserve history)
- **Never force-push to main**

---

## CI/CD Pipeline

```yaml
# .github/workflows/ci.yml
name: CI
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-go@v5
        with:
          go-version: '1.22'
      - run: go test -race ./...
      - run: go vet ./...

  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: golangci/golangci-lint-action@v4

  build:
    needs: [test, lint]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: docker build -t app .
```

### What CI Checks
- [ ] Tests pass
- [ ] Lint passes (golangci-lint, eslint, ruff)
- [ ] Build succeeds
- [ ] No race conditions (`go test -race`)
- [ ] Security scan (dependency vulnerabilities)

---

## Environment Strategy

```
local       → docker compose, hot reload
development → shared dev cluster (optional)
staging     → mirrors production, auto-deploy from main
production  → manual promotion from staging
```

**Rule:** If it works in staging, it works in production. If staging doesn't mirror production, fix staging.

---

## Daily Workflow

```
Morning:
1. Check Linear — what's assigned to me?
2. Pull latest main: git pull --rebase
3. Pick up highest priority ticket
4. Create branch, start work

During the day:
- Commit frequently (small, logical commits)
- Push often (backup + trigger CI)
- Review others' PRs when requested (within 4 hours)
- Ask for help after 15 minutes stuck

End of day:
- Push all work (even if WIP — mark PR as draft)
- Update Linear ticket status
- Note any blockers for tomorrow
```

---

## Communication Tools

| Tool | Purpose |
|------|---------|
| **Linear** | Tickets, project tracking |
| **GitHub** | Code, PRs, CI/CD |
| **Slack** | Quick questions, announcements, team chat |
| **Google Meet / Zoom** | Meetings (sparingly) |
| **Loom** | Async video for demos, explanations |

**Default to async.** Write it down instead of scheduling a meeting. Meetings are expensive — everyone's time, simultaneously.

---

*← [Docker & Containers](07-docker-and-containers.md) · [Phase 6: System Design →](../phase-6-mastery/01-system-design.md)*
