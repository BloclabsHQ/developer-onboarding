# Networking Fundamentals

> Every app you build talks to the network. Understand the plumbing.

---

## What Happens When You Type a URL

```
1. DNS Resolution     "api.example.com" → 93.184.216.34      (~50ms)
2. TCP Handshake      SYN → SYN-ACK → ACK (three-way)        (~30ms)
3. TLS Handshake      Certificate exchange, key agreement      (~50ms)
4. HTTP Request       GET /api/users HTTP/2                     (~1ms)
5. Server Processing  App logic, database queries               (~50ms)
6. HTTP Response      200 OK + JSON body                        (~1ms)
7. TCP delivers data  Packets flow back through the network    (~30ms)
```

Total: ~200ms for a single API call. Most of that is network overhead, not your code.

---

## The Network Stack

```
┌─────────────────────┐
│   Application       │  HTTP, WebSocket, gRPC, DNS
│   (Layer 7)         │  ← You work here
├─────────────────────┤
│   Transport         │  TCP (reliable), UDP (fast)
│   (Layer 4)         │  ← Ports live here
├─────────────────────┤
│   Network           │  IP addresses, routing
│   (Layer 3)         │  ← Packets find their destination
├─────────────────────┤
│   Link              │  Ethernet, Wi-Fi, physical cables
│   (Layer 2)         │  ← Actual data transmission
└─────────────────────┘
```

You mostly work at Layer 7 (HTTP). But understanding the stack explains why things are slow, why connections drop, and how firewalls work.

---

## DNS — The Internet's Phone Book

Translates human names to IP addresses.

```bash
# What IP is api.github.com?
dig api.github.com +short
# 140.82.113.5

nslookup api.github.com
```

**DNS Resolution Chain:**
```
Browser cache → OS cache → Router → ISP DNS → Root servers → TLD servers → Authoritative
```

**Why it matters:**
- DNS failures = your app can't find any server
- DNS propagation takes time (TTL) — changing DNS records isn't instant
- DNS-based load balancing distributes traffic across servers

---

## TCP vs UDP

| Feature | TCP | UDP |
|---------|-----|-----|
| Reliable | ✅ Guarantees delivery | ❌ Fire and forget |
| Ordered | ✅ Packets arrive in order | ❌ No ordering |
| Connection | ✅ Connection established first | ❌ No connection |
| Speed | Slower (overhead) | Faster (no overhead) |
| Use cases | HTTP, APIs, databases | Video streaming, gaming, DNS |

**Mental model:** TCP = certified mail (slow but guaranteed). UDP = shouting across a room (fast but might be missed).

Almost everything you build uses TCP (via HTTP).

---

## HTTP — The Language of the Web

### Request
```http
GET /api/users/123 HTTP/2
Host: api.example.com
Authorization: Bearer eyJ...
Accept: application/json
```

### Response
```http
HTTP/2 200 OK
Content-Type: application/json
Cache-Control: max-age=300

{"id": 123, "name": "Cris", "email": "cris@example.com"}
```

### Methods
| Method | Purpose | Idempotent | Safe |
|--------|---------|-----------|------|
| GET | Read data | ✅ | ✅ |
| POST | Create data | ❌ | ❌ |
| PUT | Replace data | ✅ | ❌ |
| PATCH | Update data partially | ❌ | ❌ |
| DELETE | Remove data | ✅ | ❌ |

**Idempotent:** Calling it 10 times has the same effect as calling it once.
**Safe:** Doesn't modify data.

### Status Codes
| Range | Meaning | Common Codes |
|-------|---------|-------------|
| 2xx | Success | 200 OK, 201 Created, 204 No Content |
| 3xx | Redirect | 301 Moved, 304 Not Modified |
| 4xx | Client error | 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 429 Too Many Requests |
| 5xx | Server error | 500 Internal Error, 502 Bad Gateway, 503 Service Unavailable |

---

## HTTPS / TLS

