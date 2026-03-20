# SAD-ND-2025-001_00 — Tổng Quan Kiến Trúc
# NutriDay System Architecture Document

---

| | |
|---|---|
| **Module** | 00 — Architecture Overview |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](SAD-ND-2025-001_INDEX.md) |

---

## 1. KIẾN TRÚC TỔNG QUAN

### 1.1 C4 Model — Context Level

```
                    ┌─────────────────────────────────────┐
                    │           NutriDay System            │
                    │                                     │
     Mobile User ──►│  React Native App  ──►  NestJS API  │◄── Admin User
     (iOS/Android)  │                         (Backend)   │    (Web Browser)
                    │                            │        │
                    │                     ┌──────┴──────┐ │
                    │                     │  MySQL RDS  │ │
                    │                     │  S3 (files) │ │
                    │                     │  ElastiCache│ │
                    └─────────────────────────────────────┘
                                  │
                          ┌───────┴────────┐
                          │  External      │
                          │  SMS Gateway   │
                          │  (OTP - ESMS)  │
                          └────────────────┘
```

### 1.2 C4 Model — Container Level

```
┌─────────────────────────────────────────────────────────────────┐
│                        AWS Cloud                                │
│                                                                 │
│  ┌──────────────┐   HTTPS    ┌──────────────────────────────┐  │
│  │ CloudFront   │◄──────────►│  Application Load Balancer   │  │
│  │ (CDN/Static) │            └──────────────┬───────────────┘  │
│  └──────────────┘                           │                  │
│                                    ┌────────┴────────┐         │
│  ┌──────────────┐                  │  ECS Fargate     │         │
│  │  S3 Bucket   │◄── Media files ─►│  NestJS API      │         │
│  │  (Images)    │                  │  (Container)     │         │
│  └──────────────┘                  └────────┬────────┘         │
│                                             │                  │
│  ┌──────────────────────┐     ┌─────────────┴──────────┐       │
│  │ ElastiCache (Redis)  │◄────│  AWS RDS MySQL 8.0     │       │
│  │ (Session/Cache)      │     │  Multi-AZ Primary       │       │
│  └──────────────────────┘     │  + 1 Read Replica       │       │
│                               └────────────────────────┘       │
└─────────────────────────────────────────────────────────────────┘
```

---

## 2. TECHNOLOGY DECISIONS

### 2.1 Backend: NestJS

| Tiêu chí | Lý do chọn NestJS |
|---|---|
| TypeScript native | Tránh lỗi runtime, tăng developer experience |
| Module system | Dễ tổ chức code theo domain (auth, dish, meal-plan...) |
| Decorator-based | Guard, Interceptor, Pipe sẵn có cho auth, validation |
| Built-in DI | Dependency injection giúp test dễ hơn |
| OpenAPI support | Auto-generate Swagger docs từ decorators |

### 2.2 Database: MySQL 8.0

| Tiêu chí | Lý do |
|---|---|
| ACID compliance | Đảm bảo consistency cho meal_plans và shopping_lists |
| JSON column | Lưu audit_logs flexible |
| Full-text search | Tìm kiếm tên món nhanh không cần Elasticsearch |
| Window functions | Báo cáo analytics (v8.0+) |
| AWS RDS | Managed service, automated backup, Multi-AZ |

### 2.3 Mobile: React Native (Expo)

| Tiêu chí | Lý do |
|---|---|
| Cross-platform | 1 codebase cho iOS + Android |
| Expo SDK | OTA updates, managed build pipeline |
| TypeScript | Type safety với API responses |
| React Native Reanimated v3 | 60fps animations chạy trên JS thread |

### 2.4 Cache: Redis (ElastiCache)

| Use case | TTL | Key pattern |
|---|---|---|
| Dish list by dietGoal | 5 phút | `dishes:goal:{dietGoal}:page:{n}` |
| Dish detail | 10 phút | `dish:{dishId}` |
| Search results | 2 phút | `search:{hash(query+filters)}` |
| User session data | 15 phút | `user:session:{userId}` |
| Rate limit counters | 1 phút | `ratelimit:{ip}:{endpoint}` |

---

## 3. API ARCHITECTURE

### 3.1 Request Flow

```
Client (Mobile/Admin)
        │ HTTPS
        ▼
CloudFront (WAF + CDN)
        │
        ▼
Application Load Balancer
        │ Round-robin
   ┌────┴────┐
   │         │
ECS Task 1  ECS Task 2   (Auto-scaling: 2-10 tasks)
   │
   ├── Guards (JWT auth, Role check)
   ├── Interceptors (Logging, Transform response)
   ├── Pipes (Validation via class-validator)
   ├── Controllers (Route handlers)
   ├── Services (Business logic)
   └── Repositories (TypeORM → MySQL / Redis)
```

### 3.2 NestJS Module Structure

```
src/
├── app.module.ts
├── common/
│   ├── guards/          ← JwtAuthGuard, RolesGuard
│   ├── decorators/      ← @CurrentUser(), @Roles()
│   ├── interceptors/    ← TransformResponseInterceptor, LoggingInterceptor
│   ├── pipes/           ← ValidationPipe (global)
│   ├── filters/         ← HttpExceptionFilter (global)
│   └── dto/             ← Shared DTOs (PaginationDto, etc.)
│
├── config/              ← ConfigModule (env vars)
├── database/            ← TypeORM connection, migrations
├── cache/               ← Redis CacheModule
│
└── modules/
    ├── auth/            ← JWT, OTP, refresh tokens
    ├── users/           ← Profile, onboarding, health
    ├── dishes/          ← Dish CRUD, search, substitutions
    ├── ingredients/     ← Ingredient management
    ├── meal-plans/      ← Generate, swap, randomize
    ├── shopping-lists/  ← Generate, check-off, export
    ├── search/          ← Full-text, autocomplete, explore
    ├── favorites/       ← User favorites
    ├── notifications/   ← Push notifications, settings
    └── admin/           ← Admin-only CRUD, review, reports
```

---

## 4. SCALABILITY

### 4.1 Horizontal Scaling

- **ECS Auto Scaling:** Min 2, Max 10 tasks
- **Scale-out trigger:** CPU > 70% OR memory > 80%
- **Stateless API:** Không lưu state trên server → scale dễ
- **Session:** JWT (stateless) + Redis (blacklist revoked tokens)

### 4.2 Database Scaling

- **Read Replica:** Route reporting queries → Read Replica
- **Connection Pooling:** TypeORM pool size 10 per instance
- **Slow query monitoring:** `long_query_time = 1s`

### 4.3 Estimated Capacity (12 tháng)

| Metric | Value |
|---|---|
| Users | 50,000 active |
| Daily meal plans generated | ~50,000/ngày |
| Peak RPS | ~300 RPS |
| Database size | ~50GB |
| S3 storage (images) | ~20GB |

---

**Trang tiếp theo:** [01 — Infrastructure →](SAD-ND-2025-001_01_Infrastructure.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
