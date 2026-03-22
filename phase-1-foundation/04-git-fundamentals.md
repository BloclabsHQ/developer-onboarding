# Git Fundamentals

> Git is not a feature of GitHub. It's the most important tool in your workflow.

---

## The Mental Model

Forget everything you think you know about "saving files." Git tracks **snapshots of your entire project** over time. Every commit is a complete picture of your code at that moment.

```
commit 1 ──▶ commit 2 ──▶ commit 3 ──▶ commit 4 (HEAD)
   │             │             │             │
   └─ snapshot   └─ snapshot   └─ snapshot   └─ snapshot
```

You can go back to any snapshot. You can branch off and experiment. You can merge experiments back in. Nothing is ever truly lost.

---

## First-Time Setup

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@company.com"
git config --global init.defaultBranch main
git config --global pull.rebase true     # Clean history
git config --global push.autoSetupRemote true  # Auto-track branches
```

---

## The Three Areas

This is the most important concept in Git:

```
Working Directory     Staging Area          Repository
(your files)          (ready to commit)     (committed history)
     │                     │                     │
     │── git add ─────────▶│                     │
     │                     │── git commit ──────▶│
     │                     │                     │
     │◀── git checkout ────│◀── git reset ───────│
```

1. **Working Directory** — your actual files on disk
2. **Staging Area** (index) — files marked for the next commit
3. **Repository** (.git) — the full history of all commits

```bash
# See what's changed
git status

# Stage specific files
git add file.go
git add src/

# Stage everything
git add -A

# Commit with a message
git commit -m "Add user authentication endpoint"

# See history
git log --oneline --graph
```

---

## Branching

Branches are cheap. Use them for everything.

```bash
# Create and switch to a new branch
git checkout -b feature/user-auth

# Switch between branches
git checkout main
git checkout feature/user-auth

# List branches
git branch          # Local
git branch -a       # All (including remote)

# Delete a branch (after merging)
git branch -d feature/user-auth
```

### Branch Naming Convention (BlocLabs)

```
feature/FAB-123-add-solana-indexer     # New feature linked to Linear ticket
fix/FAB-456-null-pointer-on-empty      # Bug fix
chore/update-dependencies              # Maintenance
docs/api-documentation                 # Documentation
```

Format: `type/TICKET-ID-short-description`

---

## The Pull Request Workflow

This is how code gets into production:

```
1. Create branch from main
   git checkout -b feature/FAB-123-new-endpoint

2. Write code, commit frequently
   git add -A && git commit -m "Add endpoint handler"
   git add -A && git commit -m "Add input validation"
   git add -A && git commit -m "Add tests"

3. Push to remote
   git push

4. Open Pull Request on GitHub
   gh pr create --title "FAB-123: Add new endpoint" --body "..."

5. Code review → Address feedback → Approve

6. Merge to main (squash or merge commit)

7. Delete branch
```

---

## Commit Messages

Your commit message is a gift to your future self and your teammates.

### Format

```
<type>: <what changed>

<why it changed — optional but valuable for non-obvious changes>
```

### Types
- `feat:` — new feature
- `fix:` — bug fix
- `refactor:` — code change that neither fixes nor adds
- `test:` — adding or fixing tests
- `docs:` — documentation only
- `chore:` — maintenance, dependencies, config
- `perf:` — performance improvement

### Good vs Bad

```
❌ "fixed stuff"
❌ "updates"
❌ "WIP"
❌ "asdfasdf"

✅ "feat: add Solana block indexer"
✅ "fix: handle nil pointer when wallet is empty"
✅ "refactor: extract validation logic into middleware"
✅ "test: add integration tests for auth flow"
```

---

## Common Operations

### Undo Last Commit (keep changes)
```bash
git reset --soft HEAD~1
```

### Discard All Local Changes
```bash
git checkout -- .
# or
git restore .
```

### Stash (save work temporarily)
```bash
git stash                  # Save current changes
git stash pop              # Restore saved changes
git stash list             # Show all stashes
```

### See What Changed
```bash
git diff                   # Unstaged changes
git diff --staged          # Staged changes
git diff main..feature     # Between branches
git log --oneline -10      # Last 10 commits
git blame file.go          # Who changed each line
```

### Rebase (clean up history)
```bash
# Update your branch with latest main
git checkout feature/my-branch
git rebase main

# Interactive rebase (squash, reword, reorder)
git rebase -i HEAD~3      # Last 3 commits
```

---

## GitHub CLI (`gh`)

```bash
brew install gh
gh auth login

# Pull requests
gh pr create --title "Title" --body "Description"
gh pr list
gh pr view 42
gh pr checkout 42          # Checkout someone's PR locally
gh pr merge 42

# Issues
gh issue list
gh issue create --title "Bug: ..." --body "..."

# Repos
gh repo clone your-org/project-name
```

---

## .gitignore

Never commit these:

```gitignore
# OS
.DS_Store
Thumbs.db

# Dependencies
node_modules/
vendor/

# Build output
dist/
build/
*.exe

# Environment & secrets
.env
.env.local
*.pem
*.key

# IDE
.idea/
.vscode/settings.json
*.swp
```

---

## Golden Rules

1. **Commit often.** Small commits are easy to review, easy to revert, easy to understand.
2. **Never commit secrets.** API keys, passwords, tokens — use `.env` files and `.gitignore`.
3. **Pull before you push.** `git pull --rebase` to keep history clean.
4. **Branch for everything.** Never commit directly to `main`.
5. **Write real commit messages.** "fixed stuff" helps nobody, including future you.
6. **When in doubt, stash.** `git stash` is your safety net.

---

## Exercises

1. Create a new repo, make 5 commits, view the log with `git log --oneline --graph`.
2. Create a branch, make changes, push it, open a PR with `gh pr create`.
3. Practice `git stash` — save work, switch branches, restore.
4. Use `git rebase -i HEAD~3` to squash 3 commits into one.
5. Intentionally create a merge conflict, then resolve it.

---

## Checklist

- [ ] Git configured (name, email, default branch)
- [ ] Understand the three areas (working, staging, repository)
- [ ] Can create branches, commit, push, and open PRs
- [ ] Write meaningful commit messages
- [ ] Know how to stash, rebase, and resolve conflicts
- [ ] GitHub CLI installed and authenticated
- [ ] `.gitignore` set up properly

---

*← [Homebrew](03-homebrew.md) · [Dev Tools →](05-dev-tools.md)*
