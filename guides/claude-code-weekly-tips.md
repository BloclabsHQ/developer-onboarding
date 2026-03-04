# Claude Code Weekly Tips — 12-Week Mastery Program

One tip per week. By week 12 you'll be running fully automated development workflows.

---

## Week 1: Headless Basics (`claude -p`)

The most powerful Claude Code feature most devs never use. Run it from scripts, CI, or other agents.

```bash
# Fix a bug without opening an interactive session
claude -p "Fix the type error in pkg/auth/token.go" \
  --allowedTools "Read,Edit,Bash"

# Check exit code for scripting
claude -p "Run tests and report failures" --allowedTools "Bash" && echo "Clean"
```

**Why it matters:** Unlocks automation — agents can call Claude Code programmatically.
📖 https://code.claude.com/docs/en/cli-reference

---

## Week 2: Your First Skill

Skills are reusable prompt templates you invoke with a slash command. Create one for your most common task.

```bash
mkdir -p ~/.claude/skills/go-reviewer
cat > ~/.claude/skills/go-reviewer/SKILL.md << 'EOF'
---
name: go-reviewer
description: Review Go code for context propagation, error handling, and test coverage
user-invocable: true
---
Review for: context.Context in all I/O functions, fmt.Errorf wrapping, test coverage on new code.
EOF
```

Invoke: `/go-reviewer` in any Claude Code session.
📖 https://code.claude.com/docs/en/skills

---

## Week 3: Hooks for Auto-Formatting

Stop manually running `goimports`. Use a PostToolUse hook to auto-format every Go file Claude edits.

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": {"tool": "Edit", "path": ".*\\.go$"},
        "command": "goimports -w {{path}} && gofmt -w {{path}}"
      }
    ]
  }
}
```

Add to `~/.claude/settings.json`. Now every file Claude touches is auto-formatted.
📖 https://code.claude.com/docs/en/hooks

---

## Week 4: Custom Subagents

Create a specialist agent with restricted tools. Great for read-only audits that can't accidentally edit files.

```markdown
---
name: secret-scanner
description: Scans for hardcoded secrets — read only, never edits
model: claude-haiku-4-5
denied-tools: Edit, Write
---
Scan all files for hardcoded secrets: API keys (sk-, ghp_, AKIA), passwords, tokens.
Report as JSON: [{file, line, pattern, severity}]. Exit 1 if CRITICAL found.
```

Save to `~/.claude/agents/secret-scanner.md`, then reference it in tasks.
📖 https://code.claude.com/docs/en/sub-agents

---

## Week 5: `/batch` for Bulk Operations

The bundled `/batch` skill processes multiple items in one pass — no loop needed.

```bash
# In interactive session
/batch

# Then describe the batch:
# "Add JSDoc comments to all exported functions in src/api/*.ts"
# Claude will process all files in a single coordinated pass
```

Combine with headless: `claude -p "/batch — add error handling to all service methods" --allowedTools "Read,Edit"`.
📖 https://code.claude.com/docs/en/skills

---

## Week 6: Structured JSON Output

Use `--output-format json` + `--json-schema` to get machine-readable output for pipelines.

```bash
claude -p "List all exported functions missing tests in ./pkg" \
  --allowedTools "Bash,Read" \
  --output-format json \
  --json-schema '{
    "type": "array",
    "items": {
      "type": "object",
      "properties": {
        "function": {"type": "string"},
        "file": {"type": "string"},
        "line": {"type": "integer"}
      }
    }
  }' | jq '.[] | select(.file | contains("auth"))'
```

**Use case:** Feed output directly into CI dashboards or issue creation scripts.
📖 https://code.claude.com/docs/en/cli-reference

---

## Week 7: Agent Teams

Enable experimental multi-agent mode to parallelize large tasks across worker agents.

```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
claude -p "Refactor all service handlers to use the new middleware pattern" \
  --allowedTools "Read,Edit,Bash"
```

The orchestrator decomposes the task; workers handle files in parallel. 4x faster on large codebases.
Configure workers in `settings.json`: `{"agentTeams": {"maxWorkers": 4, "workerModel": "claude-haiku-4-5"}}`.
📖 https://code.claude.com/docs/en/agent-teams

---

## Week 8: Learning Mode

When you want Claude to explain *why*, not just *what* — activate Learning output style.

```bash
# In session
/output-style Learning

# Or set as default in settings.json
{
  "defaultOutputStyle": "Learning"
}
```

Learning mode adds: step-by-step reasoning, alternative approaches considered, and trade-offs explained.
Great for code review sessions where you want to grow, not just get the fix.
📖 https://code.claude.com/docs/en/output-styles

---

## Week 9: `/simplify` for Cleaner Responses

Got a wall-of-text response? `/simplify` compresses the last answer without losing key info.

```bash
# After a complex explanation
/simplify

# Or ask for the simplified version upfront
claude -p "Explain the EIP-4337 bundler flow" --allowedTools ""
# Then: /simplify
```

**Pro tip:** Create a `compressed` output style for permanent terseness:
`~/.claude/output-styles/compressed.md` → "Code blocks only. One-line summary. No prose."
📖 https://code.claude.com/docs/en/skills

---

## Week 10: Protected Files with PreToolUse Hooks

Block Claude from ever editing sensitive files — `.env`, secrets, prod configs.

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": {"tool": "Edit", "path": ".*(\\.env|\\.pem|secrets\\.).*"},
        "command": "echo 'BLOCKED: Protected file. Edit manually.' && exit 1"
      },
      {
        "matcher": {"tool": "Write", "path": ".*(prod|production).*\\.yaml$"},
        "command": "echo 'BLOCKED: Production configs require manual edit.' && exit 1"
      }
    ]
  }
}
```

`exit 1` cancels the tool call entirely. Claude gets the error and tries a different approach.
📖 https://code.claude.com/docs/en/hooks

---

## Week 11: Plugin Safety & `denied-tools`

When spawning agents or using Claude headless, always scope tools to the minimum needed.

```bash
# Audit-only: no writes allowed
claude -p "Audit auth.go for security issues" \
  --allowedTools "Read,Bash"
  # No Edit/Write = read-only guaranteed

# In subagent definition
denied-tools: Edit, Write, Bash  # Read-only agent
```

**Rule:** If a task doesn't need to write files, remove Edit/Write from allowedTools.
Principle of least privilege applies to AI agents too.
📖 https://code.claude.com/docs/en/sub-agents

---

## Week 12: Full Automation Pipeline

Combine everything: headless + JSON output + hooks + subagents = fully automated dev loop.

```bash
#!/bin/bash
# full-audit-and-fix.sh

# 1. Scan for issues (read-only subagent)
ISSUES=$(claude -p "Run security audit on ./pkg" \
  --allowedTools "Read,Bash" \
  --output-format json \
  --json-schema '{"type":"array","items":{"properties":{"file":{"type":"string"},"issue":{"type":"string"},"severity":{"type":"string"}}}}')

# 2. Fix CRITICAL issues
echo "$ISSUES" | jq -r '.[] | select(.severity=="CRITICAL") | .file' | while read f; do
  claude -p "Fix the CRITICAL security issue in $f" \
    --allowedTools "Read,Edit" --continue
done

# 3. Run tests
claude -p "Run all tests and report failures" --allowedTools "Bash"

# 4. Create PR if clean
gh pr create --title "security: auto-fix CRITICAL issues" --body "$(echo $ISSUES | jq .)"
```

This is the 10x loop: audit → fix → test → PR. Fully automated.
📖 https://code.claude.com/docs/en/
