# APID-ND-2025-001_00 — Tổng Quan & Quy Ước Chung
# NutriDay API Design

---

| | |
|---|---|
| **Module** | 00 — Overview |
| **Phiên bản** | v1.0 DRAFT |
| **Ngày tạo** | 20/03/2025 |
| **Index** | [← Quay lại INDEX](APID-ND-2025-001_INDEX.md) |

---

## 1. TỔNG QUAN API

### 1.1 Kiến trúc

NutriDay API là **RESTful API** được xây dựng trên **NestJS**, triển khai trên **AWS**, phục vụ:
- **Mobile App** (React Native — iOS & Android)
- **Web Admin** (ReactJS — Desktop)

### 1.2 Base URL

| Môi trường | URL |
|---|---|
| **Development** | `https://api-dev.nutriday.vn/v1` |
| **Staging** | `https://api-staging.nutriday.vn/v1` |
| **Production** | `https://api.nutriday.vn/v1` |

### 1.3 Phiên bản API

- Phiên bản hiện tại: **v1**
- Định dạng: `/v1/{resource}`
- Chiến lược nâng cấp: URL versioning, duy trì v1 tối thiểu 12 tháng sau khi phát hành v2

### 1.4 Hiệu năng yêu cầu

| Chỉ số | Yêu cầu |
|---|---|
| **API Response Time** | ≤ 300ms (p95) |
| **Throughput** | ≥ 500 RPS |
| **Uptime** | ≥ 99.5% |

---

## 2. QUY ƯỚC CHUNG

### 2.1 Request Format

- **Content-Type:** `application/json`
- **Accept:** `application/json`
- **Encoding:** UTF-8
- **Date Format:** ISO 8601 — `2025-04-01T10:00:00+07:00`

### 2.2 Response Format

Tất cả response tuân theo cấu trúc chuẩn:

```json
{
  "success": true,
  "statusCode": 200,
  "message": "Thành công",
  "data": { ... },
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "totalPages": 8
  },
  "timestamp": "2025-04-01T10:00:00+07:00"
}
```

**Error Response:**
```json
{
  "success": false,
  "statusCode": 400,
  "message": "Dữ liệu không hợp lệ",
  "error": {
    "code": "VALIDATION_ERROR",
    "details": [
      { "field": "email", "message": "Email không hợp lệ" }
    ]
  },
  "timestamp": "2025-04-01T10:00:00+07:00"
}
```

### 2.3 Pagination

Áp dụng cho tất cả endpoint trả về danh sách:

| Query Param | Mô tả | Default | Max |
|---|---|---|---|
| `page` | Số trang (bắt đầu từ 1) | 1 | — |
| `limit` | Số bản ghi mỗi trang | 20 | 100 |
| `sortBy` | Trường sắp xếp | `createdAt` | — |
| `sortOrder` | `asc` hoặc `desc` | `desc` | — |

### 2.4 HTTP Methods

| Method | Mục đích |
|---|---|
| `GET` | Lấy dữ liệu |
| `POST` | Tạo mới |
| `PUT` | Cập nhật toàn bộ |
| `PATCH` | Cập nhật một phần |
| `DELETE` | Xóa |

### 2.5 HTTP Status Codes

| Code | Ý nghĩa |
|---|---|
| `200 OK` | Thành công |
| `201 Created` | Tạo mới thành công |
| `204 No Content` | Xóa thành công |
| `400 Bad Request` | Dữ liệu không hợp lệ |
| `401 Unauthorized` | Chưa xác thực |
| `403 Forbidden` | Không có quyền |
| `404 Not Found` | Không tìm thấy |
| `409 Conflict` | Xung đột dữ liệu |
| `422 Unprocessable Entity` | Lỗi business logic |
| `429 Too Many Requests` | Vượt rate limit |
| `500 Internal Server Error` | Lỗi hệ thống |

---

## 3. XÁC THỰC & BẢO MẬT

### 3.1 Phương thức xác thực

Sử dụng **JWT (JSON Web Token) — Bearer Token**

```
Authorization: Bearer <access_token>
```

### 3.2 Token Life

| Token | Thời hạn |
|---|---|
| `access_token` | 15 phút |
| `refresh_token` | 30 ngày |

### 3.3 Rate Limiting

| Endpoint | Giới hạn |
|---|---|
| Auth endpoints | 10 requests/phút/IP |
| OTP endpoints | 5 requests/phút/IP |
| Public endpoints | 100 requests/phút/IP |
| Authenticated endpoints | 300 requests/phút/user |
| Admin endpoints | 500 requests/phút/user |

### 3.4 Security Headers

```
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Strict-Transport-Security: max-age=31536000
```

---

**Trang tiếp theo:** [01 — Auth API →](APID-ND-2025-001_01_Auth.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
