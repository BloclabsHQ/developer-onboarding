# Development Tools

> Install the languages and runtimes you'll use daily.

---

## Go

Our primary backend language. Install via Homebrew:

```bash
brew install go
```

**Verify:**
```bash
go version
# go1.22+ expected
```

**Setup:**
```bash
# Add to ~/.zshrc
export GOPATH="$HOME/go"
export PATH="$GOPATH/bin:$PATH"
```

**Test it works:**
```bash
mkdir -p ~/Developer/sandbox/hello-go && cd $_
go mod init hello
cat > main.go << 'EOF'
package main

import "fmt"

func main() {
    fmt.Println("Hello from Go")
}
EOF
go run main.go
```

📖 [Go Installation](https://go.dev/doc/install) · [Effective Go](https://go.dev/doc/effective_go)

---

## Rust

For performance-critical systems. Install via rustup (not Homebrew):

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Choose default installation. Restart your terminal.

**Verify:**
```bash
rustc --version
cargo --version
```

**Test:**
```bash
cd ~/Developer/sandbox
cargo new hello-rust
cd hello-rust
cargo run
```

📖 [Rust Book](https://doc.rust-lang.org/book/) · [Rustlings](https://github.com/rust-lang/rustlings)

---

## Node.js

For frontend, tooling, and JavaScript/TypeScript:

```bash
# Install Node Version Manager (manage multiple versions)
brew install nvm

# Add to ~/.zshrc
export NVM_DIR="$HOME/.nvm"
[ -s "$(brew --prefix nvm)/nvm.sh" ] && . "$(brew --prefix nvm)/nvm.sh"

# Install latest LTS
nvm install --lts
nvm use --lts
```

**Verify:**
```bash
node --version
npm --version
```

**Alternatives:** Bun (`brew install oven-sh/bun/bun`) — faster, TypeScript-native runtime.

📖 [Node.js Docs](https://nodejs.org/en/docs) · [Bun](https://bun.sh)

---

## Python

For scripting, data processing, ML:

```bash
# Install pyenv (manage multiple versions)
brew install pyenv

# Add to ~/.zshrc
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"

# Install latest stable
pyenv install 3.12
pyenv global 3.12
```

**Verify:**
```bash
python --version
pip --version
```

**Virtual environments** (always use them):
```bash
python -m venv .venv
source .venv/bin/activate
pip install requests
deactivate
```

📖 [Python Docs](https://docs.python.org/3/) · [Real Python](https://realpython.com)

---

## Docker

For containerization and consistent environments:

```bash
brew install --cask docker
```

Open Docker Desktop once to complete setup.

**Verify:**
```bash
docker --version
docker run hello-world
```

**Essential commands:**
```bash
docker ps                    # Running containers
docker images                # Downloaded images
docker compose up -d         # Start services defined in docker-compose.yml
docker compose down          # Stop services
docker logs <container>      # View logs
docker exec -it <container> sh  # Shell into container
```

📖 [Docker Docs](https://docs.docker.com/get-started/)

---

## PostgreSQL

Our primary database:

```bash
brew install postgresql@16
brew services start postgresql@16

# Add to ~/.zshrc
export PATH="/opt/homebrew/opt/postgresql@16/bin:$PATH"
```

**Verify:**
```bash
psql --version
createdb testdb
psql testdb -c "SELECT 'Hello from PostgreSQL';"
dropdb testdb
```

📖 [PostgreSQL Tutorial](https://www.postgresqltutorial.com/) · [Official Docs](https://www.postgresql.org/docs/)

---

## Additional Tools

```bash
# JSON/API tools
brew install jq              # JSON processor
brew install httpie           # Better curl for APIs (http GET ...)

# AWS
brew install awscli           # AWS CLI
# Configure: aws configure

# Cloudflare
brew install cloudflare/cloudflare/cloudflared  # Cloudflare Tunnel

# Kubernetes (when needed)
brew install kubectl
brew install helm
```

---

## Verify Everything

Run this to confirm your setup:

```bash
echo "=== Development Environment ==="
echo "Go:       $(go version 2>/dev/null || echo 'NOT INSTALLED')"
echo "Rust:     $(rustc --version 2>/dev/null || echo 'NOT INSTALLED')"
echo "Node:     $(node --version 2>/dev/null || echo 'NOT INSTALLED')"
echo "Python:   $(python --version 2>/dev/null || echo 'NOT INSTALLED')"
echo "Docker:   $(docker --version 2>/dev/null || echo 'NOT INSTALLED')"
echo "Postgres: $(psql --version 2>/dev/null || echo 'NOT INSTALLED')"
echo "Git:      $(git --version 2>/dev/null || echo 'NOT INSTALLED')"
echo "gh:       $(gh --version 2>/dev/null | head -1 || echo 'NOT INSTALLED')"
```

---

## Checklist

- [ ] Go installed and `$GOPATH` configured
- [ ] Rust installed via rustup
- [ ] Node.js installed via nvm
- [ ] Python installed via pyenv with virtual environments understood
- [ ] Docker Desktop installed and running
- [ ] PostgreSQL installed and running
- [ ] All tools verified with test commands

---

*← [Git Fundamentals](04-git-fundamentals.md) · [Editor Setup →](06-editor-setup.md)*
