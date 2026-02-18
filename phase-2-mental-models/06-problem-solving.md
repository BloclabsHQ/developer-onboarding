# Problem Solving & Debugging

> The #1 skill that separates senior engineers from everyone else is not writing code — it's solving problems.

---

## The Scientific Method for Code

Debugging is not guessing. It's a systematic process:

```
1. OBSERVE    → What exactly is happening? (not what you think is happening)
2. REPRODUCE  → Can you make it happen again, consistently?
3. HYPOTHESIZE → What could cause this behavior?
4. TEST       → Change ONE thing to test your hypothesis
5. CONCLUDE   → Was your hypothesis correct?
6. REPEAT     → If wrong, form a new hypothesis
```

### The Cardinal Sin: Changing Multiple Things at Once

If you change 3 things and the bug goes away — which fix actually worked? You don't know. And the other 2 changes might introduce new bugs.

**One change at a time. Always.**

---

## Read the Error Message

This seems obvious but most juniors skip it. The error message tells you:
- **What** went wrong
- **Where** it went wrong (file, line number)
- **Why** it went wrong (usually)

```
panic: runtime error: index out of range [5] with length 3
    goroutine 1 [running]:
    main.processItems(...)
        /app/main.go:42
```

This tells you everything:
- **What:** Array index out of range
- **Where:** `main.go`, line 42, function `processItems`
- **Why:** You accessed index 5 but the array only has 3 elements

Before Googling, before asking a colleague, before reaching for AI — **read the error message**. Actually read it. Word by word.

---

## The Debugging Toolkit

### 1. Print Debugging (The Humble Log)

The fastest debugging tool:

```go
fmt.Printf("DEBUG: value=%v, type=%T\n", x, x)
```

```python
print(f"DEBUG: {variable=}, {type(variable)=}")
```

```javascript
console.log('DEBUG:', { variable, typeof: typeof variable });
```

**When to use:** Quick investigation, understanding flow, verifying assumptions.

**Rules:**
- Remove debug prints before committing
- Use structured logging in production (not print statements)
- Print the *type* too, not just the value

### 2. Binary Search for Bugs

When you don't know where the bug is:

```
Code works at line 1
Code broken at line 100

Check line 50 → still works
Check line 75 → broken!
Check line 62 → still works
Check line 68 → broken!
...narrow down to the exact line
```

This is `git bisect` for finding which commit broke things:
```bash
git bisect start
git bisect bad              # Current commit is broken
git bisect good abc1234     # This old commit was working
# Git checks out the middle commit
# Test it, then:
git bisect good   # or
git bisect bad
# Repeat until Git finds the exact commit
```

### 3. Rubber Duck Debugging

Explain the problem out loud. To a rubber duck. To a colleague. To your AI assistant. To thin air.

The act of articulating the problem forces you to organize your thoughts. You'll often solve it mid-sentence.

**Why it works:** Your brain uses different pathways for thinking vs. speaking. Explaining forces you through both.

### 4. Read the Code, Don't Just Run It

Before adding more logs, before stepping through the debugger — **read the code**. Trace the execution path in your head:

- What are the inputs?
- What does each line do?
- Where could the data be wrong?
- What assumptions am I making?

Most bugs are assumption violations. You assumed the array wouldn't be empty. You assumed the API would return 200. You assumed the user would provide valid input.

---

## Common Bug Categories

### 1. Off-by-One Errors
```python
# Bug: range(len(array)) is 0..n-1, but you're accessing array[i+1]
for i in range(len(array)):
    diff = array[i+1] - array[i]  # 💥 Index out of range on last iteration
    
# Fix:
for i in range(len(array) - 1):
    diff = array[i+1] - array[i]
```

### 2. Null/Nil References
```go
// Bug: user could be nil
user, err := getUser(id)
fmt.Println(user.Name)  // 💥 nil pointer dereference

// Fix: check error first
user, err := getUser(id)
if err != nil {
    return err
}
fmt.Println(user.Name)
```

### 3. Race Conditions
Two things accessing shared data simultaneously:
```go
// Bug: concurrent writes to a map
go func() { m["key"] = "value1" }()
go func() { m["key"] = "value2" }()
// 💥 fatal error: concurrent map writes

// Fix: use sync.Mutex or sync.Map
```

### 4. Silent Failures
```python
# Bug: exception swallowed, function returns wrong result silently
try:
    result = int(user_input)
except:
    pass  # 💥 result is undefined, no error shown

# Fix: handle specifically, log the error
try:
    result = int(user_input)
except ValueError as e:
    logger.warning(f"Invalid input: {user_input}, error: {e}")
    result = 0  # explicit default
```

### 5. State Bugs
The program works sometimes and fails other times. The bug depends on *order of operations* or *accumulated state*.

**Diagnostic:** Does it fail on the first try or only after running a while? Does restarting fix it? These point to state bugs.

---

## How to Ask for Help

When you're stuck, don't say: *"It doesn't work."*

Instead:

```
1. What I'm trying to do: [goal]
2. What I expected to happen: [expected]
3. What actually happened: [actual]
4. What I've already tried: [attempts]
5. Relevant error messages: [paste them]
6. Relevant code: [minimal reproduction]
```

This applies to asking colleagues, posting on Stack Overflow, and prompting AI.

---

## The 15-Minute Rule

When you're stuck:
1. Spend **15 minutes** trying to solve it yourself
2. If stuck after 15 minutes → **ask for help**

Less than 15 = you didn't try hard enough.
More than 15 = you're wasting time being a hero.

**Asking for help is not weakness. It's efficiency.**

---

## Debugging Mindset

1. **Assume your code is wrong.** Not the language, not the framework, not the compiler. Your code.
2. **Be suspicious of "it works on my machine."** It means you have an environment difference. Find it.
3. **The bug is always logical.** Computers do exactly what they're told. If the output is wrong, the instructions are wrong.
4. **Recent changes are the prime suspect.** What changed since it last worked? Start there.
5. **Simplify.** Remove code until the bug disappears. The last thing you removed was the problem.
6. **Sleep on it.** Seriously. Your subconscious will solve it overnight. Fresh eyes tomorrow > frustrated eyes today.

---

## Exercises

1. Clone a repo with a known bug (or have a teammate introduce one). Use `git bisect` to find the exact commit.

2. Write a program with an intentional off-by-one error. Practice the scientific method: observe, reproduce, hypothesize, test.

3. Take a bug you recently fixed. Write a post-mortem: What was the root cause? How did you find it? How can you prevent it?

4. Practice rubber ducking: explain a piece of complex code to a teammate without them looking at the code. Did they find issues?

---

*← [Databases & State](05-databases-and-state.md) · [Operating Systems →](07-operating-systems.md)*
