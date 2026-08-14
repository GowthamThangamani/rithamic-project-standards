# Hybrid SSO & Single Sign-On Specification

This specification defines the cryptographic ticket exchange protocol for cross-application single sign-on (SSO) and workspace switching across the Rithamic software ecosystem.

---

## 1. Core Principles

1. **Suite Clusters vs. Standalone Apps**:
   - Apps sharing a **`product_suite`** (e.g. `'rithamic_business'`) permit zero-prompt auto-switching.
   - Apps with `product_suite = NULL` (e.g. **Family Tree**) operate as isolated standalone apps.
2. **Zero URL Token Exposure**:
   - Real JWT tokens are **NEVER** passed in URL query strings.
   - Instead, the backend issues an ephemeral, single-use, 60-second **SSO Exchange Ticket** (`sso_tk_...`).
3. **One-Time Consumption**:
   - The ticket is immediately invalidated upon first exchange.

---

## 2. SSO Exchange Protocol Sequence

```text
[ Client App A (POS) ]               [ Backend API ]               [ Client App B (CoirFlow) ]
         │                                  │                                   │
         │ 1. User clicks "Open CoirFlow"   │                                   │
         ├─── POST /api/auth/sso/generate ─►│                                   │
         │    Headers: Bearer JWT           │                                   │
         │    Body: { targetProjectKey }    │                                   │
         │                                  │                                   │
         │◄── Returns Ticket & URL ─────────┤                                   │
         │    { ticket: "sso_tk_98a",       │                                   │
         │      redirectUrl: "..." }        │                                   │
         │                                  │                                   │
         │ 2. Browser Redirects: https://coirflow.rithamic.co.in/sso?ticket=sso_tk_98a │
         └─────────────────────────────────────────────────────────────────────►│
                                                                                │
                                            │ 3. Target App exchanges ticket    │
                                            │◄── POST /api/auth/sso/exchange ───┤
                                            │    Body: { ticket: "sso_tk_98a" } │
                                            │                                   │
                                            ├─── Returns fresh JWT for App B ──►│
                                            │    { token: "JWT_B", user: {...}} │
                                            │                                   │
                                            │ 4. User is logged in instantly!   │
```

---

## 3. Endpoints & Schemas

### A. Generate SSO Ticket
- **Route**: `POST /api/auth/sso/generate-ticket`
- **Auth**: `Bearer <current_app_jwt>`
- **Request Body**:
  ```json
  {
    "targetProjectKey": "coirflow_factory_erp"
  }
  ```
- **Response (`200 OK`)**:
  ```json
  {
    "success": true,
    "ticket": "sso_tk_3f8e910a2b4c5d6e7f8a9b0c1d2e3f4a",
    "expiresInSeconds": 60,
    "redirectUrl": "https://coirflow.rithamic.co.in/sso/callback?ticket=sso_tk_3f8e910a2b4c5d6e7f8a9b0c1d2e3f4a"
  }
  ```

### B. Exchange SSO Ticket
- **Route**: `POST /api/auth/sso/exchange`
- **Auth**: Public / Origin Validated
- **Request Body**:
  ```json
  {
    "ticket": "sso_tk_3f8e910a2b4c5d6e7f8a9b0c1d2e3f4a",
    "targetProjectKey": "coirflow_factory_erp"
  }
  ```
- **Response (`200 OK`)**:
  ```json
  {
    "success": true,
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": 1,
      "email": "gowtham@rithamic.co.in",
      "fullName": "Gowtham Thangamani",
      "role": "factory_owner",
      "projectKey": "coirflow_factory_erp"
    }
  }
  ```

### C. Workspace Resolver
- **Route**: `GET /api/auth/hub/workspaces`
- **Auth**: `Bearer <any_valid_user_jwt>`
- **Response (`200 OK`)**:
  ```json
  {
    "success": true,
    "email": "gowtham@rithamic.co.in",
    "suites": [
      {
        "suiteKey": "rithamic_business",
        "suiteName": "Rithamic Business Suite",
        "apps": [
          { "projectKey": "rithamic_pos", "name": "Rithamic POS", "role": "admin", "url": "https://pos.rithamic.co.in" },
          { "projectKey": "coirflow_erp", "name": "CoirFlow ERP", "role": "owner", "url": "https://coirflow.rithamic.co.in" }
        ]
      }
    ],
    "standalone": [
      { "projectKey": "rithamic_familytree", "name": "Family Tree", "role": "admin", "url": "https://familytree.rithamic.co.in" }
    ]
  }
  ```
