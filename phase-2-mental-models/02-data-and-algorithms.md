# Data Structures & Algorithms

> You don't need to grind LeetCode. But you need to know WHEN to use WHAT and WHY.

---

## The Mental Model

Every data structure is a trade-off:

```
Fast reads  ◀──────────────────▶  Fast writes
Fast search ◀──────────────────▶  Low memory
Simple      ◀──────────────────▶  Flexible
```

There is no "best" data structure. There is only the right one for your problem.

---

## Big O — How to Think About Performance

Big O tells you how performance scales as data grows.

| Notation | Name | Example | 1,000 items | 1,000,000 items |
|----------|------|---------|-------------|-----------------|
| O(1) | Constant | Hash map lookup | 1 op | 1 op |
| O(log n) | Logarithmic | Binary search | 10 ops | 20 ops |
| O(n) | Linear | Array scan | 1,000 ops | 1,000,000 ops |
| O(n log n) | Linearithmic | Sorting | 10,000 ops | 20,000,000 ops |
| O(n²) | Quadratic | Nested loops | 1,000,000 ops | 1,000,000,000,000 ops |

**Mental model:** If your data can grow, O(n²) will eventually kill you. O(n log n) is usually fine. O(1) is the dream.

---

## Arrays / Slices / Lists

The most fundamental structure. Contiguous memory.

```go
// Go
items := []string{"a", "b", "c"}
items = append(items, "d")
```

```python
# Python
items = ["a", "b", "c"]
items.append("d")
```

```javascript
// JavaScript
const items = ["a", "b", "c"];
items.push("d");
```

| Operation | Time |
|-----------|------|
| Access by index | O(1) |
| Search (unsorted) | O(n) |
| Append | O(1) amortized |
| Insert at beginning | O(n) — everything shifts |
| Delete from middle | O(n) — everything shifts |

**When to use:** Ordered data, sequential access, when you know the index. This is your default. Start here unless you have a reason not to.

---

## Hash Maps / Dictionaries

Key-value pairs with near-instant lookup.

```go
// Go
users := map[string]User{
    "alice": {Name: "Alice", Age: 30},
}
user, exists := users["alice"]  // O(1)
```

```python
# Python
users = {"alice": User("Alice", 30)}
user = users.get("alice")  # O(1)
```

```javascript
// JavaScript
const users = new Map();
users.set("alice", { name: "Alice", age: 30 });
const user = users.get("alice");  // O(1)
```

| Operation | Time |
|-----------|------|
| Get by key | O(1) average |
| Set | O(1) average |
| Delete | O(1) average |
| Search by value | O(n) |

**When to use:** Looking up data by a key. Counting occurrences. Deduplication. Caching. This is the second most important structure after arrays.

**Common pattern — counting:**
```python
word_count = {}
for word in words:
    word_count[word] = word_count.get(word, 0) + 1
```

---

## Sets

Unique values with fast membership testing.

```python
# Python
seen = set()
for item in items:
    if item in seen:  # O(1)
        print(f"Duplicate: {item}")
    seen.add(item)
```

```go
// Go (no built-in set — use map[T]struct{})
seen := map[string]struct{}{}
seen["alice"] = struct{}{}
_, exists := seen["alice"]  // O(1)
```

| Operation | Time |
|-----------|------|
| Contains | O(1) |
| Add | O(1) |
| Remove | O(1) |
| Union/Intersection | O(n) |

**When to use:** Deduplication, membership testing, set operations (union, intersection, difference).

---

## Stacks (LIFO)

Last In, First Out. Like a stack of plates.

```python
stack = []
stack.append("a")  # push
stack.append("b")
top = stack.pop()   # "b" — last in, first out
```

**When to use:** Undo operations, parsing (matching brackets), backtracking, function call tracking.

---

## Queues (FIFO)

First In, First Out. Like a line at a store.

```python
from collections import deque
queue = deque()
queue.append("first")    # enqueue
queue.append("second")
item = queue.popleft()   # "first" — first in, first out
```

**When to use:** Task processing, message queues, BFS (breadth-first search), rate limiting, buffering.

