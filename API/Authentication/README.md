# API Authentication

Authentication is **session-bound JWT**, not purely stateless tokens. Access tokens carry a session id; nearly every authenticated request re-validates a database session for idle/absolute timeout, user/tenant health, and revocation.

Related: [Authorization](../Authorization/README.md) · [Multi-Tenancy](../MultiTenancy/README.md) · [Security](../Security/README.md) · [Observability](../Observability/README.md)

Diagrams:

- [JWT + session validation](../../assets/diagrams/jwt-session-validation/README.md)
- [Refresh-token rotation](../../assets/diagrams/refresh-token-rotation/README.md)
- [Session lifecycle](../../assets/diagrams/session-lifecycle/README.md)

## Verified capabilities

- JWT issuance with HMAC signing, issuer/audience, and zero clock skew on validation.
- Session creation at login/token issuance with absolute expiry and session type (client vs internal).
- `OnTokenValidated` DB session validation on authenticated requests (with explicit logout path exemptions).
- Refresh-token storage as **hash only** (SHA-256), constant-time compare, and **rotation** on each successful refresh.
- Refresh **reuse detection** revokes the session; multi-tab concurrency conflicts reject without revoking the winning rotation.
- Idle and absolute session timeouts independent of JWT `exp` / refresh TTL.
- Logout / logout-all: AllowAnonymous endpoints that accept expired/idle JWTs (signature still validated) so clients can revoke after timeout.
- Optional 2FA challenge before token issuance; social login path (Facebook/Google) with auto-register then token flow.
- Security event logging for login, 2FA, refresh, logout, session create/revoke, and related outcomes.
- Account gates: active user, optional email confirmation, lockout, tenant active (root special-cased).

## Lifecycle

### 1. Login / token issuance

`POST /api/tokens` (AllowAnonymous, authentication rate-limit policy):

1. Resolve user by email; validate password (or social path); apply lockout / active / confirmation / tenant checks.
2. If 2FA is required for the user → create a two-factor session and return a challenge (no real JWT yet).
3. Otherwise create an `ApplicationUserSession`, issue access JWT + refresh token, store only the refresh **hash** on the session.

JWT claims include user identity, tenant id + tenant unique id, IP, and **session id (`sid`)**. Role/permission claims are **not** embedded in the access token (authorization loads permissions from the DB — see [Authorization](../Authorization/README.md)).

### 2. Authenticated request validation

`ConfigureJwtBearerOptions`:

- Validates JWT with strict clock skew (`TimeSpan.Zero`).
- For SignalR `/notifications`, accepts token from query `access_token`.
- On successful signature validation, loads `sid` + user id and calls `ValidateSessionAsync` (activity update skipped for `/notifications`).
- Session validation failures fail authentication ("session invalid").
- Logout and logout-all paths **skip** session validation so revocation can still run.

### 3. Session validation rules

`UserSessionService.ValidateSessionAsync` checks, in order:

1. Session exists and matches user
2. Not already revoked
3. User exists and is active
4. Session tenant matches current user tenant (mismatch → revoke `TenantMismatch`)
5. Non-root tenant is active (else revoke `TenantInactive`)
6. Absolute timeout (else revoke `AbsoluteTimeout`)
7. Idle timeout by session type (client vs internal idle windows) (else revoke `IdleTimeout`)
8. Optionally bump last-activity (throttled)

### 4. Refresh rotation

`POST /api/tokens/refresh` (AllowAnonymous, refresh rate-limit policy):

1. Accepts an access token with **lifetime validation disabled** (expired access token allowed).
2. Re-validates the session (without extending idle activity).
3. Verifies presented refresh token against stored hash.
4. On mismatch → treat as reuse → revoke session (`RefreshTokenReuse`).
5. On success → issue new access + refresh pair on the same session row (rotation) and bump concurrency token.
6. On EF concurrency conflict → reject refresh without revoking (safe multi-tab race).

### 5. Logout

`POST /api/tokens/logout` and `logout-all-devices`:

- AllowAnonymous; bearer middleware skips session validation for these paths.
- Token signature/issuer/audience validated with lifetime ignored.
- Revokes current session or all sessions; clears refresh hash.

### 6. Session purge

Daily Hangfire job purges aged session rows (retention cleanup) — see [Background Jobs](../BackgroundJobs/README.md).

## UI evidence

Client idle warning corresponding to session-bound auth (Continue refreshes; Log out revokes):

![Session timeout](../../assets/screenshots/session-timeout/session-timeout.png)

See [session-timeout screenshot](../../assets/screenshots/session-timeout/README.md) · [FrontEnd Authentication](../../FrontEnd/Authentication/README.md).

## Revocation reasons (constants)

Documented reason codes include logout, logout-all, password change/reset, user deactivation, tenant inactive/mismatch, idle/absolute timeout, and refresh-token reuse (`UserSessionRevocationReasons`).

## Configuration (shape only)

- `API/src/Host/Configurations/security.json` — `SecuritySettings` (provider, JWT settings shape, 2FA flags), `SessionSecurity`, lockout/password-policy sections.
- Development overlay can shorten access-token lifetime for local testing.
- JWT signing key must be supplied by environment/secrets for startup; do not publish key material.

## Evidence

- [`API/src/Infrastructure/Auth/Jwt/ConfigureJwtBearerOptions.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Auth/Jwt/ConfigureJwtBearerOptions.cs)
- [`API/src/Infrastructure/Auth/Jwt/Startup.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Auth/Jwt/Startup.cs)
- [`API/src/Infrastructure/Auth/Jwt/JwtSettings.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Auth/Jwt/JwtSettings.cs)
- [`API/src/Infrastructure/Auth/SessionSecuritySettings.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Auth/SessionSecuritySettings.cs)
- [`API/src/Infrastructure/Nexus/Identity/TokenService.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Nexus/Identity/TokenService.cs)
- [`API/src/Infrastructure/Nexus/Identity/RefreshTokenHasher.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Nexus/Identity/RefreshTokenHasher.cs)
- [`API/src/Infrastructure/Nexus/Identity/UserSessionService.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Nexus/Identity/UserSessionService.cs)
- [`API/src/Infrastructure/Nexus/Identity/DbModels/ApplicationUserSession.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Nexus/Identity/DbModels/ApplicationUserSession.cs)
- [`API/src/Core/Application/Nexus/Identity/Sessions/UserSessionRevocationReasons.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Core/Application/Nexus/Identity/Sessions/UserSessionRevocationReasons.cs)
- [`API/src/Host/Controllers/Identity/TokensController.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Host/Controllers/Identity/TokensController.cs)
- [`API/src/Host/Configurations/security.json`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Host/Configurations/security.json)
- [`API/src/Infrastructure/Logging/Security/SecurityEventNames.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Logging/Security/SecurityEventNames.cs)
