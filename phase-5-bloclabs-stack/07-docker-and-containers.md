# Docker & Containers

> "Works on my machine" → "Works in the container" → Works everywhere.

---

## Why Containers

Containers package your app + its dependencies into a single, portable unit. Same binary runs on your Mac, in CI, and in production.

---

## Dockerfile Best Practices

### Multi-Stage Build (Go)
```dockerfile
# Stage 1: Build
FROM golang:1.22-alpine AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -o /api ./cmd/api

# Stage 2: Run (minimal image)
FROM alpine:3.19
RUN apk add --no-cache ca-certificates
COPY --from=builder /api /api
EXPOSE 8080
USER nobody
ENTRYPOINT ["/api"]
```

**Why multi-stage:**
- Build image: ~1GB (Go compiler, source code)
- Final image: ~15MB (binary + ca-certs)
- Smaller = faster deploys, smaller attack surface

### Node.js
```dockerfile
FROM node:22-alpine AS builder
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
RUN corepack enable && pnpm install --frozen-lockfile
COPY . .
RUN pnpm build

FROM node:22-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./
EXPOSE 3000
USER node
CMD ["node", "dist/index.js"]
```

### Rules
- **Pin versions** — `golang:1.22-alpine`, not `golang:latest`
- **Copy dependency files first** — cache layer for `go mod download` / `npm install`
- **Run as non-root** — `USER nobody` or `USER node`
- **`.dockerignore`** — exclude `node_modules`, `.git`, `.env`, test files

---

## Docker Compose (Local Development)

```yaml
# docker-compose.yml
services:
  api:
    build: .
    ports:
      - "8080:8080"
    environment:
      - DATABASE_URL=postgres://postgres:postgres@db:5432/myapp?sslmode=disable
      - REDIS_URL=redis://cache:6379
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 5s
      retries: 5

  cache:
    image: redis:7-alpine
    ports:
      - "6379:6379"

volumes:
  pgdata:
```

```bash
docker compose up -d        # Start all services
docker compose logs -f api  # Follow API logs
docker compose down         # Stop all
docker compose down -v      # Stop and delete volumes (fresh DB)
```

---

## Essential Commands

```bash
# Images
docker build -t myapp:latest .
docker images
docker rmi myapp:latest

# Containers
docker ps                    # Running containers
docker ps -a                 # All containers
docker logs <container>      # View logs
docker exec -it <container> sh  # Shell into container
docker stop <container>
docker rm <container>

# Cleanup
docker system prune          # Remove unused data
docker system prune -a       # Remove ALL unused (reclaim disk)
```

---

## Image Optimization

| Technique | Impact |
|-----------|--------|
| Multi-stage builds | 10-50x smaller images |
| Alpine base images | ~5MB vs ~100MB (Ubuntu) |
| `.dockerignore` | Faster builds, smaller context |
| Order layers by change frequency | Better cache hits |
| Pin versions | Reproducible builds |

---

## Checklist

- [ ] Multi-stage Dockerfile for all services
- [ ] Running as non-root user
- [ ] `.dockerignore` configured
- [ ] Docker Compose for local dev stack
- [ ] Base image versions pinned
- [ ] Health checks configured
- [ ] No secrets in Dockerfile or image

---

*← [AWS & Cloudflare](06-aws-and-cloudflare.md) · [Development Workflow →](08-workflow.md)*
