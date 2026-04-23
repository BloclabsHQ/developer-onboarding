# Code Quality Standards & Checklists

## Pre-Commit Checklist

Before any code is committed:

- [ ] Compiles with zero warnings (warnings are bugs waiting to happen)
- [ ] All states handled (loading, error, empty, loaded, idle)
- [ ] Every error is checked -- no silent swallowing, no ignored return values
- [ ] Errors are wrapped with context (what was happening when it failed)
- [ ] Edge cases handled: nil, empty, zero, negative, overflow, max values
- [ ] No hardcoded secrets, tokens, or credentials
- [ ] Function parameters are 5 or fewer
- [ ] Function length is under ~30 lines (readable without scrolling)
- [ ] Nesting depth is 3 or fewer
- [ ] Each module/package has a single, nameable purpose

## Code Review Standards

### Correctness
- Are all edge cases handled?
- Is every error checked and wrapped with context?
- Are there race conditions or shared mutable state?
- Are all states in the state machine accounted for?

### Clarity
- Can you understand each function without reading its callers?
- Are names descriptive and unambiguous?
- Is the control flow linear (no goto, minimal early returns in complex functions)?
- Would a new team member understand this in 5 minutes?

### Architecture
- Does each package/module have a single, nameable purpose?
- Do imports flow in one direction (down)?
- Is there a clear boundary between I/O and business logic?
- Can the API be used correctly by reading only the signature?
- Are invalid states unrepresentable?

### Performance (only when relevant)
- Was this optimization measured, not assumed?
- Is there a benchmark proving the improvement?
- Does the optimization sacrifice correctness or clarity?

## Error Handling Standards

### The Rules
1. Never ignore errors. Every error must be explicitly handled or propagated.
2. Wrap errors with context: what operation failed and why it matters.
3. Expected errors are domain types (NotFound, ValidationError, Conflict).
4. Unexpected errors propagate up to the nearest error boundary.
5. No log-and-continue patterns. Either handle it or let it propagate.
6. Error messages should be actionable: tell the user/developer what to do next.

### Error Categories
- **Operational errors**: Expected failures (network timeout, file not found, validation). Handle gracefully.
- **Programming errors**: Bugs (null reference, out of bounds, type mismatch). Fail fast and loud.
- **System errors**: Infrastructure failures (disk full, OOM, hardware). Degrade gracefully.

## Concurrency Standards

1. Every worker/goroutine/task needs a clear exit condition
2. The creator of a channel/queue is responsible for closing it
3. Context cancellation is mandatory -- no fire-and-forget without timeout
4. Shared mutable state requires explicit synchronization (actors, mutexes, channels)
5. Prefer message passing over shared memory
6. If you add concurrency, measure that sequential was actually too slow first

## Testing Standards

### What to Test
- Business logic and domain rules (heavily)
- API contracts and data serialization
- Error paths and edge cases
- State machine transitions
- Critical user workflows (sparingly, at the E2E level)

### What Not to Test
- Framework internals (the framework authors already tested those)
- Private implementation details that may change
- Trivial getters/setters with no logic
- Third-party library behavior

### Test Quality
- Tests should be deterministic (no flaky tests)
- Tests should be fast (unit tests under 1 second each)
- Tests should be independent (no shared state between tests)
- Test names should describe the expected behavior, not the implementation
- One assertion per test (where practical)