**Real-world:** Every message queue (SQS, RabbitMQ, Kafka) is fundamentally a queue.

---

## Trees

Hierarchical data. Nodes with children.

```
        CEO
       /    \
     CTO    CFO
    /   \
  Dev1  Dev2
```

**Binary Search Tree:** Left child < parent < right child. Enables O(log n) search.

**When to use:** Hierarchical data (file systems, org charts), sorted data that needs fast insert/delete, database indexes (B-trees).

**You rarely build trees yourself.** But you use them constantly:
- File systems are trees
- DOM (HTML) is a tree
- Database indexes (B-tree) are trees
- JSON is a tree

---

## Graphs

Nodes connected by edges. Trees are a special case of graphs.

```
A ── B ── D
|    |
C ── E
```

**When to use:** Social networks, routing, dependency resolution, recommendation engines.

**Two key algorithms:**
- **BFS** (Breadth-First Search) — explore level by level. Find shortest path.
- **DFS** (Depth-First Search) — explore as deep as possible first. Find if path exists.

---

## The Decision Table

| Problem | Data Structure | Why |
|---------|---------------|-----|
| Look up by key | Hash Map | O(1) access |
| Ordered collection | Array/Slice | Index access, iteration |
| Remove duplicates | Set | O(1) contains |
| Process in order received | Queue | FIFO |
| Undo/redo | Stack | LIFO |
| Hierarchical data | Tree | Natural representation |
| Relationships between entities | Graph | Connections |
| Sorted data + fast insert | Balanced BST / B-tree | O(log n) for all ops |
| Priority processing | Heap / Priority Queue | O(log n) insert, O(1) max/min |

---

## Sorting — When It Matters

Most of the time, use your language's built-in sort:

```go
sort.Slice(users, func(i, j int) bool {
    return users[i].Name < users[j].Name
})
```

```python
users.sort(key=lambda u: u.name)
```

```javascript
users.sort((a, b) => a.name.localeCompare(b.name));
```

Built-in sorts are O(n log n). That's good enough for almost everything.

**When to care about sorting algorithms:** Never, unless you're in an interview or building a database engine.

---

## Practical Patterns

### Two-Pointer
Process a sorted array from both ends:
```python
def two_sum_sorted(nums, target):
    left, right = 0, len(nums) - 1
    while left < right:
        total = nums[left] + nums[right]
        if total == target: return [left, right]
        elif total < target: left += 1
        else: right -= 1
```

### Sliding Window
Process a subarray of fixed or variable size:
```python
def max_sum_subarray(nums, k):
    window = sum(nums[:k])
    max_sum = window
    for i in range(k, len(nums)):
        window += nums[i] - nums[i-k]
        max_sum = max(max_sum, window)
    return max_sum
```

### Frequency Map
Count occurrences:
```python
from collections import Counter
counts = Counter(words)  # {"the": 42, "code": 15, ...}
most_common = counts.most_common(10)
```

---

## The 80/20 Rule

80% of problems in professional development use:
1. **Arrays** — iteration, transformation, filtering
2. **Hash maps** — lookups, counting, caching
3. **Sets** — deduplication, membership

Master these three deeply. Know the rest conceptually.

---

## Exercises

1. Given a list of 1M user IDs with duplicates, find the unique count. Use a set. Time it vs sorting + dedup.

2. Build a simple LRU cache using a hash map and a doubly-linked list (or use `collections.OrderedDict` in Python).

3. Given API access logs, find the top 10 most requested endpoints using a frequency map.

4. Implement a task queue that processes items FIFO. Add priority support (priority queue).

5. Profile: sort 1M random integers using your language's built-in sort. How long does it take? Double the size — does time double?

---

## Recommended Reading

- [Big-O Cheat Sheet](https://www.bigocheatsheet.com/)
- [Visualgo](https://visualgo.net/) — Animated visualizations of algorithms
- *Grokking Algorithms* — Aditya Bhargava (visual, beginner-friendly)
- *Introduction to Algorithms* — CLRS (reference, not cover-to-cover)

---

*← [How Computers Work](01-how-computers-work.md) · [Systems Thinking →](03-systems-thinking.md)*
