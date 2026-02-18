# Testing

> Tests are not proof your code works. Tests are proof your code does what you THINK it does.

---

## The Testing Pyramid

```
         ╱╲
        ╱ E2E╲          Slow, expensive, brittle
       ╱ Tests ╲         Few: critical user journeys
      ╱──────────╲
     ╱ Integration╲      Medium speed, medium cost
    ╱    Tests     ╲     Some: APIs, DB, service boundaries
   ╱────────────────╲
  ╱    Unit Tests    ╲   Fast, cheap, reliable
 ╱                    ╲  Many: business logic, pure functions
╱──────────────────────╲
```

**Invest most in unit tests.** They're fast, reliable, and catch most bugs. Integration tests verify components work together. E2E tests verify critical paths only.

---

## What to Test

### Always Test
- Business logic (calculations, validations, transformations)
- Edge cases (empty input, nil, zero, negative, max values)
- Error paths (what happens when things fail?)
- Public APIs (the contract with your callers)

### Don't Test
- Framework internals (the HTTP library works, trust it)
- Simple getters/setters (no logic = no bugs)
- Implementation details (test behavior, not structure)
- Everything at once (that's integration testing)

---

## Unit Tests — The Foundation

### Go — Table-Driven Tests
```go
func TestCalculateFee(t *testing.T) {
    tests := []struct {
        name     string
        amount   decimal.Decimal
        chain    Chain
        expected decimal.Decimal
    }{
        {"small ETH", d("10.0"), Ethereum, d("0.03")},
        {"large ETH", d("10000.0"), Ethereum, d("25.0")},
        {"zero amount", d("0"), Ethereum, d("0")},
        {"SOL fee", d("100.0"), Solana, d("0.01")},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got := CalculateFee(tt.amount, tt.chain)
            if !got.Equal(tt.expected) {
                t.Errorf("CalculateFee(%v, %v) = %v, want %v",
                    tt.amount, tt.chain, got, tt.expected)
            }
        })
    }
}
```

### Python — pytest
```python
import pytest

def test_calculate_fee_small_amount():
    assert calculate_fee(10.0, "ethereum") == 0.03

def test_calculate_fee_zero():
    assert calculate_fee(0, "ethereum") == 0

@pytest.mark.parametrize("amount,chain,expected", [
    (10.0, "ethereum", 0.03),
    (10000.0, "ethereum", 25.0),
    (100.0, "solana", 0.01),
])
def test_calculate_fee(amount, chain, expected):
    assert calculate_fee(amount, chain) == expected

def test_calculate_fee_invalid_chain():
    with pytest.raises(ValueError, match="unsupported chain"):
        calculate_fee(100.0, "nonexistent")
```

### JavaScript — Vitest/Jest
```javascript
describe('calculateFee', () => {
    it('calculates ETH fee correctly', () => {
        expect(calculateFee(10.0, 'ethereum')).toBe(0.03);
    });

    it('returns zero for zero amount', () => {
        expect(calculateFee(0, 'ethereum')).toBe(0);
    });

    it('throws for unsupported chain', () => {
        expect(() => calculateFee(100, 'fake')).toThrow('unsupported chain');
    });
});
```

---

## Test Naming

Name tests so failures tell you what broke:

```
✅ TestCalculateFee_ZeroAmount_ReturnsZero
✅ TestCreateUser_DuplicateEmail_ReturnsConflictError
✅ TestParseTransaction_InvalidJSON_ReturnsParseError

❌ TestCalculateFee1
❌ TestHappy
❌ TestEdgeCase
```

---

## Integration Tests

Test real components together (database, API, external services).

```go
func TestUserRepository_Create(t *testing.T) {
    // Setup: real test database
    db := setupTestDB(t)
    repo := NewUserRepository(db)

    // Act
    user, err := repo.Create(context.Background(), CreateUserInput{
        Name:  "Test User",
        Email: "test@example.com",
    })

    // Assert
    require.NoError(t, err)
    assert.Equal(t, "Test User", user.Name)

    // Verify it's actually in the database
    found, err := repo.GetByID(context.Background(), user.ID)
    require.NoError(t, err)
    assert.Equal(t, user.ID, found.ID)
}
```

---

## Mocking — Use Sparingly

Mocks replace real dependencies with fake ones. Useful but overused.

```go
// Interface for the dependency
type EmailSender interface {
    Send(to, subject, body string) error
}

// Real implementation
type SMTPSender struct { ... }

// Mock for testing
type MockSender struct {
    SentEmails []Email
}

func (m *MockSender) Send(to, subject, body string) error {
    m.SentEmails = append(m.SentEmails, Email{to, subject, body})
    return nil
}

func TestOrderProcessor_SendsConfirmation(t *testing.T) {
    mock := &MockSender{}
    processor := NewOrderProcessor(mock)

    processor.Process(testOrder)

    require.Len(t, mock.SentEmails, 1)
    assert.Equal(t, "order@test.com", mock.SentEmails[0].To)
}
```

**Rules:**
- Mock external services (APIs, email, payment)
- Don't mock your own code (if you need to, your design might be wrong)
- Don't mock the database — use a test database

---

## Code Coverage

Coverage measures what percentage of code runs during tests.

```bash
# Go
go test -coverprofile=coverage.out ./...
go tool cover -html=coverage.out

# Python
pytest --cov=src --cov-report=html

# JavaScript
vitest --coverage
```

**Coverage is NOT quality.** 100% coverage with bad assertions is worse than 60% coverage with good assertions. A test that runs code but never checks results catches nothing.

Aim for: **80%+ on business logic, don't obsess about 100% overall.**

---

## TDD (Test-Driven Development)

```
1. Write a failing test (RED)
2. Write minimal code to pass (GREEN)
3. Refactor (REFACTOR)
4. Repeat
```

**When TDD works well:**
- Pure functions with clear inputs/outputs
- Bug fixes (write test that reproduces bug, then fix)
- API design (writing tests forces you to think about the interface)

**When TDD doesn't fit:**
- Exploratory/prototype code (you don't know the design yet)
- UI code (better tested manually or with snapshot tests)
- Infrastructure code (integration tests are more valuable)

**BlocLabs stance:** TDD is a tool, not a religion. Use it when it helps.

---

## Checklist

- [ ] Business logic has unit tests
- [ ] Edge cases are covered (nil, empty, zero, max)
- [ ] Error paths are tested
- [ ] Test names describe behavior, not implementation
- [ ] No mocking of internal code
- [ ] Tests run fast (<10s for unit suite)
- [ ] CI runs tests on every PR

---

*← [Error Handling](02-error-handling.md) · [Architecture →](04-architecture.md)*
