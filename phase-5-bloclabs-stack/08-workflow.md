# Development Workflow

> How we work day to day. Tools, processes, conventions.

---

## The Core Rule: 1 Issue = 1 PR

Every code change traces back to a GitHub Issue (or Linear ticket). The issue defines *what* and *why*. The PR delivers *how*.

This isn't bureaucracy — it's how we maintain traceability, enable fast reviews, and keep a clean history. When something breaks in production, we can trace the change back to the issue that motivated it in seconds.

### Why 1:1 Mapping?

| Benefit | Why it matters |
|---------|---------------|
| **Traceability** | Every line of code links to a reason it was written |
| **Reviewability** | Small, focused PRs are fast to review and safe to revert |
| **CI gate** | No code lands without build + tests passing |
| **Clean history** | `git log` tells the story of the product |
| **Auto-close** | `Closes #N` in the PR description auto-closes the issue on merge |
| **Accountability** | Clear ownership — who did what, when, and why |

### When to Raise a PR ✅

| Scenario | PR? | Notes |
|----------|-----|-------|
| Feature implementation from an issue | ✅ | Branch off main, 1 PR per issue |
| Bug fix with reproducible root cause | ✅ | Small, focused diff |
| Refactor that changes behavior | ✅ | Needs tests to prove safety |
| Docs update tied to a code change | ✅ | Can bundle with the code PR |
| Security fix / patch | ✅ | Mark as security in description |
| Dependency bump | ✅ | Keep isolated from feature work |
| Config change that touches prod infra | ✅ | Always needs review |

### When NOT to Raise a PR ❌

| Scenario | PR? | Notes |
|----------|-----|-------|
| Exploration / spike / research | ❌ | Stay in local branch, discard when done |
| WIP that doesn't build or pass tests | ❌ | Never open a PR with a broken build |
| Pure local config / dev-only change | ❌ | No review needed |
| Micro-edits to your own draft PR | ❌ | Push more commits to the existing PR |

### What If One Issue Needs Multiple PRs?

Sometimes an issue is too big for one PR (>500 lines changed). Split it:

1. Break the issue into sub-tasks in Linear
2. Each sub-task gets its own branch and PR
3. PRs should be independently mergeable (no "Part 1 of 3" chains that break if merged out of order)
4. Use feature flags if the feature needs all parts to work

---

## The Full Flow

```
1. Issue created (Linear / GitHub) — describes the problem or feature
2. Developer picks it up, moves to "In Progress"
3. Clone repo → create branch: feat/FAB-123-description or fix/FAB-456-description
4. READ EXISTING CODE FIRST (mandatory — understand the area before changing it)
5. Implement → build passes → tests pass → lint clean
6. Push branch → open PR referencing the issue: "Closes FAB-123" or "Closes #N"
7. CI runs automatically (tests, lint, build, security scan)
8. PR reviewed (AI + human) → approved → squash merged
9. Issue auto-closes → auto-deploy to staging
10. Promote to production after staging verification
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
