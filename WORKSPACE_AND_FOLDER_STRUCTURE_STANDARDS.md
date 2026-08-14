# Workspace & Folder Structure Standards

This document defines the official directory architecture, folder conventions, and multi-root workspace organization across the Rithamic ecosystem.

---

## 1. Consolidated Ecosystem Root (`/rithamic-projects`)

All Rithamic repositories and shared standards reside inside the centralized parent directory:
`C:\Users\Gowtham\repos\rithamic-projects\`

```text
rithamic-projects/
├── .agents/                        # Ecosystem-wide agent instructions (AGENTS.md)
├── rithamic-backend-api/           # Central TypeScript backend API, Postgres DB & auth
├── rithamic-login/                 # Central SSO login portal & workspace hub
├── rithamic-familytree/            # Interactive lineage & genealogy portal
├── rithamic-lic-agent-profile/     # MDRT insurance agent website & consultation desk
├── rithamic-website/               # Rithamic Studio product & services landing site
├── rithamic-project-standards/     # Official architectural, API, & coding guidelines
└── rithamic-project.code-workspace # Multi-root workspace definition file
```

---

## 2. Multi-Root Workspace (`rithamic-project.code-workspace`)

Every developer and AI agent opens the unified workspace file to navigate seamlessly across all projects:

```json
{
  "folders": [
    { "name": "⚙️ Backend API", "path": "rithamic-backend-api" },
    { "name": "🛡️ Central Login Hub", "path": "rithamic-login" },
    { "name": "🌳 Family Tree Portal", "path": "rithamic-familytree" },
    { "name": "💼 LIC Agent Profile", "path": "rithamic-lic-agent-profile" },
    { "name": "🌐 Rithamic Studio Website", "path": "rithamic-website" },
    { "name": "📚 Engineering Standards", "path": "rithamic-project-standards" }
  ],
  "settings": {
    "typescript.tsdk": "rithamic-backend-api/node_modules/typescript/lib",
    "editor.formatOnSave": true,
    "editor.tabSize": 2
  }
}
```

---

## 3. Backend Service Folder Structure (`rithamic-backend-api`)

```text
rithamic-backend-api/
├── .agents/
│   └── AGENTS.md                  # Repository rules & API guidelines
├── docs/                          # Architecture & integration plans
├── logs/                          # Daily rotated structured Pino log files (api-YYYY-MM-DD.log)
├── postman/                       # Postman collections & environments
├── sql/                           # Versioned idempotent migration scripts (rithamYYYYN.sql)
├── src/
│   ├── config/                    # DB pool, environment variables & Zod env schemas
│   ├── controllers/               # Express request handlers
│   ├── docs/                      # Swagger / OpenAPI 3.0 specification
│   ├── middleware/                # Auth, CORS, rate limiters, Zod validators, Pino logger
│   ├── routes/                    # Express modular route definitions
│   ├── schemas/                   # Zod request/response validation schemas
│   ├── scripts/                   # CLI maintenance scripts (generateToken, test suites)
│   ├── services/                  # Business logic (OTP, OAuth, SSO, Audit, Email, Metrics)
│   ├── types/                     # TypeScript domain models & request interfaces
│   └── server.ts                  # Express server entry point & graceful shutdown
├── package.json                   # "dev": "tsx watch src/server.ts", "build": "tsc"
├── tsconfig.json                  # Strict TypeScript configuration
└── .env                           # Environment secrets (never committed to git)
```

---

## 4. Frontend Application Folder Structure (`Vite + TypeScript`)

All frontend applications (`rithamic-login`, `rithamic-familytree`, etc.) must follow this uniform structure:

```text
<frontend-project-name>/
├── .agents/
│   └── AGENTS.md                  # Frontend portal guidelines
├── public/                        # Static assets, icons, offline JSON datasets
├── src/
│   ├── config/
│   │   └── index.ts               # Central backend API & SSO URL resolvers
│   ├── services/                  # Modular services:
│   │   ├── authService.ts         # SSO ticket exchange, OTP verification, privacy masking
│   │   ├── telemetryService.ts    # Non-blocking client metrics dispatcher
│   │   └── dataService.ts         # Domain data loading & graph queries
│   ├── types/
│   │   └── index.ts               # Domain models, auth users, response envelopes
│   ├── app.ts                     # Main TypeScript orchestrator & DOM bindings
│   └── style.css                  # Curated design tokens, responsive layout, glassmorphism
├── index.html                     # Vite entry HTML loading <script type="module" src="./src/app.ts">
├── package.json                   # "dev": "vite", "build": "tsc && vite build"
├── tsconfig.json                  # Strict TypeScript configuration
├── vite.config.ts                 # Vite server & port configuration
└── .gitignore                     # Ignores node_modules/, dist/, .env
```

---

## 5. Naming Conventions

| Entity | Convention | Examples |
|:---|:---|:---|
| **Repository Folders** | `kebab-case` with `rithamic-` prefix | `rithamic-login`, `rithamic-familytree` |
| **Source Directories** | `camelCase` / `kebab-case` | `controllers`, `services`, `middleware` |
| **TypeScript Files** | `camelCase` with descriptive suffix | `authService.ts`, `leadController.ts` |
| **Interfaces / Types** | `PascalCase` | `AuthUser`, `Individual`, `SsoTicketResponse` |
| **Database Columns** | `snake_case` | `project_key`, `created_at`, `is_verified` |
| **Telemetry Events** | `snake_case` | `page_view`, `search_query`, `sso_login_success` |
| **CSS Variables** | `kebab-case` with `--` prefix | `--bg-primary`, `--accent-gold`, `--text-muted` |
