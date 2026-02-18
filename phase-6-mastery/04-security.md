# Security

> Security is not a feature. It's a property of every feature.

---

## OWASP Top 10 — The Threats You Must Know

### 1. Injection (SQL, NoSQL, Command)
```python
# ❌ SQL Injection — user controls the query
query = f"SELECT * FROM users WHERE email = '{email}'"

# ✅ Parameterized query — database treats input as data, not code
cursor.execute("SELECT * FROM users WHERE email = %s", (email,))
```

```go
// ❌ Command injection
exec.Command("sh", "-c", "ping " + userInput)

// ✅ Pass arguments separately
exec.Command("ping", "-c", "4", userInput)
```

### 2. Broken Authentication
- Use bcrypt/argon2 for password hashing (never MD5/SHA)
- Short-lived JWTs (15 min) + refresh tokens (7 days)
- Rate limit login attempts
- Enforce MFA for sensitive operations

### 3. Sensitive Data Exposure
- HTTPS everywhere (TLS 1.3)
- Encrypt data at rest (database, backups)
- Don't log PII (emails, names, addresses)
- Don't return sensitive fields in APIs unless needed

### 4. Broken Access Control
```go
// ❌ Trusting client-provided user ID
func GetProfile(w http.ResponseWriter, r *http.Request) {
    userID := r.URL.Query().Get("user_id")  // User can change this!
    user := db.GetUser(userID)
}

// ✅ Use authenticated user's ID from token
func GetProfile(w http.ResponseWriter, r *http.Request) {
    userID := auth.GetUserID(r.Context())  // From verified JWT
    user := db.GetUser(userID)
}
```

### 5. Security Misconfiguration
- Don't expose stack traces in production
- Remove default credentials
- Disable directory listing
- Keep dependencies updated
- Disable unnecessary features/ports

### 6. Cross-Site Scripting (XSS)
```javascript
// ❌ Injecting user input into HTML
element.innerHTML = userInput;

// ✅ Use textContent (auto-escapes)
element.textContent = userInput;

// ✅ Or sanitize with DOMPurify
element.innerHTML = DOMPurify.sanitize(userInput);
```

---

## Authentication vs Authorization

```
Authentication: WHO are you? (login, JWT, API key)
Authorization:  WHAT can you do? (roles, permissions, policies)
```

### JWT Best Practices
```go
// Short-lived access token
claims := jwt.MapClaims{
    "sub": userID,
    "exp": time.Now().Add(15 * time.Minute).Unix(),
    "iat": time.Now().Unix(),
}

// Always verify:
// 1. Signature is valid
// 2. Token is not expired
// 3. Issuer is correct
// 4. Audience is correct
```

### RBAC (Role-Based Access Control)
```go
type Role string
const (
    RoleAdmin  Role = "admin"
    RoleEditor Role = "editor"
    RoleViewer Role = "viewer"
)

func RequireRole(required Role) Middleware {
    return func(next http.Handler) http.Handler {
        return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
            user := auth.GetUser(r.Context())
            if user.Role != required && user.Role != RoleAdmin {
                http.Error(w, "Forbidden", http.StatusForbidden)
                return
            }
            next.ServeHTTP(w, r)
        })
    }
}
```

---

## Secrets Management

```
❌ Hardcoded in code:     const API_KEY = "sk_live_abc123"
❌ In git:                .env committed to repo
❌ In Docker image:       ENV API_KEY=sk_live_abc123
❌ In logs:               logger.Info("calling API with key: " + apiKey)

✅ Environment variables: os.Getenv("API_KEY")
✅ Secret manager:        AWS Secrets Manager, Vault, 1Password
✅ Encrypted at rest:     KMS-encrypted environment variables
```

**If a secret is ever committed to git:** Rotate it immediately. Git history is forever.

```bash
# Scan for secrets
git log -p | grep -i "password\|secret\|api_key\|token"

# Use tools
# truffleHog, gitleaks, detect-secrets
```

---

## Dependency Security

```bash
# Go
go list -m -json all | nancy sleuth    # Scan for vulnerabilities
govulncheck ./...                       # Official Go vuln checker

# Node
npm audit
npm audit fix

# Python
pip-audit
safety check

# Rust
cargo audit
```

**Rules:**
- Update dependencies monthly minimum
- Enable Dependabot / Renovate for automated PRs
- Never ignore critical/high vulnerabilities

---

## Input Validation

**Validate everything at the boundary.** Trust nothing from the client.

```go
func validateCreateUser(req CreateUserRequest) error {
    if req.Email == "" {
        return ValidationError{Field: "email", Message: "required"}
    }
    if !isValidEmail(req.Email) {
        return ValidationError{Field: "email", Message: "invalid format"}
    }
    if len(req.Name) > 255 {
        return ValidationError{Field: "name", Message: "too long"}
    }
    if req.Age < 0 || req.Age > 150 {
        return ValidationError{Field: "age", Message: "invalid range"}
    }
    return nil
}
```

**Validate:**
- Type (is it a string? number? UUID?)
- Length/range (max length, min/max value)
- Format (email regex, UUID format)
- Business rules (positive amounts, valid chains)

---

## Threat Modeling (Simplified)

For every new feature, ask:

1. **What data does this feature handle?** (PII? financial? public?)
2. **Who should have access?** (authenticated users? admins only? public?)
3. **What could an attacker do?** (inject data? bypass auth? exfiltrate?)
4. **How do we prevent it?** (validation, auth, encryption, rate limiting)

---

## Checklist

- [ ] No SQL injection (parameterized queries everywhere)
- [ ] No hardcoded secrets (environment variables or secret manager)
- [ ] Authentication on all non-public endpoints
- [ ] Authorization checks (user can only access their own data)
- [ ] Input validation at every boundary
- [ ] HTTPS everywhere
- [ ] Dependencies scanned for vulnerabilities
- [ ] No PII in logs
- [ ] Rate limiting on authentication endpoints
- [ ] CORS properly configured

---

## Resources

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/)
- *Web Application Security* — Andrew Hoffman
- [CWE/SANS Top 25](https://cwe.mitre.org/top25/)

---

*← [Performance](03-performance.md) · [Supplementary Tracks →](../supplementary/apple-ios-track.md)*
