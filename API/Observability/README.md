# API Observability

Operational and security observability is built from Serilog, structured security events, optional HTTP request/response logging with redaction, audit trails with sensitive-field redaction, exception ErrorIds, and health checks.

Related: [Security](../Security/README.md) · [Authentication](../Authentication/README.md) · [Persistence](../Persistence/README.md)

Diagrams: [Audit trail + soft delete](../../assets/diagrams/audit-soft-delete/README.md) · [Session lifecycle](../../assets/diagrams/session-lifecycle/README.md)

## Verified capabilities

- **Serilog host logging** — enrichers (app/machine/process/thread/exception details), console output (plain or compact JSON), optional rolling file sink, optional Elasticsearch sink when configured.
- **Security event logging** — dedicated helper writes structured security contexts (event name, outcome, user/tenant/session, path, IP, correlation/error ids).
- **Security event catalog** — authentication, session, authorization, administration, and operations event name constants (login, 2FA, refresh/reuse, logout, permission denied, cross-tenant denied, rate-limit rejected, Hangfire access, etc.).
- **Request/response logging** (optional) — gated by middleware settings; redacts Authorization/Cookie/API-key headers and sensitive path bodies (tokens/password/2FA/social/jobs fragments).
- **Exception ErrorId** — each handled failure gets a GUID ErrorId in logs and client payload for support correlation.
- **EF audit trails** — Create/Update/Delete trails persisted to Auditing DB; sensitive property names redacted (password/refresh-token/authenticator/connection-string style fields).
- **Health checks** — liveness and readiness endpoints (database + Hangfire readiness when enabled).

## Security event categories (high level)

| Category | Examples of covered outcomes |
|----------|------------------------------|
| Authentication | Login success/failure, lockout, disabled user, 2FA challenge/result, password reset/change |
| Session | Created, refresh success/reject, rotated-token reuse, logout, idle/absolute expiry |
| Authorization | Anonymous challenge, forbidden, permission denied, cross-tenant denied |
| Administration | User/role/permission/tenant administration events |
| Operations | Rate-limit reject, Hangfire access, config/external-secrets failures |

Exact constant strings live in source; public docs intentionally summarize categories rather than publishing a full operational playbook.

## Configuration (shape only)

- `API/src/Host/Configurations/logger.json` — app name, file/console/Elastic options
- `API/src/Host/Configurations/middleware.json` — request/response logging enablement
- `API/src/Host/Configurations/exceptionhandling.json` — detailed error exposure
- `API/src/Host/Configurations/healthchecks.json` — live/ready paths and detail flags

## Evidence

- `API/src/Infrastructure/Logging/Serilog/Extensions.cs`
- `API/src/Infrastructure/Logging/LoggerSettings.cs`
- `API/src/Infrastructure/Logging/Security/SecurityEventLogger.cs`
- `API/src/Infrastructure/Logging/Security/SecurityEventNames.cs`
- `API/src/Infrastructure/Middleware/RequestLoggingMiddleware.cs`
- `API/src/Infrastructure/Middleware/ResponseLoggingMiddleware.cs`
- `API/src/Infrastructure/Middleware/ExceptionMiddleware.cs`
- `API/src/Infrastructure/Auditing/AuditTrail.cs`
- `API/src/Infrastructure/Persistence/Context/BaseDbContext.cs`
- `API/src/Infrastructure/Persistence/Context/Auditing/AuditingDbContext.cs`
- `API/src/Infrastructure/HealthChecks/Startup.cs`
- `API/src/Host/Configurations/{logger,middleware,exceptionhandling,healthchecks}.json`
