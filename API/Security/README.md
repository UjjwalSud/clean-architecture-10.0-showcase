# API Security

Config-driven hardening around the request pipeline: HTTPS/HSTS, security headers, CORS validation, rate limiting, exception boundaries, and related operational controls.

Related: [Authentication](../Authentication/README.md) · [Authorization](../Authorization/README.md) · [Multi-Tenancy](../MultiTenancy/README.md) · [Observability](../Observability/README.md)

Diagram: [Request / security pipeline](../../assets/diagrams/request-pipeline/README.md)

## Verified capabilities

- **Ordered security pipeline** — HTTPS/forwarded headers, exception middleware, HSTS/HTTPS redirect, security headers, CORS, authentication, current-user initialization, rate limiting, authorization, optional request logging, Hangfire dashboard, OpenAPI.
- **HTTPS / HSTS** — optional HTTPS redirection and HSTS; forwarded-header trust requires configured known proxies/networks when enabled.
- **Security headers** — config-driven `X-Frame-Options`, `X-Content-Type-Options`, `Referrer-Policy`, `Permissions-Policy`, optional XSS header; authenticated responses can receive a no-store style cache-control value.
- **CORS validation** — origins/headers/methods allowlists; wildcard origins rejected; credentials mode is permanently disallowed for this Bearer API (validator fails startup if credentials are enabled).
- **Rate limiting** — fixed-window ASP.NET Core limiter; global/general partitions prefer authenticated user id, otherwise IP; named policies for authentication, refresh, registration, 2FA, social login, anonymous participant, and Stripe webhook categories. Exact thresholds are intentionally not published.
- **Exception boundary** — consistent JSON error payload with a generated **ErrorId**; detailed exception content only when Development or `ExposeDetailedErrors` is enabled; allowlisted client exceptions return safe messages.
- **Default-authorized controllers** — endpoint mapping applies `RequireAuthorization()` globally; anonymous access is explicit.
- **Hangfire dashboard hardening** — environment/network/auth-mode gates (documented under Background Jobs).
- **Health endpoints** — anonymous liveness/readiness; detailed health entry lists are gated (Development + settings).

## Request / security pipeline

Order in `API/src/Infrastructure/Startup.cs` (`UseInfrastructure`):

1. Forwarded headers (when enabled)
2. Exception middleware
3. HSTS + HTTPS redirection (when enabled)
4. Security headers
5. Static files / file-storage setup
6. Routing
7. CORS
8. Authentication
9. Current-user middleware
10. Rate limiting (when enabled)
11. Authorization
12. Request/response logging (when enabled)
13. Hangfire dashboard
14. OpenAPI / Swagger UI

**Design notes verified in source:** forwarded headers run early; HSTS/HTTPS redirect run after exception middleware; rate limiting runs after authentication so user-keyed partitioning is possible.

## Configuration (shape only)

| File | Controls |
|------|----------|
| `API/src/Host/Configurations/httpssecurity.json` | Redirection, HSTS, forwarded headers |
| `API/src/Host/Configurations/securityheaders.json` | Header enablement and header values |
| `API/src/Host/Configurations/cors.json` (+ Development overlay) | Origins/headers/methods/credentials flag |
| `API/src/Host/Configurations/ratelimit.json` | Enable flag + named policy windows/limits |
| `API/src/Host/Configurations/exceptionhandling.json` (+ Development overlay) | Detailed error exposure |
| `API/src/Host/Configurations/middleware.json` | Request/response logging gate |

## Notable verified limits

- No Content-Security-Policy header is applied by the API security-headers middleware.
- Base CORS config may ship with empty origins; Development overlay supplies localhost origins; production must supply valid origins or startup validation fails.
- `EnableHttpsLogging` names the request/response logging gate (HTTP body/header logging), not TLS itself.

## Evidence

- `API/src/Infrastructure/Startup.cs`
- `API/src/Infrastructure/Https/Startup.cs`
- `API/src/Infrastructure/SecurityHeaders/Startup.cs`
- `API/src/Infrastructure/Cors/Startup.cs`
- `API/src/Infrastructure/Cors/CorsSettingsValidator.cs`
- `API/src/Infrastructure/RateLimiting/Startup.cs`
- `API/src/Infrastructure/Middleware/ExceptionMiddleware.cs`
- `API/src/Infrastructure/Middleware/ExceptionHandlingSettings.cs`
- `API/src/Infrastructure/Middleware/ErrorResult.cs`
- `API/src/Infrastructure/Middleware/RequestLoggingMiddleware.cs`
- `API/src/Infrastructure/Middleware/ResponseLoggingMiddleware.cs`
- `API/src/Infrastructure/HealthChecks/Startup.cs`
- `API/src/Host/Configurations/{httpssecurity,securityheaders,cors,ratelimit,exceptionhandling,middleware}.json`
