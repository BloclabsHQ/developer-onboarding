# BlocLabs Developer Onboarding Program

> From junior developer to AI-augmented senior engineer.

Welcome to BlocLabs. This program is your path from wherever you are today to where we need you to be: a world-class engineer who thinks in systems, writes code that lasts, and leverages AI to move at 10x speed.

This isn't a collection of tutorials. It's a **thinking framework** — language-agnostic principles that apply whether you're writing Go, Rust, Python, Swift, or TypeScript. The tools change. The mental models don't.

---

## 🗺️ The Learning Path

```
                        BLOCLABS DEVELOPER PATHWAY
                        ══════════════════════════

    ┌─────────────────────────────────────────────────────────────┐
    │                                                             │
    │   PHASE 1                        PHASE 2                   │
    │   ┌───────────────┐              ┌──────────────────┐      │
    │   │               │              │                  │      │
    │   │  FOUNDATION   │─────────────▶│  MENTAL MODELS   │      │
    │   │               │              │                  │      │
    │   │  macOS Setup  │              │  How Computers   │      │
    │   │  Terminal/Zsh │              │  Actually Work   │      │
    │   │  Git          │              │  Data & Systems  │      │
    │   │  Dev Tools    │              │  Problem Solving │      │
    │   │  Editor       │              │  Networking      │      │
    │   │               │              │  Databases       │      │
    │   └───────────────┘              │  OS Internals    │      │
    │                                  └────────┬─────────┘      │
    │                                           │                │
    │   PHASE 3                        PHASE 4  │                │
    │   ┌───────────────┐              ┌────────▼─────────┐      │
    │   │               │              │                  │      │
    │   │  ENGINEERING  │◀─────────────│  AI-AUGMENTED    │      │
    │   │  CRAFT        │              │  DEVELOPMENT     │      │
    │   │               │              │                  │      │
    │   │  Code Quality │              │  Cursor + Claude │      │
    │   │  Error Paths  │              │  Codex / Copilot │      │
    │   │  Testing      │              │  Prompt Craft    │      │
    │   │  Architecture │              │  AI Code Review  │      │
    │   │  API Design   │              │  Models & When   │      │
    │   │  Reading Code │              │  Ethics & Limits │      │
    │   │  Communication│              │                  │      │
    │   │  Observability│              │                  │      │
    │   └───────┬───────┘              └──────────────────┘      │
    │           │                                                │
    │   PHASE 5 │                      PHASE 6                   │
    │   ┌───────▼───────┐              ┌──────────────────┐      │
    │   │               │              │                  │      │
    │   │  BLOCLABS     │─────────────▶│  MASTERY         │      │
    │   │  STACK        │              │                  │      │
    │   │               │              │  System Design   │      │
    │   │  Go / Rust    │              │  DDD + Hexagonal │      │
    │   │  JS / Python  │              │  Distributed Sys │      │
    │   │  PostgreSQL   │              │  Performance     │      │
    │   │  AWS / CF     │              │  Security        │      │
    │   │  Docker       │              │                  │      │
    │   │  Workflow     │              │                  │      │
    │   └───────────────┘              └──────────────────┘      │
    │                                                             │
    │   SUPPLEMENTARY TRACKS                                      │
    │   ┌─────────────┐ ┌──────────────┐ ┌─────────────────┐    │
    │   │  Apple/iOS   │ │  Blockchain   │ │  Infrastructure  │    │
    │   │  Swift/SwiftUI│ │  EVM/Solana   │ │  AWS/DevOps      │    │
    │   └─────────────┘ └──────────────┘ └─────────────────┘    │
    │                                                             │
    └─────────────────────────────────────────────────────────────┘

    REFERENCES
    ─────────
    → Engineering Mental Models:  github.com/agent67-atlas/engineering-mental-models
    → Apple Developer Guide:      github.com/agent67-atlas/apple-developer-guide
```

---

## How to Use This Program

### If you're a junior developer
Start at Phase 1. Work through each phase in order. Take your time with Phase 2 — these mental models will define the rest of your career.

### If you're a mid-level developer
Skim Phase 1, deep dive into Phase 2 and 3. You probably know how to write code but may need to level up on *thinking about code*.

### If you're a senior developer joining BlocLabs
Jump to Phase 4 (AI tools) and Phase 5 (our stack). Review Phase 3 for our engineering standards.

### For everyone
Phase 4 (AI-Augmented Development) is **mandatory** regardless of level. This is how we work.

---

## Curriculum

### Phase 1: Foundation
*Get your machine ready and learn the tools of the trade.*

| # | Guide | Duration | Description |
|---|-------|----------|-------------|
| 1 | [macOS Setup](phase-1-foundation/01-macos-setup.md) | 1-2 hours | System config, Xcode CLI tools, essential settings |
| 2 | [Terminal & Shell](phase-1-foundation/02-terminal-and-shell.md) | 2-3 hours | Zsh, Oh My Zsh, navigation, piping, scripting basics |
| 3 | [Homebrew](phase-1-foundation/03-homebrew.md) | 30 min | Package management on macOS |
| 4 | [Git Fundamentals](phase-1-foundation/04-git-fundamentals.md) | 3-4 hours | Mental model for version control, branching, PRs |
| 5 | [Dev Tools](phase-1-foundation/05-dev-tools.md) | 2-3 hours | Node, Go, Python, Rust, Docker installation and verification |
| 6 | [Editor Setup](phase-1-foundation/06-editor-setup.md) | 1-2 hours | Cursor, VS Code, extensions, keybindings, productivity |

