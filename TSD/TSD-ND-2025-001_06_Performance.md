# TSD-ND-2025-001_06 — Performance Tests
# NutriDay Test Design

---

| | |
|---|---|
| **Module** | 06 — Performance Tests |
| **Tool** | k6 (Grafana) |
| **Index** | [← Quay lại INDEX](TSD-ND-2025-001_INDEX.md) |

---

## 1. PERFORMANCE TARGETS

| Metric | Target | Fail Threshold |
|---|---|---|
| API Response p50 | < 200ms | > 500ms |
| API Response p95 | < 500ms | > 1000ms |
| API Response p99 | < 1000ms | > 3000ms |
| Error rate | < 1% | > 5% |
| Throughput | > 100 RPS | < 50 RPS |
| Concurrent users | 500 | — |

---

## 2. k6 SCRIPTS

### 2.1 Load Test — Meal Plan Generation

```javascript
// k6/scripts/meal-plan-load.js
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '2m', target: 50 },   // Ramp up to 50 users
    { duration: '5m', target: 200 },  // Ramp up to 200 users
    { duration: '5m', target: 200 },  // Stay at 200
    { duration: '2m', target: 500 },  // Peak: 500 users
    { duration: '3m', target: 500 },  // Stay at peak
    { duration: '2m', target: 0 },    // Ramp down
  ],
  thresholds: {
    http_req_duration: ['p(95)<500', 'p(99)<1000'],
    http_req_failed: ['rate<0.01'],
    http_reqs: ['rate>100'],
  },
};

const BASE_URL = __ENV.BASE_URL || 'https://staging-api.nutriday.vn';

export function setup() {
  // Login and get tokens for test users
  const tokens = [];
  for (let i = 1; i <= 100; i++) {
    const res = http.post(`${BASE_URL}/v1/auth/login`, JSON.stringify({
      emailOrPhone: `loadtest_${i}@test.vn`,
      password: 'LoadTest@123',
    }), { headers: { 'Content-Type': 'application/json' } });
    tokens.push(res.json('data.accessToken'));
  }
  return { tokens };
}

export default function (data) {
  const token = data.tokens[Math.floor(Math.random() * data.tokens.length)];
  const headers = {
    'Content-Type': 'application/json',
    'Authorization': `Bearer ${token}`,
  };

  // Scenario: User opens app → view today plan → view dish detail
  const todayPlan = http.get(`${BASE_URL}/v1/meal-plans/today`, { headers });
  check(todayPlan, {
    'today plan 200': (r) => r.status === 200,
    'has 3 meals': (r) => r.json('data.meals').length === 3,
  });

  sleep(1);

  const dishId = todayPlan.json('data.meals.0.dishId');
  if (dishId) {
    const dishDetail = http.get(`${BASE_URL}/v1/dishes/${dishId}`, { headers });
    check(dishDetail, {
      'dish detail 200': (r) => r.status === 200,
      'has nutrition': (r) => r.json('data.nutrition') !== null,
    });
  }

  sleep(2);
}
```

### 2.2 Stress Test — API Endpoints Mix

```javascript
// k6/scripts/api-stress.js
export const options = {
  scenarios: {
    browse: {
      executor: 'ramping-vus',
      startVUs: 0,
      stages: [
        { duration: '5m', target: 300 },
        { duration: '10m', target: 500 },
        { duration: '5m', target: 0 },
      ],
      exec: 'browse',
    },
    generate: {
      executor: 'constant-arrival-rate',
      rate: 10,           // 10 iterations/sec
      timeUnit: '1s',
      duration: '10m',
      preAllocatedVUs: 50,
      exec: 'generatePlan',
    },
  },
  thresholds: {
    'http_req_duration{scenario:browse}': ['p(95)<300'],
    'http_req_duration{scenario:generate}': ['p(95)<1000'],
    http_req_failed: ['rate<0.05'],
  },
};

export function browse(data) {
  // GET /dishes, GET /search, GET /favorites
  http.get(`${BASE_URL}/v1/dishes?page=1&limit=20`, { headers });
  sleep(0.5);
}

export function generatePlan(data) {
  // POST /meal-plans/generate (heaviest endpoint)
  http.post(`${BASE_URL}/v1/meal-plans/generate`,
    JSON.stringify({ date: '2025-06-01', mode: 'single' }),
    { headers }
  );
}
```

### 2.3 Spike Test

```javascript
// k6/scripts/spike.js
export const options = {
  stages: [
    { duration: '1m', target: 50 },
    { duration: '10s', target: 1000 },  // Spike!
    { duration: '3m', target: 1000 },
    { duration: '10s', target: 50 },    // Drop
    { duration: '2m', target: 50 },
  ],
  thresholds: {
    http_req_duration: ['p(99)<3000'],  // Relaxed for spike
    http_req_failed: ['rate<0.1'],       // 10% acceptable during spike
  },
};
```

---

## 3. DATABASE PERFORMANCE

### 3.1 Slow Query Monitoring

```sql
-- MySQL slow query log: threshold = 200ms
SET GLOBAL slow_query_log = 1;
SET GLOBAL long_query_time = 0.2;

-- Key queries to benchmark:
-- Q1: Meal plan generation (complex JOIN + filter)
-- Q2: Shopping list aggregation (GROUP BY)
-- Q3: Search (FULLTEXT)
-- Q4: Admin dish list (paginated with filters)
```

### 3.2 Expected Query Performance

| Query | Target | Index Used |
|---|---|---|
| Get today plan | < 5ms | idx_mp_user_date |
| List dishes (paginated) | < 20ms | idx_dishes_status |
| Generate plan (eligible dishes) | < 50ms | idx_ddg_goal_dish + idx_da_allergen |
| Shopping list aggregate | < 30ms | idx_di_dish_ingredient |
| FULLTEXT search | < 50ms | ft_dishes_name_desc |
| Admin dish list + filters | < 30ms | idx_dishes_status_deleted |

---

## 4. CACHING PERFORMANCE

| Scenario | Without Cache | With Cache | Cache Hit Target |
|---|---|---|---|
| Dish detail | ~20ms | ~2ms | > 80% |
| Today plan | ~15ms | ~2ms | > 70% |
| Dish list | ~30ms | ~3ms | > 60% |
| Search results | ~50ms | ~5ms | > 50% |

---

**Trang trước:** [05 — E2E Admin ←](TSD-ND-2025-001_05_E2E_Admin.md)
**Trang tiếp theo:** [07 — Security Tests →](TSD-ND-2025-001_07_Security.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
