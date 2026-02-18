# Homebrew

> The missing package manager for macOS. Install anything with one command.

---

## What is Homebrew?

Linux has `apt` and `yum`. macOS has Homebrew. It installs, updates, and manages software packages from the command line.

## Install

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Follow the post-install instructions to add Homebrew to your PATH.

**Verify:**
```bash
brew --version
# Homebrew 4.x.x
```

---

## Core Commands

```bash
brew install <package>       # Install a package
brew uninstall <package>     # Remove a package
brew list                    # Show installed packages
brew search <term>           # Search for packages
brew update                  # Update Homebrew itself
brew upgrade                 # Upgrade all installed packages
brew upgrade <package>       # Upgrade specific package
brew info <package>          # Show package info
brew doctor                  # Diagnose common issues
```

## Formulae vs Casks

- **Formulae** = CLI tools and libraries (e.g., `git`, `go`, `jq`)
- **Casks** = GUI applications (e.g., `visual-studio-code`, `docker`, `firefox`)

```bash
brew install jq              # Formula (CLI tool)
brew install --cask firefox  # Cask (GUI app)
```

---

## Essential Developer Packages

```bash
# Core tools
brew install git             # Version control (newer than macOS built-in)
brew install jq              # JSON processor (essential for APIs)
brew install tree            # Directory visualization
brew install htop            # Better process viewer
brew install trash           # Safe delete (moves to Trash)
brew install wget            # Download files
brew install curl            # HTTP client (newer than built-in)
brew install ripgrep         # Fast search (rg — faster than grep)
brew install fd              # Fast find (better than find)
brew install bat             # Better cat (syntax highlighting)
brew install fzf             # Fuzzy finder

# After installing fzf:
$(brew --prefix)/opt/fzf/install
# Now Ctrl+R gives you fuzzy command history search
```

---

## Keeping Things Updated

```bash
# Monthly maintenance
brew update && brew upgrade

# Clean up old versions
brew cleanup

# Check for issues
brew doctor
```

---

## Brewfile — Reproducible Setup

Save your packages to a file, install on any new Mac:

```bash
# Export current packages
brew bundle dump --file=~/Brewfile

# Install from Brewfile
brew bundle --file=~/Brewfile
```

Example Brewfile:
```ruby
# CLI tools
brew "git"
brew "go"
brew "node"
brew "python"
brew "jq"
brew "ripgrep"
brew "fd"
brew "bat"
brew "tree"
brew "htop"
brew "trash"

# GUI apps
cask "cursor"
cask "docker"
cask "iterm2"
cask "firefox"
```

---

## Checklist

- [ ] Homebrew installed
- [ ] `brew doctor` shows no critical issues
- [ ] Essential tools installed (jq, tree, ripgrep, bat, fzf)
- [ ] Understand formulae vs casks

---

*← [Terminal & Shell](02-terminal-and-shell.md) · [Git Fundamentals →](04-git-fundamentals.md)*