### Phase 2: Mental Models
*Understand the machine and the principles. Language-agnostic, career-defining.*

| # | Guide | Duration | Description |
|---|-------|----------|-------------|
| 1 | [How Computers Work](phase-2-mental-models/01-how-computers-work.md) | 3-4 hours | CPU, memory hierarchy, disk, I/O — what's under your code |
| 2 | [Data & Algorithms](phase-2-mental-models/02-data-and-algorithms.md) | 4-5 hours | Structures, complexity, trade-offs — think before you code |
| 3 | [Systems Thinking](phase-2-mental-models/03-systems-thinking.md) | 2-3 hours | Inputs/outputs, feedback loops, emergence, second-order effects |
| 4 | [Networking Fundamentals](phase-2-mental-models/04-networking-fundamentals.md) | 3-4 hours | TCP/IP, HTTP, DNS, TLS, WebSockets — how the internet works |
| 5 | [Databases & State](phase-2-mental-models/05-databases-and-state.md) | 3-4 hours | SQL, ACID, CAP theorem, caching — where data lives |
| 6 | [Problem Solving](phase-2-mental-models/06-problem-solving.md) | 2-3 hours | Debugging as discipline, scientific method for code |
| 7 | [Operating Systems](phase-2-mental-models/07-operating-systems.md) | 3-4 hours | Processes, threads, memory management, filesystems |

### Phase 3: Engineering Craft
*Write code that lasts. The standards that separate professionals from hobbyists.*

| # | Guide | Duration | Description |
|---|-------|----------|-------------|
| 1 | [Code as Craft](phase-3-engineering/01-code-as-craft.md) | 2-3 hours | Readability, naming, correctness > cleverness |
| 2 | [Error Handling](phase-3-engineering/02-error-handling.md) | 2-3 hours | The main code path, not an afterthought |
| 3 | [Testing](phase-3-engineering/03-testing.md) | 3-4 hours | Pyramid, what to test, TDD when it fits |
| 4 | [Architecture](phase-3-engineering/04-architecture.md) | 3-4 hours | Layering, dependency rules, clean boundaries |
| 5 | [API Design](phase-3-engineering/05-api-design.md) | 2-3 hours | REST, contracts, versioning, backward compatibility |
| 6 | [Observability](phase-3-engineering/06-observability.md) | 2-3 hours | Logs, metrics, traces — if you can't see it, you can't fix it |
| 7 | [Reading Code](phase-3-engineering/07-reading-code.md) | 2-3 hours | Navigate codebases, trace data flow, review PRs |
| 8 | [Developer Communication](phase-3-engineering/08-developer-communication.md) | 2-3 hours | PRs, commits, docs, asking for help, incident reports |

### Phase 4: AI-Augmented Development
*The force multiplier. How we work at BlocLabs.*

| # | Guide | Duration | Description |
|---|-------|----------|-------------|
| 1 | [AI Tools Overview](phase-4-ai-augmented-dev/01-ai-tools-overview.md) | 2-3 hours | Cursor, Claude Code, Codex, Copilot, OpenClaw |
| 2 | [Models & When to Use Them](phase-4-ai-augmented-dev/02-models-and-when.md) | 1-2 hours | Opus vs Sonnet vs GPT vs Gemini — trade-offs |
| 3 | [Prompt Engineering](phase-4-ai-augmented-dev/03-prompt-engineering.md) | 2-3 hours | How to talk to AI effectively for code |
| 4 | [Code Review with AI](phase-4-ai-augmented-dev/04-code-review-with-ai.md) | 1-2 hours | Using AI to review, not replace judgment |
| 5 | [AI Ethics & Limits](phase-4-ai-augmented-dev/05-ai-ethics-and-limits.md) | 1-2 hours | What AI gets wrong, verification, IP, licensing |

### Phase 5: BlocLabs Stack
*Our tools, our patterns, our workflow.*

| # | Guide | Duration | Description |
|---|-------|----------|-------------|
| 1 | [Go at BlocLabs](phase-5-bloclabs-stack/01-go-at-bloclabs.md) | 3-4 hours | Our Go patterns and conventions |
| 2 | [Rust at BlocLabs](phase-5-bloclabs-stack/02-rust-at-bloclabs.md) | 2-3 hours | When and why we use Rust |
| 3 | [JavaScript & TypeScript](phase-5-bloclabs-stack/03-javascript-typescript.md) | 2-3 hours | Frontend, Node.js, TypeScript patterns |
| 4 | [Python for Developers](phase-5-bloclabs-stack/04-python-for-devs.md) | 2-3 hours | Scripting, data processing, ML pipelines |
| 5 | [PostgreSQL](phase-5-bloclabs-stack/05-postgresql.md) | 3-4 hours | Schema design, migrations, query performance |
| 6 | [AWS & Cloudflare](phase-5-bloclabs-stack/06-aws-and-cloudflare.md) | 3-4 hours | Services we use, when, why |
| 7 | [Docker & Containers](phase-5-bloclabs-stack/07-docker-and-containers.md) | 2-3 hours | Containerization, compose, registry |
| 8 | [Development Workflow](phase-5-bloclabs-stack/08-workflow.md) | 1-2 hours | Linear + GitHub, branch naming, PR process |

