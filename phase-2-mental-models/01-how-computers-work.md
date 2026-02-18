# How Computers Actually Work

> If you don't understand the machine, you can't write good software for it.

---

## Why This Matters

You don't need a CS degree. But you need to understand the machine well enough to predict what your code does and why it's slow (or fast). This chapter gives you that.

---

## The Computer in 60 Seconds

```
┌──────────────────────────────────────────────────┐
│                    YOUR CODE                      │
├──────────────────────────────────────────────────┤
│              Operating System (macOS)              │
├──────────────────────────────────────────────────┤
│                    Hardware                        │
│                                                    │
│   ┌─────┐    ┌─────────┐    ┌──────┐    ┌─────┐ │
│   │ CPU │◀──▶│  Memory  │◀──▶│ Disk │    │ NIC │ │
│   │     │    │  (RAM)   │    │(SSD) │    │(Net)│ │
│   └─────┘    └─────────┘    └──────┘    └─────┘ │
│                                                    │
│   Fast ◀──────────────────────────────────▶ Slow  │
│   Small ◀─────────────────────────────────▶ Big   │
│   Expensive ◀─────────────────────────────▶ Cheap │
└──────────────────────────────────────────────────┘
```

Everything your code does boils down to: **move data between these components, and have the CPU do math on it.**

---

## CPU — The Brain

The CPU executes instructions. That's it. Add two numbers. Compare values. Jump to a different instruction. Billions of times per second.

### What you need to know:
- **Clock speed** (e.g., 3.5 GHz) = 3.5 billion operations per second
- **Cores** = independent processors. 8 cores = 8 things in parallel.
- **CPU Cache** = tiny, ultra-fast memory on the CPU chip
  - L1: ~1ns access, ~64KB per core
  - L2: ~4ns access, ~256KB per core
  - L3: ~12ns access, ~8-32MB shared

### Why it matters for code:

```python
# SLOW — data is scattered in memory, cache misses on every access
for item in linked_list:
    process(item)

# FAST — data is contiguous in memory, CPU cache loads entire chunks
for item in array:
    process(item)
```

**Mental model:** Data that lives close together in memory gets processed faster. Arrays beat linked lists not because of algorithmic complexity, but because of cache locality.

---

## Memory (RAM) — The Desk

RAM is your working space. Everything your program is actively using lives here.

### What you need to know:
- Access time: ~100 nanoseconds
- Your Mac: 8-64GB
- **Volatile** — power off = data gone
- Programs get their own virtual memory space (the OS fakes it)

### The Stack vs The Heap

```
┌─────────────────────────────┐
│          STACK               │  ← Fast, automatic, small
│  - Function calls            │  ← Each function gets a "frame"
│  - Local variables           │  ← Cleaned up when function returns
│  - Fixed size                │
├─────────────────────────────┤
│          HEAP                │  ← Slower, manual-ish, large
│  - Dynamic data              │  ← Objects, arrays, strings
│  - Allocated on demand       │  ← Must be freed (GC or manual)
│  - Can fragment              │
└─────────────────────────────┘
```

```go
func example() {
    x := 42              // Stack — fast, automatic
    name := "hello"      // Stack (pointer), heap (string data)
    users := make([]User, 1000)  // Heap — dynamic, GC manages it
}
// When example() returns, stack is instantly cleaned up
// Heap data lives until garbage collector reclaims it
```

**Mental model:** Stack = fast and automatic but limited. Heap = flexible but slower and needs management. Languages like Go and Rust help you make the right choice. JavaScript/Python put almost everything on the heap.

---

## Disk (SSD) — The Filing Cabinet

Persistent storage. Data survives power off.

### What you need to know:
- SSD read: ~100 microseconds (1000x slower than RAM)
- HDD read: ~10 milliseconds (100,000x slower than RAM)
- Your files, databases, Docker images — all on disk

### The speed hierarchy:

