# Terminal & Shell

> The terminal is not a scary relic. It's the most powerful tool on your machine.

---

## Why Terminal Matters

GUIs show you what someone decided you should see. The terminal shows you everything. Every server you'll ever work with, every CI/CD pipeline, every Docker container — they all run through a terminal. Master this, and you're never helpless.

---

## Terminal App

macOS comes with Terminal.app. It works fine. Later you might want:
- **iTerm2** — more features, split panes, better search
- **Warp** — modern, AI-integrated
- **Alacritty** — GPU-accelerated, minimal

For now, Terminal.app is enough. Don't optimize your tools before you know what you need.

---

## Zsh Basics

macOS default shell is `zsh`. Verify:

```bash
echo $SHELL
# /bin/zsh
```

### Navigation

```bash
pwd                    # Where am I? (Print Working Directory)
ls                     # What's here?
ls -la                 # What's here? (everything, detailed)
cd ~/Developer         # Go to Developer folder
cd ..                  # Go up one level
cd -                   # Go back to previous directory
cd                     # Go home
```

### Files & Directories

```bash
mkdir project-name     # Create directory
mkdir -p a/b/c         # Create nested directories
touch file.txt         # Create empty file
cp file.txt copy.txt   # Copy
mv old.txt new.txt     # Move/rename
rm file.txt            # Delete (PERMANENT — no trash!)
rm -rf directory/      # Delete directory and everything inside (DANGEROUS)
```

**⚠️ `rm` is permanent.** There is no undo. Use `trash` (install via Homebrew) instead:
```bash
brew install trash
trash file.txt         # Moves to macOS Trash — recoverable
```

### Reading Files

```bash
cat file.txt           # Print entire file
head -20 file.txt      # First 20 lines
tail -20 file.txt      # Last 20 lines
tail -f log.txt        # Follow a file in real-time (great for logs)
less file.txt          # Scrollable viewer (q to quit)
wc -l file.txt         # Count lines
```

### Searching

```bash
grep "error" log.txt           # Find lines containing "error"
grep -r "TODO" ./src/          # Search recursively in directory
grep -rn "func main" ./       # Search with line numbers
find . -name "*.go"            # Find files by name
find . -name "*.log" -mtime -1 # Files modified in last day
```

---

## Piping & Redirection

This is where the terminal becomes a superpower.

### The Pipe (`|`)

Connect the output of one command to the input of another:

```bash
# Find all Go files, count them
find . -name "*.go" | wc -l

# Show running processes, find the one eating memory
ps aux | grep node

# Read a log, find errors, sort by frequency
cat app.log | grep ERROR | sort | uniq -c | sort -rn | head -10

# Show disk usage, sorted by size
du -sh */ | sort -rh | head -10
```

**Mental model:** Each command does ONE thing well. Piping composes them into something powerful. This is the Unix philosophy.

### Redirection

```bash
echo "hello" > file.txt      # Write to file (overwrite)
echo "world" >> file.txt     # Append to file
command 2> errors.log         # Redirect errors only
command > output.log 2>&1     # Redirect everything
command > /dev/null 2>&1      # Silence everything
```

---

## Oh My Zsh

Framework for managing your Zsh configuration. Makes the terminal more productive and more pleasant.

### Install

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

### Essential Plugins

Edit `~/.zshrc`:

```bash
plugins=(
    git               # Git aliases and status in prompt
    z                 # Jump to frequent directories (type 'z proj' instead of 'cd ~/Developer/bloclabs/project')
    docker            # Docker completions
    kubectl           # Kubernetes completions
    aws               # AWS completions
    colored-man-pages # Readable man pages
    extract           # Extract any archive with 'extract file.tar.gz'
)
```

### Useful Git Aliases (from Oh My Zsh git plugin)

```bash
gst     # git status
ga      # git add
gcmsg   # git commit -m
gp      # git push
gl      # git pull
gco     # git checkout
gb      # git branch
gd      # git diff
glog    # git log --oneline --decorate --graph
```

### Theme

Recommended: `robbyrussell` (default, clean) or `agnoster` (shows git branch, virtualenv).

