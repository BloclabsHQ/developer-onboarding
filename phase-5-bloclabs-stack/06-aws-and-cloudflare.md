# AWS & Cloudflare

> AWS for critical infrastructure. Cloudflare for edge and non-critical. Choose the right tool.

---

## AWS — Our Services

### Compute
| Service | Use Case |
|---------|----------|
| **ECS (Fargate)** | Container orchestration — our primary deployment target |
| **Lambda** | Event-driven functions (webhooks, scheduled tasks) |
| **EC2** | Raw VMs (rare — prefer containers) |

### Database
| Service | Use Case |
|---------|----------|
| **RDS (PostgreSQL)** | Primary relational database |
| **ElastiCache (Redis)** | Caching, session storage, rate limiting |
| **DynamoDB** | High-throughput key-value (when PostgreSQL isn't enough) |

### Storage
| Service | Use Case |
|---------|----------|
| **S3** | Object storage (files, backups, static assets) |
| **ECR** | Docker image registry |

### Messaging
| Service | Use Case |
|---------|----------|
| **SQS** | Message queues (decoupling services) |
| **SNS** | Pub/sub notifications |
| **EventBridge** | Event routing between services |

### Networking & Security
| Service | Use Case |
|---------|----------|
| **CloudFront** | CDN (static asset delivery) |
| **Route 53** | DNS management |
| **ACM** | SSL/TLS certificates (free) |
| **IAM** | Access control (users, roles, policies) |
| **Secrets Manager** | Secrets storage (API keys, DB passwords) |
| **VPC** | Network isolation |

---

## Cloudflare — Our Services

| Service | Use Case |
|---------|----------|
| **Workers** | Edge compute (API proxies, redirects, A/B testing) |
| **R2** | S3-compatible storage (no egress fees) |
| **D1** | Edge SQLite database |
| **DNS** | Domain management, DDoS protection |
| **CDN** | Asset caching and delivery |
| **Tunnel** | Secure access to internal services |

---

## When AWS vs Cloudflare

| Need | Choose | Why |
|------|--------|-----|
| Production database | AWS (RDS) | Managed PostgreSQL, backups, failover |
| Container workloads | AWS (ECS) | Mature orchestration |
| Static assets / CDN | Cloudflare | Free tier generous, no egress fees |
| Edge compute | Cloudflare Workers | Faster cold start, cheaper |
| Message queues | AWS (SQS) | Mature, reliable, integrates with everything |
| DNS | Cloudflare | Free, fast, DDoS protection included |
| Object storage | Cloudflare R2 | S3-compatible, zero egress fees |
| Secrets | AWS Secrets Manager | Integrates with IAM, rotation |

---

## Cost Awareness

AWS bills add up fast. Key cost drivers:

| Resource | Cost Driver | How to Control |
|----------|------------|----------------|
| EC2/ECS | Running time | Right-size, use Fargate Spot |
| RDS | Instance size + storage | Right-size, reserved instances |
| Data transfer | Egress (outbound) | Use CloudFront/R2, minimize cross-region |
| NAT Gateway | Per GB processed | Minimize, use VPC endpoints |
| Lambda | Invocations + duration | Optimize cold starts, right-size memory |

**Rule:** Tag everything. Review costs weekly. Set billing alerts.

```bash
# Check current costs
aws ce get-cost-and-usage \
    --time-period Start=2026-02-01,End=2026-02-28 \
    --granularity MONTHLY \
    --metrics BlendedCost
```

---

## AWS CLI Essentials

```bash
# Configure
aws configure

# S3
aws s3 ls
aws s3 cp file.txt s3://my-bucket/
aws s3 sync ./dist s3://my-bucket/static/

# ECS
aws ecs list-services --cluster production
aws ecs describe-services --cluster production --services api

# Logs
aws logs tail /ecs/api --follow

# Secrets
aws secretsmanager get-secret-value --secret-id production/db-password
```

---

## IAM — Least Privilege

Every service, every developer, every CI pipeline gets only the permissions it needs.

```json
{
    "Effect": "Allow",
    "Action": [
        "s3:GetObject",
        "s3:PutObject"
    ],
    "Resource": "arn:aws:s3:::my-bucket/uploads/*"
}
```

**Never use root credentials. Never use `*` for resources. Never embed credentials in code.**

---

*← [PostgreSQL](05-postgresql.md) · [Docker & Containers →](07-docker-and-containers.md)*
