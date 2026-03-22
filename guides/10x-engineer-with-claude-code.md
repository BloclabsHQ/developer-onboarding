# 10x Engineer with Claude Code — BlocLabs Developer Guide

> Practical guide for BlocLabs engineers on maximizing productivity with Claude Code and our engineering standards.

---

## Git Workflow

### Branch Naming

```
feat/short-description
fix/issue-description
docs/what-you-documented
chore/what-you-cleaned
refactor/what-you-restructured
security/what-you-hardened
```

### Conventional Commits

```
feat: add wallet connect flow
fix: resolve race condition in signing queue
docs: update MPC architecture diagram
chore: bump go dependencies
refactor: extract auth middleware
security: rotate API key storage to vault
test: add unit tests for token parser
```

### PR Rules

1. **Always PR** — never push directly to `main` or `develop`
2. **Wait for CodeRabbit** — all PRs get auto-reviewed by CodeRabbit bot
3. **Apply CRITICAL + MAJOR** fixes before requesting human review
4. **Assign reviewer** — always assign `cristian-bloclabs` as reviewer
5. **Link issues** — use `Closes #N` in PR body to auto-close issues

### Issue Labels

| Label | When to use |
|-------|-------------|
| `bug` | Something broken |
| `feature` | New capability |
| `security` | Security concern or hardening |
| `docs` | Documentation only |
| `chore` | Maintenance, deps, cleanup |
| `good-first-issue` | Suitable for onboarding devs |

---

## Claude Code Features

### 1. Headless Mode (`claude -p`)

Run Claude Code from scripts, CI, or other agents — no interactive session needed.

```bash
# Fix a bug non-interactively
claude -p "Fix the failing test in auth_test.go" \
  --allowedTools "Read,Edit,Bash"

# Structured JSON output (for pipelines)
claude -p "List all TODO comments in ./src" \
  --allowedTools "Bash,Read" \
  --output-format json \
  --json-schema '{"type":"array","items":{"type":"object","properties":{"file":{"type":"string"},"line":{"type":"integer"},"text":{"type":"string"}}}}'

# Stream for large tasks
claude -p "Audit all API endpoints for auth" \
  --output-format stream-json --verbose

# Resume a session
claude -p "Now add error handling" --continue
claude -p "Also add tests" --resume <session_id>

# Inject custom context
claude -p "Review this PR diff" \
  --append-system-prompt "Focus on security and performance. Flag hardcoded secrets."
```

📖 [Headless docs](https://code.claude.com/docs/en/cli-reference)

---

### 2. Skills

Skills are reusable instruction sets. Create at `~/.claude/skills/<name>/SKILL.md`.

```markdown
---
name: bloclabs-reviewer
description: BlocLabs code review — security, Go standards, test coverage
user-invocable: true
agent-invocable: true
---

Review code for:
- Hardcoded secrets (fail if found)
- Missing context.Context propagation
- Test coverage for new functions
- Conventional commit format
Report as structured JSON with severity.
```

**Built-in skills:** `/simplify`, `/batch`, `/debug`

📖 [Skills docs](https://code.claude.com/docs/en/skills)

---

### 3. Hooks

Hooks run shell commands at lifecycle events — great for enforcement and automation.

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": {"tool": "Edit", "path": ".*\\.env$"},
        "command": "echo 'BLOCKED: .env files protected' && exit 1"
      }
    ],
    "PostToolUse": [
      {
        "matcher": {"tool": "Edit", "path": ".*\\.go$"},
        "command": "goimports -w {{path}}"
      }
    ],
    "PostCompaction": [
      {
        "command": "cat ~/.claude/PERSISTENT_CONTEXT.md"
      }
    ]
  }
}
```

📖 [Hooks docs](https://code.claude.com/docs/en/hooks)

---

### 4. Subagents

Specialist AI instances with scoped tools and models.

```markdown
---
name: go-security-auditor
description: Audits Go code for OWASP and BlocLabs security standards
model: claude-haiku-4-5
tools: Read, Bash, Grep
denied-tools: Edit, Write
---

You are a security auditor. Check for:
- Hardcoded credentials
- SQL injection
- Missing input validation
- Unhandled errors that leak internals
Output JSON: [{severity, file, line, issue, recommendation}]
```

Save to `~/.claude/agents/go-security-auditor.md`

📖 [Subagents docs](https://code.claude.com/docs/en/sub-agents)

---

### 5. Agent Teams (Experimental)

Multiple Claude instances working in parallel on a shared task queue.

```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
claude  # Orchestrator auto-spawns workers
```

Best for: large refactors, parallel test generation, multi-service PR reviews.

📖 [Agent teams docs](https://code.claude.com/docs/en/agent-teams)

---

### 6. Output Styles

Control response format. Create `~/.claude/output-styles/<name>.md`.

```markdown
---
name: compressed
description: Code only, no prose
---
Respond with ONLY code blocks and a one-line summary. No explanations.
```

Invoke with `/output-style compressed`. Built-ins: `Default`, `Explanatory`, `Learning`.

📖 [Output styles docs](https://code.claude.com/docs/en/output-styles)

---

## BlocLabs Engineering Practices

### Non-Negotiables

- ✅ **Run tests before every commit** — `go test ./...` or `npm test`
- ✅ **No hardcoded secrets** — use Vault/env vars; hooks will block `.env` edits
- ✅ **Follow engineering principles** — see link below
- ✅ **Context propagation** — every Go function that does I/O takes `context.Context`
- ✅ **Error wrapping** — `fmt.Errorf("...: %w", err)` not `fmt.Errorf("...: %v", err)`
- ✅ **Structured logging** — `slog` or `zap`, never `fmt.Println` in production

### Code Review Checklist (before requesting review)

- [ ] CodeRabbit CRITICAL + MAJOR comments resolved
- [ ] Tests pass locally
- [ ] No secrets in diff
- [ ] PR description explains *why*, not just *what*
- [ ] Linked to issue

---

## Key References

| Resource | URL |
|----------|-----|
| Engineering Principles | https://github.com/agent67-atlas/engineering-principles |
| Ardan Labs Service Wiki | https://github.com/ardanlabs/service/wiki |
| Ardan Labs Design Guidelines | https://github.com/ardanlabs/service/wiki/design-guidelines |
| Ardan Labs DDD | https://github.com/ardanlabs/service/wiki/Domain-Driven,-Data-Oriented-Design |

---

## Claude Code Docs Index

| Topic | URL |
|-------|-----|
| Overview | https://code.claude.com/docs/en/ |
| CLI Reference (Headless) | https://code.claude.com/docs/en/cli-reference |
| Skills | https://code.claude.com/docs/en/skills |
| Hooks | https://code.claude.com/docs/en/hooks |
| Subagents | https://code.claude.com/docs/en/sub-agents |
| Agent Teams | https://code.claude.com/docs/en/agent-teams |
| Output Styles | https://code.claude.com/docs/en/output-styles |
| Settings | https://code.claude.com/docs/en/settings |
