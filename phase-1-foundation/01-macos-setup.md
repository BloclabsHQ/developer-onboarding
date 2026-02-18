# macOS Setup

> Your machine is your workshop. Set it up right from day one.

---

## System Update

Before anything else, update your Mac:

**Apple Menu → System Settings → General → Software Update**

Always run the latest stable macOS. Security patches alone justify it.

---

## Xcode Command Line Tools

The foundation of everything. Git, compilers, development headers.

```bash
xcode-select --install
```

Click "Install" when prompted. This gives you:
- `git` — version control
- `clang` — C/C++ compiler
- `make` — build tool
- Development headers for macOS

You do NOT need the full Xcode app unless you're developing iOS/macOS native apps.

**Verify:**
```bash
git --version
# git version 2.x.x

clang --version
# Apple clang version 16.x.x
```

---

## Essential System Settings

### Keyboard
- **System Settings → Keyboard → Key repeat rate** → Fast
- **System Settings → Keyboard → Delay until repeat** → Short
- **System Settings → Keyboard → Text Input → Input Sources → Edit → Disable "Correct spelling automatically"**
- **System Settings → Keyboard → Text Input → Input Sources → Edit → Disable "Capitalize words automatically"**

As a developer, auto-correct will fight you every day. Turn it off.

### Trackpad
- **System Settings → Trackpad → Tap to click** → On
- **System Settings → Trackpad → Tracking speed** → 7-8 out of 10

### Finder
- **Finder → Settings → Advanced → Show all filename extensions** → On
- **Finder → Settings → Sidebar** → Enable Home directory
- **Finder → View → Show Path Bar** (or `⌘ + Option + P`)
- **Finder → View → Show Status Bar** (or `⌘ + /`)

### Dock
- **System Settings → Desktop & Dock → Minimize windows using** → Scale effect
- **System Settings → Desktop & Dock → Automatically hide and show the Dock** → On (saves screen real estate)
- **System Settings → Desktop & Dock → Show suggested and recent apps** → Off

### Security
- **System Settings → Privacy & Security → FileVault** → Turn On (disk encryption)
- **System Settings → Privacy & Security → Firewall** → Turn On

---

## Directory Structure

Organize your development workspace from day one:

```bash
mkdir -p ~/Developer/{bloclabs,personal,sandbox}
```

```
~/Developer/
├── bloclabs/          ← Company repos
├── personal/          ← Your side projects
└── sandbox/           ← Experiments, learning, throwaway code
```

**Why this matters:** When you have 50 repos cloned, you need to find things fast. Consistent structure = cognitive savings every day.

---

## Screenshots

Set screenshots to save to a dedicated folder:

```bash
mkdir -p ~/Desktop/Screenshots
defaults write com.apple.screencapture location ~/Desktop/Screenshots
killall SystemUIServer
```

Keyboard shortcuts:
- `⌘ + Shift + 3` — Full screen
- `⌘ + Shift + 4` — Selection
- `⌘ + Shift + 4 + Space` — Window
- `⌘ + Shift + 5` — Screen recording

---

## Hidden Files

Show hidden files in Finder:

```bash
defaults write com.apple.finder AppleShowAllFiles YES
killall Finder
```

Or press `⌘ + Shift + .` in any Finder window to toggle.

---

## Checklist

- [ ] macOS updated to latest
- [ ] Xcode CLI tools installed
- [ ] Auto-correct disabled
- [ ] FileVault enabled
- [ ] Firewall enabled
- [ ] ~/Developer directory created
- [ ] Screenshot location configured
- [ ] Hidden files visible in Finder

---

*Next: [Terminal & Shell →](02-terminal-and-shell.md)*
