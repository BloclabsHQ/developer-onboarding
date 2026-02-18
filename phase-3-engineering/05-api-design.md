# API Design

> Your API is a contract. Once published, changing it breaks people. Design it right.

---

## REST Principles

### Resources, Not Actions

```
❌ POST /getUser
❌ POST /createUser
❌ GET  /fetchAllTransactions

✅ GET    /users/{id}          → Get a user
✅ POST   /users               → Create a user
✅ PUT    /users/{id}          → Replace a user
✅ PATCH  /users/{id}          → Update a user
✅ DELETE /users/{id}          → Delete a user
✅ GET    /users/{id}/transactions  → Get user's transactions
```

### Use Nouns (Plural)
```
✅ /users, /transactions, /chains
❌ /user, /getTransaction, /chain-list
```

---

## Status Codes — Mean What You Say

| Code | When |
|------|------|
| 200 | Success (with body) |
| 201 | Created (POST success, include Location header) |
| 204 | Success, no body (DELETE) |
| 400 | Client sent bad data (validation error) |
| 401 | Not authenticated (no/invalid token) |
| 403 | Authenticated but not authorized |
| 404 | Resource doesn't exist |
| 409 | Conflict (duplicate email, version conflict) |
| 422 | Unprocessable (valid JSON, invalid business logic) |
| 429 | Rate limited |
| 500 | Server error (our fault, not theirs) |

**Don't return 200 for everything.** If you return `200 { "error": "not found" }`, your API is lying.

---

## Request/Response Design

### Consistent Response Envelope
```json
// Success
{
    "data": { "id": "123", "name": "Cris" },
    "meta": { "requestId": "abc-123" }
}

// Error
{
    "error": {
        "code": "VALIDATION_ERROR",
        "message": "Email is required",
        "details": [
            { "field": "email", "message": "required" }
        ]
    },
    "meta": { "requestId": "abc-123" }
}

// Collection
{
    "data": [ ... ],
    "meta": {
        "total": 1250,
        "page": 1,
        "pageSize": 25,
        "requestId": "abc-123"
    }
}
```

---

## Pagination

Always paginate collections. Unbounded queries kill databases.

### Offset-based (simple)
```
GET /transactions?page=2&pageSize=25
```

### Cursor-based (scalable)
```
GET /transactions?cursor=eyJpZCI6MTIzfQ&limit=25
```

Cursor-based is better for large datasets — offset pagination gets slower as pages increase.

---

## Filtering & Sorting

```
GET /transactions?chain=ethereum&status=confirmed&sort=-created_at&limit=10
```

- Prefix `-` for descending sort
- Use query parameters for filters
- Document available filters

---

## Versioning

When you need breaking changes:

```
/v1/users     → Original
/v2/users     → Breaking change (new field required, removed field)
```

**Rules:**
- Additive changes (new optional field) → NO new version needed
- Breaking changes (removed field, changed type) → new version
- Support old versions for minimum 6 months
- Deprecation headers: `Deprecation: true`, `Sunset: 2026-06-01`

---

## Authentication

```
# Bearer token (most common)
Authorization: Bearer eyJhbGciOiJSUzI1NiIs...

# API key (simpler, less secure)
X-API-Key: sk_live_abc123
```

**Rules:**
- HTTPS always (tokens in plaintext over HTTP = game over)
- Short-lived tokens (15 min) + refresh tokens (7 days)
- Never put tokens in URLs (they end up in logs)

---

## Rate Limiting

Protect your API from abuse:

```
X-RateLimit-Limit: 1000         # Max requests per window
X-RateLimit-Remaining: 42       # Remaining in this window
X-RateLimit-Reset: 1708300800   # When window resets (Unix timestamp)
Retry-After: 60                 # Seconds to wait (on 429)
```

---

## OpenAPI / Swagger

Document your API with OpenAPI spec. It generates docs, client SDKs, and validation.

```yaml
openapi: 3.0.0
paths:
  /users/{id}:
    get:
      summary: Get a user by ID
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
      responses:
        '200':
          description: User found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '404':
          description: User not found
```

---

## Checklist

- [ ] Resources are nouns (plural), not verbs
- [ ] Status codes are meaningful and correct
- [ ] Consistent response envelope (data, error, meta)
- [ ] Collections are paginated
- [ ] Breaking changes are versioned
- [ ] Authentication is over HTTPS with bearer tokens
- [ ] Rate limiting is implemented with headers
- [ ] API is documented (OpenAPI/Swagger)

---

*← [Architecture](04-architecture.md) · [Observability →](06-observability.md)*
