# Editor Setup

> Your editor is where you spend 8+ hours a day. Make it work for you.

---

## Cursor (Recommended)

Cursor is VS Code with built-in AI. It's our primary editor.

### Install
```bash
brew install --cask cursor
```

Or download from [cursor.com](https://cursor.com).

### Why Cursor?
- Built on VS Code — same extensions, same keybindings, same ecosystem
- AI chat and inline editing powered by Claude / GPT
- Tab completion that understands your codebase
- `⌘K` to edit code with natural language
- Agent mode for multi-file changes

### Essential Extensions

Install via `⌘+Shift+X` (Extensions panel):

```
# Languages
Go                          # Rich Go support
rust-analyzer               # Rust language server
ESLint                      # JavaScript/TypeScript linting
Prettier                    # Code formatting
Python                      # Python support

# Productivity
GitLens                     # Git blame, history, annotations inline
Error Lens                  # Show errors inline (not just in panel)
TODO Highlight              # Highlight TODOs and FIXMEs
Docker                      # Dockerfile support, container management
Thunder Client              # API testing (like Postman, inside editor)

# Visual
Material Icon Theme         # Better file icons
One Dark Pro                # Clean dark theme (or your preference)
```

### Key Settings

Open settings: `⌘+,` → click the `{}` icon (top right) for JSON:

```json
{
    "editor.fontSize": 14,
    "editor.tabSize": 4,
    "editor.formatOnSave": true,
    "editor.minimap.enabled": false,
    "editor.wordWrap": "on",
    "editor.bracketPairColorization.enabled": true,
    "editor.guides.bracketPairs": "active",
    "editor.renderWhitespace": "boundary",
    "editor.suggestSelection": "first",
    "editor.inlineSuggest.enabled": true,
    "files.trimTrailingWhitespace": true,
    "files.insertFinalNewline": true,
    "files.autoSave": "onFocusChange",
    "terminal.integrated.fontSize": 13,
    "workbench.startupEditor": "none",
    "explorer.confirmDelete": false,
    "explorer.confirmDragAndDrop": false
}
```

### Essential Keybindings

| Shortcut | Action |
|----------|--------|
| `⌘+P` | Quick open file (fuzzy search) |
| `⌘+Shift+P` | Command palette (search any action) |
| `⌘+Shift+F` | Search across all files |
| `⌘+B` | Toggle sidebar |
| `⌘+J` | Toggle terminal |
| `` ⌘+` `` | Focus terminal |
| `⌘+D` | Select next occurrence |
| `⌘+Shift+L` | Select all occurrences |
| `Option+↑/↓` | Move line up/down |
| `Option+Shift+↑/↓` | Copy line up/down |
| `⌘+/` | Toggle comment |
| `⌘+Shift+K` | Delete line |
| `F12` | Go to definition |
| `⌘+Click` | Go to definition (mouse) |
| `Shift+F12` | Find all references |
| `F2` | Rename symbol |
| `⌘+.` | Quick fix (show code actions) |

### Cursor AI Shortcuts

| Shortcut | Action |
|----------|--------|
| `⌘+K` | Inline edit with AI (describe what you want) |
| `⌘+L` | Open AI chat panel |
| `⌘+I` | Composer (multi-file AI editing) |
| `Tab` | Accept AI suggestion |
| `Esc` | Reject AI suggestion |

---

## VS Code (Alternative)

If you prefer VS Code without AI features:

```bash
brew install --cask visual-studio-code
```

Same extensions and settings apply. Add GitHub Copilot extension for AI assistance.

---

## Terminal in Editor

Use the integrated terminal (`⌘+J`). Benefits:
- Same directory as your project
- Split terminals for running server + watching tests
- Output links are clickable (go to file:line)

---

## Workspace Organization

One workspace per project. Don't open your entire home directory.

```bash
# Open project in Cursor
cursor ~/Developer/your-org/your-project

# Or from inside a directory
cd ~/Developer/your-org/your-project
cursor .
```

### Multi-Root Workspaces

For working across related repos:
1. File → Add Folder to Workspace
2. Save as `.code-workspace` file

---

## Language-Specific Setup

### Go
```json
// settings.json
{
    "go.toolsManagement.autoUpdate": true,
    "go.lintTool": "golangci-lint",
    "go.lintOnSave": "workspace",
    "go.formatTool": "goimports",
    "[go]": {
        "editor.formatOnSave": true,
        "editor.defaultFormatter": "golang.go"
    }
}
```

### Rust
Rust-analyzer handles everything. Just install the `rust-analyzer` extension.

### JavaScript/TypeScript
```json
{
    "[javascript]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[typescript]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    }
}
```

### Python
```json
{
    "python.defaultInterpreterPath": "${workspaceFolder}/.venv/bin/python",
    "[python]": {
        "editor.formatOnSave": true,
        "editor.defaultFormatter": "charliermarsh.ruff"
    }
}
```

---

## The #1 Productivity Tip

Learn keyboard shortcuts. Every time you reach for the mouse, you're losing flow.

Start with these 5:
1. `⌘+P` — open any file
2. `⌘+Shift+P` — run any command
3. `⌘+Shift+F` — search everything
4. `F12` — go to definition
5. `⌘+K` — ask AI to edit code

Master these before learning more. Five shortcuts mastered > fifty shortcuts bookmarked.

---

## Checklist

- [ ] Cursor (or VS Code) installed
- [ ] Essential extensions installed
- [ ] Settings configured (format on save, trim whitespace)
- [ ] Know the 5 essential shortcuts
- [ ] Can use integrated terminal
- [ ] Language-specific tools configured

---

*← [Dev Tools](05-dev-tools.md) · [Phase 2: How Computers Work →](../phase-2-mental-models/01-how-computers-work.md)*