| Location | Access Time | Analogy |
|----------|------------|---------|
| CPU L1 Cache | ~1 ns | Thought in your head |
| CPU L2 Cache | ~4 ns | Note on your desk |
| CPU L3 Cache | ~12 ns | Book on your shelf |
| RAM | ~100 ns | File in your cabinet |
| SSD | ~100 μs | Book in the library |
| Network (same region) | ~500 μs | Package from across town |
| HDD | ~10 ms | Book from a warehouse |
| Network (cross-continent) | ~150 ms | Package from another country |

**Mental model:** Every time your code touches disk or network, it's 1,000-1,000,000x slower than memory. This is why caching exists. This is why databases have buffer pools. This is why you never read a file in a tight loop.

---

## Network — The Post Office

How computers talk to each other.

### What you need to know:
- **Latency** = time for a single round trip (ms)
- **Bandwidth** = how much data per second (Mbps)
- **Latency matters more than bandwidth** for most applications

Same room: ~0.5ms. Same city: ~5ms. Cross-country: ~50ms. Cross-ocean: ~150ms.

Every API call, every database query to a remote server, every file download — you're paying this latency cost.

```python
# This makes 100 API calls = 100 × 50ms = 5 SECONDS
for user_id in user_ids:
    user = api.get_user(user_id)

# This makes 1 API call with all IDs = 50ms total
users = api.get_users(user_ids)
```

**Mental model:** Network calls are expensive. Batch them. Cache the results. Every round trip is a tax on your user's time.

---

## How It All Connects

When you run `go run main.go`:

1. **Disk → RAM:** The OS loads your compiled program from SSD into memory
2. **RAM → CPU:** The CPU fetches instructions from memory
3. **CPU Cache:** Frequently accessed data gets cached on-chip
4. **CPU executes:** Arithmetic, comparisons, branching
5. **RAM:** Results stored back to memory
6. **Network:** If your program calls an API, data goes out the NIC, across the internet, and back
7. **Disk:** If your program writes a file, data goes from RAM to SSD

Every performance problem is a bottleneck in this chain. Profiling tells you where.

---

## Practical Implications

### 1. Allocations Have a Cost
Creating objects = asking the OS for heap memory. Do it millions of times = slow.

```go
// SLOW — allocates a new string every iteration
var result string
for _, s := range strings {
    result += s  // New allocation each time!
}

// FAST — pre-allocated buffer
var builder strings.Builder
for _, s := range strings {
    builder.WriteString(s)
}
result := builder.String()
```

### 2. I/O Dominates
Most programs spend 90% of time waiting for I/O (disk, network, database). CPU is rarely the bottleneck.

**Implication:** Optimize I/O first. Reduce network calls. Add caching. Use connection pools. The CPU will take care of itself.

### 3. Concurrency ≠ Parallelism
- **Concurrency:** Handling multiple things at once (juggling)
- **Parallelism:** Doing multiple things at once (multiple jugglers)

A single-core CPU can be concurrent (switching between tasks fast) but not parallel.

An 8-core CPU can be both concurrent and parallel.

```go
// Concurrent — one goroutine waits while others work
go fetchFromAPI()     // Waiting for network...
go readFromDisk()     // Waiting for I/O...
go computeHash()      // Actually using CPU

// Parallel — multiple goroutines on different cores
// Go runtime automatically distributes across cores
```

---

## Exercises

1. Run `top` or `htop`. Find your highest CPU and memory processes. What are they doing?

2. Time a simple program that allocates 1 million small objects vs. one large array. Measure the difference.

3. Write a program that reads a 100MB file: (a) all at once, (b) line by line. Time both. Why is one faster?

4. Make 100 sequential HTTP requests vs. 100 concurrent ones. Measure the difference.

5. Run `diskutil info /` and note your SSD's read/write speeds. How does this compare to the numbers above?

---

## Recommended Reading

- [What Every Programmer Should Know About Memory](https://people.freebsd.org/~lstewart/articles/cpumemory.pdf) — Ulrich Drepper
- [Latency Numbers Every Programmer Should Know](https://colin-scott.github.io/personal_website/research/interactive_latency.html)
- [The Architecture of Open Source Applications](https://aosabook.org/en/) — Free book

---

*← [Phase 1: Editor Setup](../phase-1-foundation/06-editor-setup.md) · [Data & Algorithms →](02-data-and-algorithms.md)*
