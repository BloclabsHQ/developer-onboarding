# Operating Systems

> The OS is the layer between your code and the hardware. Understand it, and everything else makes sense.

---

## What the OS Does

The OS manages resources so your programs don't have to:

```
Your App    Your App    Your App
    │           │           │
    ▼           ▼           ▼
┌──────────────────────────────────┐
│       Operating System            │
│                                   │
│  Process Management               │
│  Memory Management                │
│  File System                      │
│  Network Stack                    │
│  Device Drivers                   │
│  Security / Permissions           │
└──────────────────────────────────┘
    │           │           │
    ▼           ▼           ▼
   CPU        Memory       Disk
```

---

## Processes

A process is a running program. It gets its own:
- Virtual memory space (can't see other processes' memory)
- File descriptors (open files, sockets)
- Process ID (PID)

```bash
# List running processes
ps aux | head -20

# Find a specific process
ps aux | grep node

# Kill a process
kill 12345        # Graceful (SIGTERM)
kill -9 12345     # Force (SIGKILL — last resort)
```

### Process States
```
New → Ready → Running → Waiting → Running → Terminated
              ↑    │         ↑        │
              └────┘         └────────┘
```

- **Running:** CPU is executing this process
- **Ready:** Waiting for CPU time
- **Waiting:** Blocked on I/O (disk, network)
- **Terminated:** Done (or crashed)

---

## Threads

A thread is a lightweight unit of execution within a process. Threads share the same memory space.

```
Process
├── Thread 1 (main)     → Handle HTTP requests
├── Thread 2            → Background job processing
├── Thread 3            → Database connection pool
└── Thread 4            → Logging
```

**Process vs Thread:**

| Feature | Process | Thread |
|---------|---------|--------|
| Memory | Separate (isolated) | Shared (same process) |
| Creation cost | Heavy (~ms) | Light (~μs) |
| Communication | IPC (pipes, sockets) | Shared memory |
| Crash impact | Other processes unaffected | Can crash entire process |

**Go's approach:** Goroutines — even lighter than threads (~2KB initial stack vs ~1MB). The Go runtime multiplexes thousands of goroutines onto a few OS threads.

```go
// Launch 10,000 concurrent tasks — no problem
for i := 0; i < 10000; i++ {
    go processItem(items[i])
}
```

---

## Virtual Memory

Each process thinks it has the entire memory to itself. The OS fakes it.

```
Process A's view:          Physical RAM:
┌──────────────┐          ┌──────────────┐
│ 0x000: code  │──────────│ Page for A    │
│ 0x100: stack │───┐      │ Page for B    │
│ 0x200: heap  │─┐ │      │ Page for A    │
└──────────────┘ │ │      │ Page for C    │
                 │ └──────│ Page for A    │
                 └────────│ Page for A    │
Process B's view:          │ ...           │
┌──────────────┐          └──────────────┘
│ 0x000: code  │──────────     ↑
│ 0x100: stack │──────────     │
└──────────────┘          (OS maps virtual
                           to physical)
```

**Why it matters:**
- Processes can't read each other's memory (security)
- Each process sees a clean, contiguous address space
- The OS can move pages to disk (swap) when RAM is full

**Swap/paging:** When RAM is full, the OS moves inactive pages to disk. This is SLOW (SSD is 1000x slower than RAM). If you see heavy swap usage, you need more RAM.

```bash
# Check memory and swap usage
vm_stat           # macOS
free -h           # Linux
sysctl vm.swapusage  # macOS
```

---

## File System

Everything in Unix is a file (or pretends to be).

```
/
├── bin/          # Essential binaries (ls, cp, grep)
├── etc/          # Configuration files
├── home/         # User home directories (Linux)
├── Users/        # User home directories (macOS)
├── tmp/          # Temporary files (cleared on reboot)
├── var/          # Variable data (logs, databases)
├── dev/          # Device files
└── proc/         # Process information (Linux)
```

### File Descriptors

Every open file gets a number (file descriptor):

| FD | Name | Purpose |
|----|------|---------|
| 0 | stdin | Standard input |
| 1 | stdout | Standard output |
| 2 | stderr | Standard error |
| 3+ | Other | Open files, sockets, pipes |

