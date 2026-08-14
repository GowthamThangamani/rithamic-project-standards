# Technical Stack, Conventions & Code Standards

This document establishes the official technical stack, architectural standards, and coding conventions across the Rithamic software ecosystem.

---

## 1. Approved Technology Stacks

### A. Backend Services
- **Runtime**: Node.js (Active LTS).
- **Language**: TypeScript 5.x (Strict mode enabled).
- **Framework**: Express 5.x.
- **Validation**: Zod (100% of request payloads and environment variables).
- **Security**: Helmet security middleware, rate limiting.
- **Logging**: Pino structured logging with `rotating-file-stream` date-wise daily file rotation.
- **Dev Runner**: `tsx` for zero-config hot-reloading development.

### B. Frontend Applications
- **Web Applications**: HTML5, Vanilla CSS / Modern CSS, JavaScript/TypeScript.
- **Complex Dynamic SPAs**: Next.js or Vite (when explicitly warranted).
- **CSS / Styling**:
  - Prefer clean Vanilla CSS or tailored component design systems.
  - Avoid generic plain colors; use curated harmonious palettes (Slate `#1e293b`, Royal Blue `#2563eb`, Emerald `#10b981`, Light Slate `#f8fafc`).
  - Google Fonts typography (`Inter`, `Outfit`, `Plus Jakarta Sans`).
  - Glassmorphism, smooth gradients, and subtle micro-animations.
  - Zero placeholder images.

### C. Database Architecture
- **Engine**: PostgreSQL 16+.
- **Migrations**: 100% Idempotent SQL scripts (`sql/rithamYYYYN.sql`).
- **Connection Management**: Typed connection pool with health check pinging (`SELECT 1`).

---

## 2. API Design & Security Rules

1. **Zero Frontend Secrets**:
   Never embed private API keys, database credentials, or SMTP passwords in frontend repositories.
2. **Dynamic Project Scoping**:
   Every multi-tenant endpoint accepts `:projectKey` in the URL path.
3. **CORS & Origin Protection**:
   `allowed_origins` stored in PostgreSQL per project.
4. **Token Security**:
   JWT Bearer tokens must be cryptographically signed with `JWT_SECRET` and include expiration dates.

---

## 3. Copywriting & UX Principles

1. **Practical & Benefit-Driven**:
   Speak directly to business owners, managers, and family members in clear layman's terms.
2. **Avoid Developer Jargon**:
   Do not show status codes, raw stack traces, terminal logs, or database connection strings to end users.
3. **Responsive by Default**:
   Every interface must look stunning and function seamlessly on mobile, tablet, and desktop viewports.
