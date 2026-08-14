# Technical Stack, Conventions & Code Standards

This document establishes the official technical stack, architectural standards, and coding conventions across the Rithamic software ecosystem.

---

## 1. Approved Technology Stacks

### A. Backend Services
- **Runtime**: Node.js (Active LTS).
- **Language**: TypeScript 5.x (Strict mode enabled, `noImplicitAny: true`).
- **Framework**: Express 5.x.
- **Validation**: Zod (100% of request payloads and environment variables).
- **Security**: Helmet security middleware, rate limiting.
- **Logging**: Pino structured logging with `rotating-file-stream` date-wise daily file rotation.
- **Dev Runner**: `tsx` for zero-config hot-reloading development.

### B. Frontend Applications
- **Language**: **TypeScript 5.x** (Strict mode enabled across all frontend apps).
- **Build Tool / Dev Server**: **Vite** (`npm run dev`, `npm run build` with `tsc && vite build`).
- **Architecture**:
  - `src/types/index.ts`: Strongly typed domain models, user roles, API response envelopes.
  - `src/config/index.ts`: Central API and SSO URL resolvers.
  - `src/services/`: Modular typed services (`authService.ts`, `telemetryService.ts`, `dataService.ts`).
- **CSS / Styling**:
  - Clean Vanilla CSS with curated CSS variables and design tokens.
  - Curated palettes (Slate, Royal Blue, Emerald, Warm Gold).
  - Google Fonts typography (`Inter`, `Outfit`, `Plus Jakarta Sans`).
  - Glassmorphism, smooth gradients, subtle micro-animations.
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
5. **Cross-App Single Sign-On (SSO)**:
   Use 60-second single-use cryptographic SSO exchange tickets.

---

## 3. Copywriting & UX Principles

1. **Practical & Benefit-Driven**:
   Speak directly to business owners, managers, and family members in clear layman's terms.
2. **Avoid Developer Jargon**:
   Do not show status codes, raw stack traces, terminal logs, or database connection strings to end users.
3. **Responsive by Default**:
   Every interface must look stunning and function seamlessly on mobile, tablet, and desktop viewports.