```bash
# Redirect stdout to file
command > output.txt

# Redirect stderr to file
command 2> errors.txt

# Redirect both
command > output.txt 2>&1
```

### Permissions

```bash
-rwxr-xr--  1 mrmonkey  staff  4096 Feb 18 12:00 script.sh
│├──┤├──┤├──┤
│ │   │   │
│ │   │   └── Others: read only
│ │   └────── Group: read + execute
│ └────────── Owner: read + write + execute
└──────────── File type (- = file, d = directory)
```

```bash
chmod 755 script.sh    # rwxr-xr-x (owner full, others read+execute)
chmod 644 config.yaml  # rw-r--r-- (owner read+write, others read)
chmod +x script.sh     # Add execute permission
```

---

## Environment Variables

Key-value pairs that configure your process:

```bash
# View all
env

# Key variables
echo $PATH       # Where the OS looks for executables
echo $HOME       # Your home directory
echo $USER       # Current username
echo $SHELL      # Current shell

# Set for current session
export DATABASE_URL="postgres://localhost:5432/mydb"

# Set for one command only
DATABASE_URL="postgres://..." go run main.go
```

**Why they matter:** Configuration should come from the environment, not hardcoded in code. This is how you change behavior between development, staging, and production without changing code.

---

## Signals

The OS communicates with processes via signals:

| Signal | Number | Meaning | Default Action |
|--------|--------|---------|---------------|
| SIGTERM | 15 | Please shut down gracefully | Terminate |
| SIGKILL | 9 | Die immediately (can't be caught) | Terminate |
| SIGINT | 2 | Interrupt (Ctrl+C) | Terminate |
| SIGHUP | 1 | Terminal closed | Terminate |
| SIGUSR1 | 10 | User-defined | Nothing |

```go
// Handle graceful shutdown in Go
sig := make(chan os.Signal, 1)
signal.Notify(sig, syscall.SIGTERM, syscall.SIGINT)

go func() {
    <-sig
    fmt.Println("Shutting down gracefully...")
    server.Shutdown(context.Background())
}()
```

**Rule:** Always handle SIGTERM gracefully. Finish in-flight requests, close database connections, flush logs. Container orchestrators (ECS, Docker) send SIGTERM before SIGKILL.

---

## Resource Limits

Every process has limits:

```bash
# View limits
ulimit -a

# Key limits
ulimit -n    # Max open files (default ~256, production needs 65535+)
ulimit -u    # Max processes
```

**Common production issue:** "too many open files" — each socket, file, and database connection uses a file descriptor. In production, raise the limit.

---

## The /proc Filesystem (Linux)

Everything about running processes:

```bash
# Process info
cat /proc/12345/status     # Process status
cat /proc/12345/cmdline    # Command that started it
ls -la /proc/12345/fd/     # Open file descriptors

# System info
cat /proc/cpuinfo          # CPU details
cat /proc/meminfo          # Memory details
cat /proc/loadavg          # System load
```

macOS equivalent: `sysctl`, `launchctl`, Activity Monitor.

---

## Exercises

1. Run `ps aux | wc -l` — how many processes are running on your machine? Find the top 5 by memory with `ps aux --sort=-%mem | head -5`.

2. Write a program that forks a child process. Have parent and child communicate via pipe.

3. Create a file with restrictive permissions (600). Try to read it as a different user. Why does it fail?

4. Write a Go/Python program that handles SIGTERM gracefully — prints "shutting down," cleans up, exits.

5. Check your system's open file limit (`ulimit -n`). Write a program that opens files until it hits the limit.

---

## Recommended Reading

- *Operating Systems: Three Easy Pieces* — Remzi Arpaci-Dusseau (free online: [ostep.org](https://pages.cs.wisc.edu/~remzi/OSTEP/))
- *The Linux Command Line* — William Shotts (free online)
- *Unix and Linux System Administration Handbook* — Nemeth et al. (comprehensive reference)

---

*← [Problem Solving](06-problem-solving.md) · [Phase 3: Code as Craft →](../phase-3-engineering/01-code-as-craft.md)*
