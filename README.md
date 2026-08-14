# Rithamic Engineering Standards & Best Practices

Welcome to the central repository for technical standards, architectural patterns, API design rules, and security guidelines across the **Rithamic Software Ecosystem**.

---

## 📚 Standard Documentation Index

| # | Standard Document | Description |
|:---|:---|:---|
| 1 | [**`WORKSPACE_AND_FOLDER_STRUCTURE_STANDARDS.md`**](./WORKSPACE_AND_FOLDER_STRUCTURE_STANDARDS.md) | Ecosystem root `/rithamic-projects`, multi-root `.code-workspace`, and folder architecture. |
| 2 | [**`TECH_STACK_AND_CONVENTIONS.md`**](./TECH_STACK_AND_CONVENTIONS.md) | Official Node.js/Express, TypeScript 5.x, Vite, and PostgreSQL standards. |
| 3 | [**`NEW_PROJECT_SETUP_GUIDE.md`**](./NEW_PROJECT_SETUP_GUIDE.md) | Step-by-step instructions for initializing new Rithamic client applications. |
| 4 | [**`RITHAMIC_LOGIN_INTEGRATION.md`**](./RITHAMIC_LOGIN_INTEGRATION.md) | Complete guide for Email OTP, Google OAuth2, Magic Links, and local `devOtp` mode. |
| 5 | [**`HYBRID_SSO_SPECIFICATION.md`**](./HYBRID_SSO_SPECIFICATION.md) | Centralized SSO authentication, ticket exchanges, and cross-domain token handshakes. |
| 6 | [**`LOGGING_AND_MONITORING_STANDARDS.md`**](./LOGGING_AND_MONITORING_STANDARDS.md) | Pino structured logging, daily midnight rotation, 14-day purging, and client telemetry. |
| 7 | [**`API_DESIGN_STANDARDS.md`**](./API_DESIGN_STANDARDS.md) | RESTful URL conventions, HTTP verbs, standard response envelopes, and error formatting. |
| 8 | [**`DATABASE_MIGRATION_STANDARDS.md`**](./DATABASE_MIGRATION_STANDARDS.md) | Versioned idempotent SQL migrations (`rithamYYYYN.sql`) and connection pool health checks. |

---

## 🎯 Global Principles

1. **Zero Frontend Secrets**: No private API keys or database credentials in client apps.
2. **Strict TypeScript 5.x**: Full types, Zod validation, and zero implicit `any`.
3. **Multi-Tenant Scoping**: All backend endpoints accept dynamic `:projectKey` routing.
4. **Rich Aesthetics**: Glassmorphism, Google Fonts (`Inter`, `Outfit`), and responsive design.
