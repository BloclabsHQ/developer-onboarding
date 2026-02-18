# Error Handling

> Error handling is the MAIN code path. The happy path is the exception.

---

## The Mental Model

> "92% of catastrophic failures in distributed systems are caused by incorrect handling of non-fatal errors." — Simple Testing Can Prevent Most Critical Failures (Yuan et al., 2014)

Your code will encounter: network timeouts, malformed data, full disks, permission denied, rate limits, null values, and a thousand other things. The happy path is the minority of executions. Error handling is where your code lives.

---

## Go — Errors Are Values

Go makes error handling explicit and unavoidable:

```go
// The pattern: every function that can fail returns an error
result, err := doSomething()
if err != nil {
    return fmt.Errorf("doing something: %w", err)
}
```

### Never Ignore Errors
```go
// ❌ NEVER
result, _ := riskyOperation()

// ❌ NEVER
json.Unmarshal(data, &obj) // error silently ignored

// ✅ ALWAYS handle
result, err := riskyOperation()
if err != nil {
    return fmt.Errorf("risky operation: %w", err)
}
```

### Error Wrapping — Context Is Everything
```go
// ❌ Raw error — no context
if err != nil {
    return err  // "connection refused" — from where?
}

// ✅ Wrapped — full story
if err != nil {
    return fmt.Errorf("fetching user %s from database: %w", userID, err)
}
// "fetching user abc123 from database: connection refused"
```

### Custom Error Types
```go
type NotFoundError struct {
    Resource string
    ID       string
}

func (e *NotFoundError) Error() string {
    return fmt.Sprintf("%s %s not found", e.Resource, e.ID)
}

// Check error type
var notFound *NotFoundError
if errors.As(err, &notFound) {
    // Return 404 to client
}
```

---

## Python — Exceptions

```python
# ❌ Catch-all — hides bugs
try:
    result = process(data)
except:
    pass  # What went wrong? We'll never know.

# ❌ Too broad
try:
    result = process(data)
except Exception:
    logger.error("Something failed")  # Not helpful

# ✅ Specific, contextual
try:
    user = fetch_user(user_id)
except ConnectionError:
    logger.warning(f"Database unreachable fetching user {user_id}")
    raise ServiceUnavailableError("Please try again later")
except UserNotFoundError:
    raise HTTPNotFound(f"User {user_id} not found")
```

### Custom Exceptions
```python
class AppError(Exception):
    """Base error for our application."""
    pass

class ValidationError(AppError):
    def __init__(self, field: str, message: str):
        self.field = field
        self.message = message
        super().__init__(f"{field}: {message}")

class NotFoundError(AppError):
    def __init__(self, resource: str, id: str):
        self.resource = resource
        self.id = id
        super().__init__(f"{resource} {id} not found")
```

---

## JavaScript — Try/Catch + Async

```javascript
// ❌ Unhandled promise rejection (crashes Node.js)
const data = await fetch(url);

// ❌ Silent failure
try {
    const data = await fetch(url);
} catch (e) {
    // empty catch = silent failure
}

// ✅ Handle specifically
try {
    const response = await fetch(url);
    if (!response.ok) {
        throw new APIError(`HTTP ${response.status}: ${response.statusText}`);
    }
    return await response.json();
} catch (error) {
    if (error instanceof APIError) {
        logger.warn(`API error fetching ${url}: ${error.message}`);
        throw error;
    }
    logger.error(`Network error fetching ${url}: ${error.message}`);
    throw new NetworkError(`Failed to reach ${url}`);
}
```

---

## Universal Principles

### 1. User-Facing vs Developer-Facing Errors

```
Developer sees:  "pq: duplicate key value violates unique constraint users_email_key"
User sees:       "An account with this email already exists."
```

Never expose internal errors to users. Map them to friendly messages.

### 2. Log for Developers, Respond for Users

```go
// Log the full technical detail
logger.Error("database query failed",
    "query", "SELECT * FROM users WHERE id=$1",
    "error", err.Error(),
    "user_id", userID,
)

// Respond with user-friendly message
return APIError{
    Code:    500,
    Message: "We're having trouble right now. Please try again.",
}
```

### 3. Fail Fast

Validate inputs at the boundary. Don't let bad data travel deep into your system.

```go
func CreateUser(req CreateUserRequest) error {
    // Validate IMMEDIATELY
    if req.Email == "" {
        return ValidationError{Field: "email", Message: "required"}
    }
    if !isValidEmail(req.Email) {
        return ValidationError{Field: "email", Message: "invalid format"}
    }
    // Only proceed with valid data
    return userRepo.Create(req)
}
```

### 4. Structured Errors

```go
// ❌ String errors — impossible to handle programmatically
return errors.New("user not found")

// ✅ Typed errors — callers can make decisions
return &NotFoundError{Resource: "user", ID: userID}
```

### 5. Don't Panic

```go
// ❌ panic crashes the entire program
panic("something went wrong")

// ✅ Return errors, let the caller decide
return fmt.Errorf("something went wrong: %w", err)
```

Panic in Go, sys.exit in Python, process.exit in Node — these are for truly unrecoverable situations only (corrupted state, failed initialization).

---

## Error Handling Checklist

- [ ] Every error is handled (no ignored return values)
- [ ] Errors have context (where + what + why)
- [ ] User-facing errors are friendly, developer logs are detailed
- [ ] Input validation happens at the boundary
- [ ] Custom error types for domain-specific failures
- [ ] No catch-all/bare except blocks
- [ ] Errors propagate up with wrapping, not swallowing

---

*← [Code as Craft](01-code-as-craft.md) · [Testing →](03-testing.md)*
