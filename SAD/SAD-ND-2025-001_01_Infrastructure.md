# SAD-ND-2025-001_01 — Infrastructure
# NutriDay System Architecture

---

| | |
|---|---|
| **Module** | 01 — Infrastructure |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](SAD-ND-2025-001_INDEX.md) |

---

## 1. AWS ARCHITECTURE OVERVIEW

```
┌─────────────────────────────────────────────────────────────┐
│                        AWS VPC (ap-southeast-1)              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │ Public Subnet                                         │   │
│  │  ┌─────────┐  ┌─────────┐  ┌──────────────────────┐ │   │
│  │  │  ALB    │  │ NAT GW  │  │ CloudFront (CDN)     │ │   │
│  │  └────┬────┘  └─────────┘  └──────────────────────┘ │   │
│  └───────┼──────────────────────────────────────────────┘   │
│  ┌───────┼──────────────────────────────────────────────┐   │
│  │ Private Subnet                                        │   │
│  │  ┌────┴────┐  ┌─────────────┐  ┌──────────────────┐ │   │
│  │  │ ECS     │  │ ElastiCache │  │ RDS MySQL 8.0    │ │   │
│  │  │ Fargate │  │ (Redis)     │  │ (Multi-AZ)       │ │   │
│  │  └─────────┘  └─────────────┘  └──────────────────┘ │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
         │                                      │
    ┌────┴────┐                          ┌──────┴──────┐
    │ Route53 │                          │ S3 Buckets  │
    └─────────┘                          └─────────────┘
```

---

## 2. COMPUTE — ECS FARGATE

| Tham số | Staging | Production |
|---|---|---|
| Task CPU | 0.5 vCPU | 1 vCPU |
| Task Memory | 1 GB | 2 GB |
| Min tasks | 1 | 2 |
| Max tasks | 2 | 10 |
| Scale-up threshold | CPU > 70% | CPU > 60% |
| Scale-down threshold | CPU < 30% | CPU < 25% |
| Health check path | `/health` | `/health` |
| Health check interval | 30s | 15s |
| Deregistration delay | 30s | 60s |

### Task Definition

```json
{
  "family": "nutriday-api",
  "networkMode": "awsvpc",
  "containerDefinitions": [{
    "name": "api",
    "image": "xxx.dkr.ecr.ap-southeast-1.amazonaws.com/nutriday-api:latest",
    "portMappings": [{ "containerPort": 3000 }],
    "environment": [
      { "name": "NODE_ENV", "value": "production" },
      { "name": "PORT", "value": "3000" }
    ],
    "secrets": [
      { "name": "DB_HOST", "valueFrom": "arn:aws:ssm:...:DB_HOST" },
      { "name": "DB_PASSWORD", "valueFrom": "arn:aws:secretsmanager:...:db-password" },
      { "name": "JWT_SECRET", "valueFrom": "arn:aws:secretsmanager:...:jwt-secret" },
      { "name": "REDIS_URL", "valueFrom": "arn:aws:ssm:...:REDIS_URL" }
    ],
    "logConfiguration": {
      "logDriver": "awslogs",
      "options": {
        "awslogs-group": "/ecs/nutriday-api",
        "awslogs-region": "ap-southeast-1",
        "awslogs-stream-prefix": "api"
      }
    }
  }]
}
```

---

## 3. DATABASE — RDS MySQL 8.0

| Tham số | Staging | Production |
|---|---|---|
| Instance class | db.t3.medium | db.r6g.large |
| Storage | 20 GB gp3 | 100 GB gp3 |
| Multi-AZ | No | Yes |
| Read replicas | 0 | 1 |
| Backup retention | 3 days | 14 days |
| Backup window | 03:00–04:00 UTC | 03:00–04:00 UTC |
| Maintenance window | Sun 04:00–05:00 | Sun 04:00–05:00 |
| Max connections | 150 | 500 |
| Charset | utf8mb4 | utf8mb4 |
| Collation | utf8mb4_unicode_ci | utf8mb4_unicode_ci |

### Connection Pool (TypeORM)

```typescript
// ormconfig.ts
{
  type: 'mysql',
  host: process.env.DB_HOST,
  port: 3306,
  database: 'nutriday',
  extra: {
    connectionLimit: process.env.NODE_ENV === 'production' ? 20 : 5,
    waitForConnections: true,
    queueLimit: 0,
  },
  logging: process.env.NODE_ENV !== 'production' ? ['query', 'error'] : ['error'],
}
```

