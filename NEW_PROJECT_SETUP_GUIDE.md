# New Project Setup Guide & Blueprint

Follow this step-by-step checklist whenever initializing a new application or service in the Rithamic ecosystem.

---

## 📋 1. Project Naming & Git Conventions

- **Repository Naming**: Use kebab-case prefixed with `rithamic-` (e.g. `rithamic-pos`, `rithamic-login`, `rithamic-coirflow`).
- **Workspace Structure**:
  ```text
  rithamic-<project-name>/
  ├── .agents/
  │   └── AGENTS.md                  # AI agent rules & repository context
  ├── docs/
  │   ├── SPECIFICATION.md          # Functional requirements & domain logic
  │   └── INTEGRATION.md            # API endpoints & backend connections
  ├── src/                           # Source code (TypeScript / Vanilla Web / Next.js)
  ├── tests/                         # Automated test scripts
  ├── README.md                      # Quickstart, stack summary, commands
  └── package.json                   # Scripts & dependencies
  ```

---

## 📋 2. Registering the Project in Backend Database

Before the new frontend can use OTP or send telemetry, register it in `rithamic-backend-api/sql/ritham20261.sql`:

```sql
INSERT INTO client_projects (
    project_key, 
    project_name, 
    product_suite,          -- 'rithamic_business' (for suite apps) OR NULL (for standalone)
    app_launch_url,         -- e.g. 'https://pos.rithamic.co.in'
    allowed_origins,        -- Local dev & production domains
    rate_limit_max, 
    rate_limit_window_ms, 
    otp_channel_preferences,
    email_sender_name, 
    email_subject_template
)
VALUES (
    'rithamic_pos',
    'Rithamic POS',
    'rithamic_business',
    'https://pos.rithamic.co.in',
    ARRAY['http://localhost:5173', 'https://pos.rithamic.co.in'],
    20,
    60000,
    '{"email": true, "sms": false, "whatsapp": false}'::jsonb,
    'Rithamic POS',
    'Your Rithamic POS Login Code: {{otp}}'
)
ON CONFLICT (project_key) DO UPDATE 
SET project_name = EXCLUDED.project_name,
    product_suite = EXCLUDED.product_suite,
    app_launch_url = EXCLUDED.app_launch_url,
    allowed_origins = EXCLUDED.allowed_origins;
```

Run migration:
```bash
cd rithamic-backend-api
npm run db:update
```

---

## 📋 3. Connecting to Central Services

### A. Authentication & Login Flow
- **Option 1 (Redirect to `rithamic-login`)**:
  ```text
  https://auth.rithamic.co.in?project=rithamic_pos&returnUrl=https://pos.rithamic.co.in/auth/callback
  ```
- **Option 2 (Direct In-App OTP)**:
  - Request code: `POST /api/auth/rithamic_pos/otp/request`
  - Verify code: `POST /api/auth/rithamic_pos/otp/verify`

### B. Common Metrics & Telemetry
In your frontend, record user interactions non-blockingly:
```javascript
async function trackEvent(eventType, eventName, metadata = {}) {
  try {
    fetch('https://api.rithamic.co.in/api/metrics/rithamic_pos/events', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        events: [{ eventType, eventName, metadata }]
      })
    });
  } catch {}
}
```

### C. Leads & Inquiries
```javascript
async function submitLead(formData) {
  return await fetch('https://api.rithamic.co.in/api/leads/rithamic_pos', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(formData)
  });
}
```

---

## 📋 4. Documentation Requirements

Every new repository MUST contain:
1. **`README.md`**: Summary, setup instructions, local dev commands, and architecture diagrams.
2. **`.agents/AGENTS.md`**: Guidance for AI pair programmers working on the codebase.
