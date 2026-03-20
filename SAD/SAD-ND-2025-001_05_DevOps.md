# SAD-ND-2025-001_05 — DevOps & CI/CD
# NutriDay System Architecture

---

| | |
|---|---|
| **Module** | 05 — DevOps & CI/CD |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](SAD-ND-2025-001_INDEX.md) |

---

## 1. CI/CD PIPELINE

### 1.1 GitHub Actions — Backend

```yaml
# .github/workflows/backend.yml
name: Backend CI/CD
on:
  push:
    branches: [main, develop]
    paths: ['backend/**']
  pull_request:
    branches: [main]
    paths: ['backend/**']

jobs:
  test:
    runs-on: ubuntu-latest
    services:
      mysql:
        image: mysql:8.0
        env:
          MYSQL_ROOT_PASSWORD: test
          MYSQL_DATABASE: nutriday_test
        ports: ['3306:3306']
      redis:
        image: redis:7-alpine
        ports: ['6379:6379']
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 20 }
      - run: cd backend && npm ci
      - run: cd backend && npm run lint
      - run: cd backend && npm run test -- --coverage
      - run: cd backend && npm run test:e2e
      - uses: codecov/codecov-action@v3

  build-deploy:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: aws-actions/configure-aws-credentials@v4
      - uses: aws-actions/amazon-ecr-login@v2
      - run: |
          docker build -t nutriday-api backend/
          docker tag nutriday-api:latest $ECR_REGISTRY/nutriday-api:$GITHUB_SHA
          docker push $ECR_REGISTRY/nutriday-api:$GITHUB_SHA
      - run: |
          aws ecs update-service \
            --cluster nutriday-prod \
            --service nutriday-api \
            --force-new-deployment
```

### 1.2 GitHub Actions — Mobile

```yaml
# .github/workflows/mobile.yml
name: Mobile CI
on:
  push:
    branches: [main, develop]
    paths: ['mobile/**']

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: cd mobile && npm ci
      - run: cd mobile && npm run lint
      - run: cd mobile && npm run test -- --coverage

  build-android:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: expo/expo-github-action@v8
        with: { eas-version: latest, token: ${{ secrets.EXPO_TOKEN }} }
      - run: cd mobile && eas build --platform android --profile production --non-interactive

  build-ios:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: macos-latest
    steps:
      - uses: actions/checkout@v4
      - uses: expo/expo-github-action@v8
        with: { eas-version: latest, token: ${{ secrets.EXPO_TOKEN }} }
      - run: cd mobile && eas build --platform ios --profile production --non-interactive
```

### 1.3 GitHub Actions — Admin Web

```yaml
# .github/workflows/admin.yml
name: Admin Web CI/CD
on:
  push:
    branches: [main]
    paths: ['admin/**']

jobs:
  test-build-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: cd admin && npm ci
      - run: cd admin && npm run lint
      - run: cd admin && npm run test
      - run: cd admin && npm run build
      - uses: aws-actions/configure-aws-credentials@v4
      - run: aws s3 sync admin/dist/ s3://nutriday-admin-web/ --delete
      - run: aws cloudfront create-invalidation --distribution-id $CF_DIST_ID --paths "/*"
```

---

## 2. ENVIRONMENTS

| Môi trường | Branch | URL | Mục đích |
|---|---|---|---|
| Development | `develop` | `dev-api.nutriday.vn` | Dev testing |
| Staging | `release/*` | `staging-api.nutriday.vn` | QA, UAT |
| Production | `main` | `api.nutriday.vn` | Live |

### Environment Variables

```bash
# Managed via AWS SSM Parameter Store + Secrets Manager
# Backend .env template:
NODE_ENV=production
PORT=3000
DB_HOST=/nutriday/prod/db-host          # SSM
DB_PORT=3306
DB_NAME=nutriday
DB_USERNAME=/nutriday/prod/db-user      # SSM
DB_PASSWORD=arn:aws:secretsmanager:...  # Secrets Manager
REDIS_URL=/nutriday/prod/redis-url      # SSM
JWT_SECRET=arn:aws:secretsmanager:...   # Secrets Manager
JWT_EXPIRES_IN=15m
AWS_S3_BUCKET=nutriday-dish-images
AWS_SES_FROM=noreply@nutriday.vn
```

---

## 3. MONITORING & OBSERVABILITY

### 3.1 Stack

| Tool | Mục đích |
|---|---|
| CloudWatch Logs | Application logs (JSON structured) |
| CloudWatch Metrics | CPU, memory, request count, latency |
| CloudWatch Alarms | Alert khi threshold vượt |
| X-Ray | Distributed tracing (API → DB → Redis) |
| Sentry | Error tracking (frontend + backend) |

### 3.2 Alerts

| Metric | Threshold | Severity | Action |
|---|---|---|---|
| API Error Rate (5xx) | > 5% trong 5 phút | Critical | PagerDuty → on-call |
| API Latency p99 | > 3s trong 5 phút | Warning | Slack #alerts |
| CPU Usage | > 80% trong 10 phút | Warning | Auto-scale + Slack |
| DB Connections | > 80% max | Critical | PagerDuty |
| Disk Usage (RDS) | > 85% | Warning | Slack |
| Failed Login Rate | > 50/min | Warning | Slack #security |

### 3.3 Health Check

```typescript
// GET /health
{
  "status": "ok",
  "uptime": 86400,
  "checks": {
    "database": "ok",
    "redis": "ok",
    "s3": "ok"
  },
  "version": "1.0.0",
  "timestamp": "2025-06-01T10:00:00.000Z"
}
```

---

## 4. DATABASE OPERATIONS

### 4.1 Migration Strategy

```bash
# CI/CD pipeline chạy migration trước khi deploy:
npm run migration:run

# Rollback nếu cần:
npm run migration:revert

# Zero-downtime migration rules:
# 1. Chỉ ADD column (nullable hoặc có default)
# 2. KHÔNG DROP column trong cùng release
# 3. Rename = add new → migrate data → drop old (2 releases)
```

### 4.2 Backup Strategy

| Type | Frequency | Retention | Storage |
|---|---|---|---|
| RDS Automated | Daily | 14 days | RDS snapshots |
| RDS Manual | Before migration | 30 days | RDS snapshots |
| S3 Cross-Region | Daily | 90 days | S3 ap-northeast-1 |

---

## 5. DOCKER

### Backend Dockerfile

```dockerfile
# backend/Dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build

FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./
EXPOSE 3000
CMD ["node", "dist/main.js"]
```

### Docker Compose (Local Dev)

```yaml
# docker-compose.yml
version: '3.8'
services:
  api:
    build: ./backend
    ports: ['3000:3000']
    env_file: ./backend/.env
    depends_on: [mysql, redis]

  mysql:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: nutriday
    ports: ['3306:3306']
    volumes: ['mysql_data:/var/lib/mysql']

  redis:
    image: redis:7-alpine
    ports: ['6379:6379']

volumes:
  mysql_data:
```

---

**Trang trước:** [04 — Security ←](SAD-ND-2025-001_04_Security.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