### Phase 6: Mastery
*The path to staff engineer and beyond.*

| # | Guide | Duration | Description |
|---|-------|----------|-------------|
| 1 | [System Design](phase-6-mastery/01-system-design.md) | 4-5 hours | Distributed systems, scaling, trade-offs |
| 2 | [Domain-Driven Design](phase-6-mastery/02-domain-driven-design.md) | 3-4 hours | Bounded contexts, aggregates, event sourcing |
| 3 | [Performance](phase-6-mastery/03-performance.md) | 3-4 hours | Profiling, bottlenecks, optimization |
| 4 | [Security](phase-6-mastery/04-security.md) | 3-4 hours | OWASP, auth, secrets management, threat modeling |

### Supplementary Tracks

| Track | Guide | Description |
|-------|-------|-------------|
| 🍎 Apple/iOS | [Apple Track](supplementary/apple-ios-track.md) | Swift, SwiftUI, mobile development |
| ⛓️ Blockchain | [Blockchain Track](supplementary/blockchain-track.md) | EVM, Solana, smart contracts |
| 🏗️ Infrastructure | [Infrastructure Track](supplementary/infrastructure-track.md) | Deep AWS, Terraform, monitoring |

---

## Total Program Duration

| Level | Recommended Path | Duration |
|-------|-----------------|----------|
| Junior → Mid | Phase 1 → 2 → 3 → 4 → 5 | 8-12 weeks |
| Mid → Senior | Phase 2 → 3 → 4 → 5 → 6 | 4-6 weeks |
| Senior (new hire) | Phase 4 → 5 + Phase 3 review | 1-2 weeks |

---

## Principles

1. **Language-agnostic foundations** — We teach thinking, not syntax. The same principles apply in Go, Rust, Python, and Swift.
2. **Mental models over memorization** — Understanding *why* beats knowing *how*. The *how* changes every year. The *why* lasts a career.
3. **AI-native workflow** — AI is not a crutch. It's a power tool. Like any power tool, you need to understand the craft before you turn it on.
4. **Progressive depth** — Each phase builds on the last. Skip nothing in your first pass.
5. **Practical, not academic** — Every concept includes real-world examples and exercises. Theory without practice is trivia.

---

## 🧰 Templates & Resources

Ready-to-use starting points that implement our engineering standards. Clone these instead of starting from scratch.

| Template | Description |
|----------|-------------|
| Go Service Blueprint | Production-ready Go service — DDD architecture, gRPC + HTTP, OpenTelemetry. Clone your org's service template instead of starting from scratch. |
| Frontend Template | Standard frontend starting point — TypeScript, React, Vite, design system pre-wired. |
| Brand Kit | Brand kit & design guidelines — logos, colors, typography, component patterns. |
| API Standards & Roadmap | API standards, OpenAPI specs, and service roadmap for your platform. |
| Engineering Principles | [agent67-atlas/engineering-principles](https://github.com/agent67-atlas/engineering-principles) — engineering principles (source of truth) |

> These templates implement our [Engineering Principles](https://github.com/agent67-atlas/engineering-principles). When principles are updated, templates are updated to match. Inspired by [Ardan Labs Service](https://github.com/ardanlabs/service/wiki).

---

## External References

| Resource | Link | Purpose |
|----------|------|---------|
| Engineering Mental Models | [github.com/agent67-atlas/engineering-mental-models](https://github.com/agent67-atlas/engineering-mental-models) | Deep dive into engineering thinking |
| Apple Developer Guide | [github.com/agent67-atlas/apple-developer-guide](https://github.com/agent67-atlas/apple-developer-guide) | Swift, SwiftUI, iOS/macOS development |
| The Go Blog | [go.dev/blog](https://go.dev/blog/) | Official Go insights |
| Rust Book | [doc.rust-lang.org/book](https://doc.rust-lang.org/book/) | The Rust Programming Language |
| PostgreSQL Docs | [postgresql.org/docs](https://www.postgresql.org/docs/current/) | Authoritative SQL reference |
| AWS Well-Architected | [aws.amazon.com/architecture/well-architected](https://aws.amazon.com/architecture/well-architected/) | Cloud architecture framework |

---

## Contributing

This is a living document. If you find something unclear, wrong, or missing — open a PR. The best onboarding programs are built by the people who go through them.

---

*Built with ❤️ by BlocLabs — [bloclabs.com](https://bloclabs.com)*

*"The people who are crazy enough to think they can change the world are the ones who do."*
