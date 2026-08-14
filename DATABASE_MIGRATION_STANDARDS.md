# Database Migration Standards & Guidelines

This document outlines the database standards for PostgreSQL schemas, updates, and migration scripts across the Rithamic backend platform.

---

## 1. Idempotency Mandate

Every `.sql` script and migration must be **100% IDEMPOTENT**.
It must be safe to execute the script 1 time or 1,000 times on any database environment without errors or data duplication.

---

## 2. Standard Execution Sequence

```text
1. Tracking Registry (`CREATE TABLE IF NOT EXISTS schema_migrations`)
2. Base Tables (`CREATE TABLE IF NOT EXISTS table_name (...)`)
3. Column Upgrades (`ALTER TABLE table_name ADD COLUMN IF NOT EXISTS ...`)
4. Indexes (`CREATE INDEX IF NOT EXISTS idx_name ON table_name(...)`)
5. Data Upserts (`INSERT INTO ... ON CONFLICT (...) DO UPDATE`)
6. Migration Completion (`INSERT INTO schema_migrations ... ON CONFLICT DO UPDATE`)
```

---

## 3. Safe Column Alteration Template

```sql
DO $$ 
BEGIN
    IF EXISTS (SELECT 1 FROM information_schema.tables WHERE table_name = 'client_projects') THEN
        ALTER TABLE client_projects ADD COLUMN IF NOT EXISTS product_suite VARCHAR(50) DEFAULT NULL;
        ALTER TABLE client_projects ADD COLUMN IF NOT EXISTS app_icon_url VARCHAR(255);
        ALTER TABLE client_projects ADD COLUMN IF NOT EXISTS app_launch_url VARCHAR(255);
    END IF;
END $$;
```

---

## 4. Seed Data Upsert Template

```sql
INSERT INTO client_projects (project_key, project_name, allowed_origins)
VALUES ('my_app', 'My Application', ARRAY['http://localhost:3000'])
ON CONFLICT (project_key) DO UPDATE
SET project_name = EXCLUDED.project_name,
    allowed_origins = EXCLUDED.allowed_origins;
```

---

## 5. Migration Runners

- **`npm run db:update`**: Executes the current idempotent migration (`sql/ritham20261.sql`) against the target database.
- **`npm run db:make`**: Creates the database if it doesn't exist and runs the full schema setup.
