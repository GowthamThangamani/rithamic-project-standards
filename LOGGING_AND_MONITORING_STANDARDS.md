# Logging & Monitoring Standards

This document defines the mandatory logging, telemetry, and diagnostics standards across all backend services and client applications in the Rithamic ecosystem.

---

## 1. Structured Logging Standards (Pino + RFS)

1. **Standard Logger Module**: Use **Pino** (high performance structured logger) + **`rotating-file-stream`** (RFS).
2. **Date-Wise Daily File Rotation**:
   - Log files MUST be created date-wise: `logs/api-YYYY-MM-DD.log`.
   - Rotation occurs automatically daily at **midnight (00:00:00)**.
   - Older log files are automatically compressed using **gzip** (`api-YYYY-MM-DD.1.log.gz`).
   - Auto-retention window: Logs older than **14 days** (configurable via `LOG_RETENTION_DAYS`) are automatically purged.
3. **Dual-Stream Output**:
   - **Development**: Colorized pretty-printed logs to terminal/stdout via `pino-pretty`.
   - **Production**: High-performance structured JSON to stdout and date-stamped file stream.

---

## 2. Standard Log Levels & When to Use

| Log Level | Purpose & Usage Rule | Example |
|:---|:---|:---|
| `debug` | Detailed diagnostics during development only. Never log sensitive passwords or full DB credentials. | `Database query executed in 3ms` |
| `info` | Normal application lifecycle events, server boot, successful email dispatch, and HTTP access logs. | `🚀 Server running on port 3000`, `Email dispatched to user@...` |
| `warn` | Recoverable issues, client validation failures, rate limiting triggered, or OTP cooldown active. | `Rate limit exceeded for IP 127.0.0.1`, `Invalid login attempt` |
| `error` | Unhandled runtime errors, database connection failures, SMTP delivery failures. | `Database connection timeout`, `Unhandled exception in route /api/...` |

---

## 3. Standard HTTP Request & Error Log Formats

### A. HTTP Request Access Log
Every incoming request must capture:
- `method`: `GET` | `POST` | `PUT` | `DELETE`
- `url`: Request URL path
- `statusCode`: HTTP response status code
- `durationMs`: Total execution latency in milliseconds
- `ip`: Client IP address (`x-forwarded-for` or socket)
- `origin`: Client domain origin or referer

### B. Error Log Format
Errors must capture:
- `err.message`: Short description
- `err.stack`: Full stack trace
- `context`: Relevant project key, user ID, or payload context (excluding sensitive credentials).

---

## 4. Environment Configuration Variables

Every backend service must include these standardized `.env` logging parameters:

```env
# Logging Configuration
LOG_LEVEL=info              # 'debug' | 'info' | 'warn' | 'error'
LOG_DIR=logs                # Output directory
LOG_RETENTION_DAYS=14d      # Log retention duration
LOG_TO_CONSOLE=true         # Output to terminal
LOG_TO_FILE=true            # Output to date-wise rotating files
```

---

## 5. Client Telemetry & Metrics Ingestion Standards

Client applications stream telemetry events to the central backend at:
`POST /api/metrics/:projectKey/events`

Standard event types:
- `page_view`: Page views and navigation transitions (`metadata: { page: "home", view: "tree" }`).
- `search_query`: Search bar inquiries (`metadata: { query: "Palanivel" }`).
- `node_focus` / `interaction`: Entity selection and UI interactions.
- `export_action`: Downloads (`metadata: { format: "pdf" | "json" }`).