HTTPS = HTTP + TLS encryption. All production traffic should be HTTPS.

**TLS Handshake:**
1. Client: "Hello, I support these encryption methods"
2. Server: "Here's my certificate (proves I'm who I say I am)"
3. Client: Verifies certificate against trusted CAs
4. Both: Agree on encryption keys
5. All data is now encrypted

**Why it matters:** Without TLS, anyone on the network can read your API requests, including auth tokens, passwords, and data.

---

## WebSockets

HTTP is request-response. WebSockets are bidirectional — server can push data to client.

```
HTTP:       Client ──request──▶ Server ──response──▶ Client
            (every time)

WebSocket:  Client ◀──────────────────────────────▶ Server
            (persistent connection, either side can send)
```

**When to use:**
- Real-time data (live prices, chat, notifications)
- Server-sent events (when only server→client is needed, use SSE instead)

**When NOT to use:**
- Regular CRUD operations (use REST)
- Infrequent updates (polling every 30s is simpler)

---

## REST vs gRPC

| Feature | REST | gRPC |
|---------|------|------|
| Format | JSON (text) | Protocol Buffers (binary) |
| Transport | HTTP/1.1 or HTTP/2 | HTTP/2 only |
| Speed | Good | Faster (binary, streaming) |
| Browser support | ✅ Native | ⚠️ Needs proxy |
| Schema | OpenAPI (optional) | .proto files (required) |
| Streaming | ❌ (use WebSocket) | ✅ Built-in |
| Tooling | Universal | Growing |

**BlocLabs guidance:** REST for public APIs (browser compatibility). gRPC for internal service-to-service communication (performance).

---

## Headers That Matter

```http
# Authentication
Authorization: Bearer <token>

# Content negotiation
Content-Type: application/json
Accept: application/json

# Caching
Cache-Control: max-age=3600
ETag: "abc123"

# CORS (Cross-Origin Resource Sharing)
Access-Control-Allow-Origin: https://app.example.com
Access-Control-Allow-Methods: GET, POST

# Rate limiting
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 42
Retry-After: 60
```

---

## Latency Matters

```
Same machine (localhost)      →    0.1ms
Same datacenter               →    0.5ms
Same region (us-west)         →    1-5ms
Cross-country (US)            →    30-50ms
Cross-ocean (US → Europe)     →    80-120ms
Cross-ocean (US → Asia)       →    150-300ms
```

**Implications:**
- Put your database in the same region as your app
- Use CDNs for static assets (serve from edge, close to user)
- Batch API calls (1 call with 100 items > 100 calls with 1 item)
- Cache aggressively at every layer

---

## Tools

```bash
# Make HTTP requests
curl -X GET https://api.example.com/users \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json"

# Or use httpie (more readable)
http GET api.example.com/users Authorization:"Bearer TOKEN"

# Test connectivity
ping api.example.com

# Trace route
traceroute api.example.com

# DNS lookup
dig api.example.com

# Check if port is open
nc -zv api.example.com 443

# Watch network traffic
tcpdump -i en0 port 443
```

---

## Exercises

1. Use `curl -v https://api.github.com` — read the full output. Identify the TCP handshake, TLS handshake, HTTP request, and response.

2. Use `dig` to trace DNS resolution for a domain. Follow the chain from root to authoritative.

3. Build a simple HTTP client in Go/Python/JS that makes a GET request, handles errors, and respects timeouts.

4. Measure the latency difference between making 10 sequential API calls vs 10 concurrent ones.

5. Set up a simple WebSocket server and client. Send messages both directions.

---

## Recommended Reading

- [HTTP/2 Explained](https://daniel.haxx.se/http2/) — Daniel Stenberg (curl author)
- [High Performance Browser Networking](https://hpbn.co/) — Ilya Grigorik (free online)
- *Computer Networking: A Top-Down Approach* — Kurose & Ross

---

*← [Systems Thinking](03-systems-thinking.md) · [Databases & State →](05-databases-and-state.md)*