For a more modern prompt, try **Starship**:
```bash
brew install starship
echo 'eval "$(starship init zsh)"' >> ~/.zshrc
```

---

## Essential Terminal Skills

### Process Management

```bash
command &              # Run in background
jobs                   # List background jobs
fg                     # Bring to foreground
Ctrl+C                 # Kill current process
Ctrl+Z                 # Suspend current process
kill PID               # Kill by process ID
kill -9 PID            # Force kill (last resort)
```

### Environment Variables

```bash
echo $PATH             # Show PATH
export MY_VAR="value"  # Set for this session
echo $MY_VAR           # Read it

# Permanent — add to ~/.zshrc:
export GOPATH="$HOME/go"
export PATH="$GOPATH/bin:$PATH"
```

### Aliases

Add to `~/.zshrc`:

```bash
# Navigation
alias dev="cd ~/Developer"
alias bloc="cd ~/Developer/bloclabs"

# Safety
alias rm="trash"       # Never accidentally delete forever

# Shortcuts
alias ll="ls -la"
alias ..="cd .."
alias ...="cd ../.."
```

After editing `~/.zshrc`:
```bash
source ~/.zshrc        # Reload without restarting terminal
```

---

## SSH Basics

You'll need SSH for connecting to servers, GitHub, and remote machines.

```bash
# Generate SSH key (if you don't have one)
ssh-keygen -t ed25519 -C "your.email@company.com"

# Start SSH agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# Copy public key to clipboard
pbcopy < ~/.ssh/id_ed25519.pub
# → Paste this in GitHub → Settings → SSH keys
```

```bash
# Connect to a server
ssh user@hostname

# Copy files to/from server
scp file.txt user@host:/path/
scp user@host:/path/file.txt ./
```

---

## Keyboard Shortcuts (Terminal)

| Shortcut | Action |
|----------|--------|
| `Ctrl + A` | Go to beginning of line |
| `Ctrl + E` | Go to end of line |
| `Ctrl + W` | Delete word before cursor |
| `Ctrl + U` | Delete entire line |
| `Ctrl + R` | Search command history (type to search) |
| `Ctrl + L` | Clear screen |
| `Tab` | Auto-complete commands and paths |
| `Tab Tab` | Show all completions |
| `↑ / ↓` | Navigate command history |
| `!!` | Repeat last command |
| `!$` | Last argument of previous command |

---

## The "I Broke Something" Toolkit

```bash
# What's using a port?
lsof -i :3000

# What's eating CPU?
top
htop                   # Better (brew install htop)

# What's eating disk?
df -h                  # Disk space overview
du -sh */ | sort -rh   # Biggest directories here

# Am I connected to the internet?
ping google.com
curl -I https://api.github.com

# What's my IP?
curl ifconfig.me       # Public IP
ipconfig getifaddr en0 # Local IP (Wi-Fi)

# DNS lookup
nslookup example.com
dig example.com
```

---

## Exercises

1. Navigate to your home directory. Create the `~/Developer/{bloclabs,personal,sandbox}` structure. Verify with `tree ~/Developer` (install tree: `brew install tree`).

2. Create a file called `practice.txt` with 100 lines: `for i in {1..100}; do echo "Line $i: $(date)" >> practice.txt; done`. Use `head`, `tail`, `grep`, and `wc` to explore it.

3. Use piping: `history | grep "git" | wc -l` — how many git commands have you run?

4. Set up Oh My Zsh, enable the `git` and `z` plugins. Navigate to 3 different directories, then try `z` to jump between them.

5. Generate an SSH key and add it to your GitHub account.

---

## Checklist

- [ ] Comfortable navigating with `cd`, `ls`, `pwd`
- [ ] Can create, copy, move, delete files and directories
- [ ] Understand piping (`|`) and redirection (`>`, `>>`)
- [ ] Oh My Zsh installed with git + z plugins
- [ ] Custom aliases in `~/.zshrc`
- [ ] SSH key generated and added to GitHub
- [ ] Can search command history with `Ctrl + R`
- [ ] Know how to kill a stuck process

---

*← [macOS Setup](01-macos-setup.md) · [Homebrew →](03-homebrew.md)*
