# TSD-ND-2025-001_07 — Security Tests
# NutriDay Test Design

---

| | |
|---|---|
| **Module** | 07 — Security Tests |
| **Tool** | OWASP ZAP + Manual Testing |
| **Index** | [← Quay lại INDEX](TSD-ND-2025-001_INDEX.md) |

---

## 1. OWASP TOP 10 TEST MATRIX

| # | Vulnerability | Test Method | Priority |
|---|---|---|---|
| A01 | Broken Access Control | Manual + Automated | P0 |
| A02 | Cryptographic Failures | Manual review | P0 |
| A03 | Injection (SQL, NoSQL) | ZAP + Manual | P0 |
| A04 | Insecure Design | Architecture review | P1 |
| A05 | Security Misconfiguration | ZAP scan | P1 |
| A06 | Vulnerable Components | npm audit | P1 |
| A07 | Auth Failures | Manual | P0 |
| A08 | Data Integrity Failures | Manual | P1 |
| A09 | Logging Failures | Manual review | P2 |
| A10 | SSRF | Manual | P1 |

---

## 2. AUTHENTICATION TESTS

```typescript
// SEC-AUTH-001: Brute force protection
describe('Brute Force Protection', () => {
  it('Lock account after 5 failed login attempts', async () => {
    for (let i = 0; i < 5; i++) {
      await request(app).post('/v1/auth/login')
        .send({ emailOrPhone: 'target@test.vn', password: 'wrong' });
    }
    const res = await request(app).post('/v1/auth/login')
      .send({ emailOrPhone: 'target@test.vn', password: 'correct' });
    expect(res.status).toBe(429); // Rate limited
  });
});

// SEC-AUTH-002: Token expiration
describe('Token Expiration', () => {
  it('Expired access token returns 401', async () => {
    const expiredToken = jwt.sign({ sub: 'user_001' }, SECRET, { expiresIn: '-1h' });
    const res = await request(app).get('/v1/users/me')
      .set('Authorization', `Bearer ${expiredToken}`);
    expect(res.status).toBe(401);
  });
});

// SEC-AUTH-003: Token reuse after logout
describe('Token Reuse', () => {
  it('Revoked refresh token cannot be used', async () => {
    const loginRes = await loginAs(testUser);
    await request(app).post('/v1/auth/logout')
      .set('Authorization', `Bearer ${loginRes.accessToken}`);
    const refreshRes = await request(app).post('/v1/auth/refresh')
      .send({ refreshToken: loginRes.refreshToken });
    expect(refreshRes.status).toBe(401);
  });
});

// SEC-AUTH-004: JWT algorithm confusion
// SEC-AUTH-005: Password complexity enforcement
// SEC-AUTH-006: OTP replay attack prevention
```

---

## 3. AUTHORIZATION TESTS

```typescript
// SEC-AUTHZ-001: Horizontal privilege escalation
describe('Resource Ownership', () => {
  it('User A cannot access User B meal plan', async () => {
    const tokenA = await loginAs(userA);
    const tokenB = await loginAs(userB);
    // Create plan as User B
    const plan = await createPlan(tokenB);
    // User A tries to access
    const res = await request(app).get(`/v1/meal-plans/${plan.id}`)
      .set('Authorization', `Bearer ${tokenA}`);
    expect(res.status).toBe(403);
  });
});

// SEC-AUTHZ-002: Vertical privilege escalation
describe('Admin Role Enforcement', () => {
  it('Nutritionist cannot create dishes', async () => {
    const token = await loginAsAdmin('nutritionist');
    const res = await request(app).post('/v1/admin/dishes')
      .set('Authorization', `Bearer ${token}`)
      .send(validDishPayload);
    expect(res.status).toBe(403);
  });

  it('Content editor cannot manage users', async () => {
    const token = await loginAsAdmin('content_editor');
    const res = await request(app).get('/v1/admin/users')
      .set('Authorization', `Bearer ${token}`);
    expect(res.status).toBe(403);
  });
});

// SEC-AUTHZ-003: IDOR (Insecure Direct Object Reference)
// SEC-AUTHZ-004: Admin endpoint access with user token
```

---

## 4. INJECTION TESTS

```typescript
// SEC-INJ-001: SQL Injection
describe('SQL Injection', () => {
  const payloads = [
    "'; DROP TABLE users; --",
    "1 OR 1=1",
    "1 UNION SELECT * FROM admin_users",
    "' OR ''='",
  ];

  payloads.forEach(payload => {
    it(`Blocked: ${payload.substring(0, 30)}...`, async () => {
      const res = await request(app).get(`/v1/dishes?search=${encodeURIComponent(payload)}`)
        .set('Authorization', `Bearer ${token}`);
      // Should not return all dishes or cause error
      expect(res.status).not.toBe(500);
    });
  });
});

// SEC-INJ-002: XSS in recipe steps
describe('XSS Prevention', () => {
  it('Script tags are sanitized in dish description', async () => {
    const res = await request(app).post('/v1/admin/dishes')
      .set('Authorization', `Bearer ${editorToken}`)
      .send({ ...validDish, description: '<script>alert("xss")</script>Mô tả' });
    const dish = await request(app).get(`/v1/dishes/${res.body.data.dishId}`)
      .set('Authorization', `Bearer ${userToken}`);
    expect(dish.body.data.description).not.toContain('<script>');
  });
});
```

---

## 5. OWASP ZAP AUTOMATED SCAN

```yaml
# zap/scan-config.yaml
env:
  contexts:
    - name: "NutriDay API"
      urls: ["https://staging-api.nutriday.vn"]
      authentication:
        method: "json"
        parameters:
          loginPageUrl: "https://staging-api.nutriday.vn/v1/auth/login"
          loginRequestData: '{"emailOrPhone":"zap@test.vn","password":"Zap@1234"}'
          tokenField: "data.accessToken"
  policy: "API-Scan"

jobs:
  - type: activeScan
    parameters:
      maxRuleDurationInMins: 5
      maxScanDurationInMins: 60
  - type: report
    parameters:
      template: "traditional-html"
      reportDir: "./reports"
```

### ZAP CI Integration

```yaml
# .github/workflows/security.yml
security-scan:
  runs-on: ubuntu-latest
  steps:
    - uses: zaproxy/action-api-scan@v0.7.0
      with:
        target: 'https://staging-api.nutriday.vn/v1'
        rules_file_name: 'zap/rules.tsv'
        cmd_options: '-a -j'
```

---

## 6. DEPENDENCY AUDIT

```bash
# Chạy hàng tuần trong CI:
npm audit --audit-level=high
# Fail nếu có high/critical vulnerabilities

# Snyk integration (optional):
snyk test --severity-threshold=high
```

---

**Trang trước:** [06 — Performance Tests ←](TSD-ND-2025-001_06_Performance.md)
**Trang tiếp theo:** [08 — Test Cases ←](TSD-ND-2025-001_08_TestCases.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