---

## 4. CACHING — ELASTICACHE REDIS

| Tham số | Staging | Production |
|---|---|---|
| Node type | cache.t3.micro | cache.r6g.large |
| Nodes | 1 | 2 (primary + replica) |
| Max memory | 512 MB | 13 GB |
| Eviction policy | allkeys-lru | allkeys-lru |

### Cache Strategy

| Key Pattern | TTL | Mục đích |
|---|---|---|
| `dish:{id}` | 30 phút | Chi tiết món ăn |
| `dishes:list:{hash}` | 5 phút | Danh sách món (paginated) |
| `meal-plan:{userId}:{date}` | 10 phút | Meal plan hôm nay |
| `shopping:{listId}` | 5 phút | Shopping list |
| `user:{id}:profile` | 15 phút | User profile |
| `search:{hash}` | 3 phút | Kết quả tìm kiếm |
| `rate:{ip}:{endpoint}` | 1 phút | Rate limiting counter |

### Cache Invalidation

```typescript
// Khi dish được update:
await redis.del(`dish:${dishId}`);
await redis.keys('dishes:list:*').then(keys => keys.length && redis.del(...keys));

// Khi meal plan thay đổi:
await redis.del(`meal-plan:${userId}:${date}`);
await redis.del(`shopping:${listId}`);
```

---

## 5. STORAGE — S3

| Bucket | Mục đích | Access |
|---|---|---|
| `nutriday-dish-images` | Ảnh món ăn (original + thumbnails) | CloudFront |
| `nutriday-imports` | Excel import files (tạm) | Private |
| `nutriday-backups` | DB backups, archived data | Private |

### Image Processing Pipeline

```
Upload → S3 (original)
       → Lambda (Sharp)
         ├── thumbnail: 200×200 (WebP, 80%)
         ├── card: 400×300 (WebP, 85%)
         └── detail: 800×600 (WebP, 90%)
       → S3 (processed/) → CloudFront
```

### S3 Lifecycle Rules

| Rule | Điều kiện | Hành động |
|---|---|---|
| Import cleanup | 7 ngày | Delete từ `nutriday-imports/` |
| Archive transition | 90 ngày | `nutriday-backups/` → S3 Glacier |
| Glacier cleanup | 365 ngày | Delete từ Glacier |

---

## 6. CDN — CLOUDFRONT

| Tham số | Giá trị |
|---|---|
| Origins | S3 (`nutriday-dish-images`), ALB (`api`) |
| Price class | PriceClass_200 (Asia + NA + EU) |
| TTL (images) | 30 ngày |
| TTL (API) | Không cache (pass-through) |
| Compress | gzip, brotli |
| WAF | AWS WAF v2 (rate limiting, geo-blocking) |

---

## 7. NETWORKING

### VPC Layout

| Subnet | CIDR | AZ | Mục đích |
|---|---|---|---|
| Public-1a | 10.0.1.0/24 | ap-southeast-1a | ALB, NAT GW |
| Public-1b | 10.0.2.0/24 | ap-southeast-1b | ALB (multi-AZ) |
| Private-1a | 10.0.10.0/24 | ap-southeast-1a | ECS, RDS primary |
| Private-1b | 10.0.11.0/24 | ap-southeast-1b | ECS, RDS standby |

### Security Groups

| SG | Inbound | Source |
|---|---|---|
| sg-alb | 80, 443 | 0.0.0.0/0 |
| sg-ecs | 3000 | sg-alb |
| sg-rds | 3306 | sg-ecs |
| sg-redis | 6379 | sg-ecs |

---

## 8. EMAIL — SES

| Tham số | Giá trị |
|---|---|
| Region | ap-southeast-1 |
| From | noreply@nutriday.vn |
| Templates | OTP verification, Password reset, Welcome |
| Rate limit | 14/giây (production) |
| Bounce handling | SNS → Lambda → disable user |

---

**Trang trước:** [00 — Overview ←](SAD-ND-2025-001_00_Overview.md)
**Trang tiếp theo:** [02 — Backend Architecture →](SAD-ND-2025-001_02_Backend.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
