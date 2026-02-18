# Infrastructure Track

> For developers moving into DevOps, SRE, or infrastructure roles.

---

## Overview

Infrastructure at BlocLabs runs primarily on AWS with Cloudflare for edge services. This track goes deeper than Phase 5's overview.

---

## Infrastructure as Code (IaC)

### Terraform
Define infrastructure in code. Version controlled, reviewable, reproducible.

```hcl
resource "aws_ecs_service" "api" {
  name            = "api"
  cluster         = aws_ecs_cluster.main.id
  task_definition = aws_ecs_task_definition.api.arn
  desired_count   = 3

  load_balancer {
    target_group_arn = aws_lb_target_group.api.arn
    container_name   = "api"
    container_port   = 8080
  }
}
```

```bash
terraform init      # Initialize
terraform plan      # Preview changes
terraform apply     # Apply changes
terraform destroy   # Tear down (careful!)
```

**Rules:**
- Never make manual changes to infrastructure
- All changes go through Terraform → PR → review → apply
- State file is sacred — store in S3 with DynamoDB locking

---

## CI/CD Deep Dive

### GitHub Actions
```yaml
name: Deploy
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Build Docker image
        run: docker build -t $ECR_REPO:$GITHUB_SHA .
      - name: Push to ECR
        run: docker push $ECR_REPO:$GITHUB_SHA
      - name: Update ECS service
        run: |
          aws ecs update-service \
            --cluster production \
            --service api \
            --force-new-deployment
```

### Deployment Strategies
| Strategy | Risk | Rollback Speed | Complexity |
|----------|------|---------------|------------|
| Rolling update | Low | Slow | Low |
| Blue/Green | Lowest | Instant | Medium |
| Canary | Lowest | Fast | High |

---

## Monitoring & Alerting

### Prometheus + Grafana
```yaml
# prometheus.yml
scrape_configs:
  - job_name: 'api'
    static_configs:
      - targets: ['api:8080']
    metrics_path: '/metrics'
```

### Key Dashboards
- **Service Health:** Request rate, error rate, latency (p50/p95/p99)
- **Infrastructure:** CPU, memory, disk, network per service
- **Database:** Query performance, connection pool, replication lag
- **Business:** Transactions indexed, API calls served, active users

### Alert Hierarchy
```
Page (wake someone up):
  - Service completely down
  - Data loss risk
  - Security breach

Urgent (fix within hours):
  - Error rate > 5%
  - Latency p99 > 5s
  - Disk > 90%

Warning (fix within days):
  - Error rate > 1%
  - Memory trending up
  - Certificate expiring < 30 days
```

---

## Incident Management

### Severity Levels
| Level | Definition | Response Time |
|-------|-----------|--------------|
| SEV1 | Complete outage, data loss risk | Immediate |
| SEV2 | Major degradation, revenue impact | < 1 hour |
| SEV3 | Minor issue, workaround exists | < 4 hours |
| SEV4 | Cosmetic, no user impact | Next sprint |

### Incident Response Process
1. **Detect** — monitoring alerts or user report
2. **Triage** — assess severity, assign incident commander
3. **Communicate** — update status page, notify stakeholders
4. **Mitigate** — stop the bleeding (rollback, restart, scale up)
5. **Resolve** — fix the root cause
6. **Post-mortem** — blameless review, action items (within 48 hours)

---

## Backup & Recovery

| Data | Backup Strategy | RPO | RTO |
|------|----------------|-----|-----|
| PostgreSQL | Automated daily + WAL archiving | 1 hour | 1 hour |
| S3 objects | Cross-region replication | 0 (real-time) | Minutes |
| Configuration | Git (Terraform) | 0 (committed) | Minutes |
| Secrets | 1Password + AWS Secrets Manager | Manual | Minutes |

**RPO (Recovery Point Objective):** Maximum acceptable data loss (time).
**RTO (Recovery Time Objective):** Maximum acceptable downtime.

**Test your backups.** Backups that haven't been tested are not backups.

---

## Networking

### VPC Design
```
VPC (10.0.0.0/16)
├── Public Subnet (10.0.1.0/24)
│   └── Load Balancer, NAT Gateway
├── Private Subnet (10.0.2.0/24)
│   └── Application servers (ECS)
└── Data Subnet (10.0.3.0/24)
    └── RDS, ElastiCache (no internet access)
```

### Security Groups
- **Load Balancer:** Allow 443 from anywhere
- **App servers:** Allow 8080 from load balancer only
- **Database:** Allow 5432 from app servers only
- **Default:** Deny everything

---

## Resources

- [Terraform Documentation](https://developer.hashicorp.com/terraform/docs)
- [AWS Well-Architected Labs](https://www.wellarchitectedlabs.com/)
- *Site Reliability Engineering* — Google (free online: sre.google/books)
- *The Phoenix Project* — Gene Kim (DevOps told as a novel)
- [Prometheus Documentation](https://prometheus.io/docs/)

---

*Part of the [BlocLabs Developer Onboarding Program](../README.md)*
