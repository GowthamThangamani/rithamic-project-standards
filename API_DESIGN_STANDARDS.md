# Rithamic API Design Standards & Architecture Guidelines

This document outlines the engineering standards and conventions mandatory for all backend services and client applications across the Rithamic ecosystem.

---

## 1. Technology Stack & Framework Rules

1. **Language & Runtime**: Strict **TypeScript 5.x** running on Node.js.
2. **Framework**: **Express 5.x** with centralized routers.
3. **Payload & Environment Validation**: **Zod** schemas for all incoming HTTP requests (`body`, `query`, `params`) and `.env` variables.
4. **Security Hardening**: **Helmet** middleware on all entrypoints.
5. **Logging**: **Pino** structured logging with **`rotating-file-stream`** for date-wise daily rotated logs (`logs/api-YYYY-MM-DD.log`).

---

## 2. URL & Route Conventions

* **Multi-Tenancy Scoping**:
  All tenant-specific routes MUST include `:projectKey` in the URL path:
  ```text
  /api/auth/:projectKey/otp/request
  /api/metrics/:projectKey/events
  /api/users/:projectKey
  /api/leads/:projectKey
  ```
* **HTTP Method Semantics**:
  - `GET`: Read-only, cacheable, zero side-effects.
  - `POST`: Create resource, trigger OTP, or submit actions.
  - `PUT`: Complete resource replacement or state update.
  - `DELETE`: Safe removal or deactivation.

---

## 3. Standard Response Formats

### A. Success Response Envelope
```json
{
  "success": true,
  "message": "Human readable confirmation (optional)",
  "data": { ... }
}
```

### B. Error Response Envelope
```json
{
  "error": "Short, actionable error title",
  "details": [
    {
      "field": "email",
      "message": "Valid email address is required"
    }
  ]
}
```

### Standard HTTP Status Codes:
- `200 OK`: Request succeeded.
- `201 Created`: Resource successfully created.
- `400 Bad Request`: Validation failure (Zod error).
- `401 Unauthorized`: Missing, expired, or invalid Bearer JWT.
- `403 Forbidden`: Insufficient role or domain mismatch.
- `404 Not Found`: Project or resource does not exist.
- `429 Too Many Requests`: Rate limit or cooldown triggered.
- `500 Internal Server Error`: Unexpected runtime failure.

---

## 4. Authentication Standards

1. **Bearer Token Authentication**:
   Protected endpoints require:
   ```text
   Authorization: Bearer <jwt_token>
   ```
2. **Dual-Scope Tokens**:
   - `type: "user"`: Contains `{ id, email, fullName, role, projectKey }`.
   - `type: "project"`: Contains `{ projectKey, issuedAt }`.
3. **Role-Based Access Control (RBAC)**:
   - Every protected route enforces permissions via `requireRole('admin')`, `requireRole('editor', 'admin')`.
