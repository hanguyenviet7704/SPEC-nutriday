# SAD-ND-2025-001_04 — Security Architecture
# NutriDay System Architecture

---

| | |
|---|---|
| **Module** | 04 — Security |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](SAD-ND-2025-001_INDEX.md) |

---

## 1. AUTHENTICATION FLOW

### 1.1 Mobile — JWT Flow

```
┌────────┐     POST /auth/login      ┌─────────┐     Verify      ┌──────┐
│ Mobile │ ──────────────────────────→│   API   │ ──────────────→ │  DB  │
│  App   │ ←────────────────────────── │ Server  │ ←────────────── │MySQL │
└───┬────┘  { accessToken,            └────┬────┘   bcrypt.compare └──────┘
    │         refreshToken }               │
    │                                      │
    │  Store tokens:                       │  Generate:
    │  - accessToken → memory (Zustand)    │  - accessToken (15min)
    │  - refreshToken → SecureStore        │  - refreshToken (30 days, DB)
    │                                      │
    │     401 Expired                      │
    │ ──── POST /auth/refresh ───────────→ │
    │ ←─── new accessToken ─────────────── │
    │                                      │
    │     refreshToken expired             │
    │ ──── Force logout ─────────────────→ │
    │     Navigate → LoginScreen           │
```

### 1.2 Admin Web — JWT Flow

```
Tương tự mobile, nhưng:
- accessToken: localStorage (httpOnly cookie trong tương lai)
- refreshToken: httpOnly secure cookie
- Thêm CSRF token cho mutations
```

### 1.3 Token Specifications

| Token | Algorithm | Expiry | Payload |
|---|---|---|---|
| Access Token | HS256 | 15 phút | `{ sub: userId, role: 'user', iat, exp }` |
| Refresh Token | random UUID | 30 ngày | Stored in DB (`refresh_tokens` table) |
| Admin Access | HS256 | 1 giờ | `{ sub: adminId, role: adminRole, iat, exp }` |
| Admin Refresh | random UUID | 7 ngày | Stored in DB |
| OTP Code | 6-digit random | 10 phút | Stored in DB (hashed) |
| Password Reset | random UUID | 2 giờ | Stored in DB |

---

## 2. AUTHORIZATION — RBAC

### 2.1 Admin Roles

| Role | Quyền |
|---|---|
| `super_admin` | Full access: CRUD dishes, users, reports, system settings |
| `content_editor` | Create/edit dishes, import Excel, view reports |
| `nutritionist` | Review dishes (approve/reject), view nutrition reports |

### 2.2 Role Guard Implementation

```typescript
// common/guards/roles.guard.ts
@Injectable()
export class RolesGuard implements CanActivate {
  canActivate(context: ExecutionContext): boolean {
    const requiredRoles = this.reflector.get<AdminRole[]>('roles', context.getHandler());
    if (!requiredRoles) return true;
    const { admin } = context.switchToHttp().getRequest();
    return requiredRoles.includes(admin.role);
  }
}

// Usage:
@Roles('super_admin', 'content_editor')
@Post('/admin/dishes')
createDish(@Body() dto: CreateDishDto) { ... }
```

### 2.3 Resource Ownership

```typescript
// Meal plans, shopping lists, favorites — user chỉ access được resource của mình
// Middleware check: resource.userId === req.user.id
// Return 403 nếu không phải owner
```

---

## 3. DATA PROTECTION

### 3.1 Password Security

| Tham số | Giá trị |
|---|---|
| Algorithm | bcrypt |
| Salt rounds | 12 |
| Min length | 8 ký tự |
| Requirements | 1 uppercase + 1 digit + 1 special char |
| Breach check | Không (Phase 1) |

### 3.2 Sensitive Data Handling

| Dữ liệu | Storage | Encryption |
|---|---|---|
| Password | MySQL | bcrypt hash (one-way) |
| Refresh token | MySQL | Plain (UUID, short-lived) |
| OTP code | MySQL | SHA-256 hash |
| Access token | Memory (client) | JWT signed (HS256) |
| User email | MySQL | Plain (indexed for lookup) |
| Health profile | MySQL | Plain (Phase 1), AES-256 (Phase 2) |

### 3.3 Data in Transit

| Channel | Protection |
|---|---|
| Client ↔ ALB | TLS 1.2+ (ACM certificate) |
| ALB ↔ ECS | TLS 1.2 (internal) |
| ECS ↔ RDS | TLS (RDS CA) |
| ECS ↔ Redis | TLS (ElastiCache in-transit encryption) |

---

## 4. RATE LIMITING

```typescript
// NestJS ThrottlerModule
ThrottlerModule.forRoot([
  { name: 'short', ttl: 1000, limit: 10 },     // 10 req/sec
  { name: 'medium', ttl: 60000, limit: 100 },   // 100 req/min
  { name: 'long', ttl: 3600000, limit: 1000 },  // 1000 req/hour
]),

// Per-endpoint overrides:
// POST /auth/login      → 5 req/min (brute force protection)
// POST /auth/register   → 3 req/min
// POST /meal-plans/gen  → 10 req/min
// GET  /dishes          → 60 req/min
```

---

## 5. INPUT VALIDATION & SANITIZATION

| Layer | Kiểm tra |
|---|---|
| DTO (class-validator) | Type, format, length, regex |
| Global pipe | whitelist: true, forbidNonWhitelisted: true |
| SQL | TypeORM parameterized queries (no raw SQL injection) |
| XSS | Sanitize HTML trong recipe steps (DOMPurify server-side) |
| File upload | MIME type check, max 5MB, chỉ xlsx/jpg/png/webp |

---

## 6. SECURITY HEADERS

```typescript
// main.ts — Helmet middleware
app.use(helmet({
  contentSecurityPolicy: false,  // Handled by CloudFront
  crossOriginEmbedderPolicy: false,
}));

// CORS config
app.enableCors({
  origin: [
    'https://admin.nutriday.vn',
    /\.nutriday\.vn$/,
  ],
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization'],
});
```

---

## 7. AUDIT LOGGING

```sql
-- Tất cả admin actions được log:
INSERT INTO audit_logs (admin_user_id, action, entity_type, entity_id, changes, ip_address)
VALUES (?, ?, ?, ?, ?, ?);

-- Actions tracked: create_dish, update_dish, delete_dish,
-- approve_dish, reject_dish, import_dishes, update_user_status
```

---

**Trang trước:** [03 — Frontend Architecture ←](SAD-ND-2025-001_03_Frontend.md)
**Trang tiếp theo:** [05 — DevOps →](SAD-ND-2025-001_05_DevOps.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
