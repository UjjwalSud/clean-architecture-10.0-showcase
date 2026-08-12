# API Multi-Tenancy

Tenant isolation is applied at **multiple layers**: JWT/session context, session validation, EF filters on Application data, manual Nexus filters, optional per-tenant Application databases, cache keys, and SignalR groups.

Related: [Authentication](../Authentication/README.md) · [Authorization](../Authorization/README.md) · [Persistence](../Persistence/README.md) · [Security](../Security/README.md)

Diagram: [Multi-tenant isolation layers](../../assets/diagrams/tenant-isolation-layers/README.md)

## Verified capabilities

- Tenant id and tenant unique id are issued into access-token claims at login.
- Current-user middleware exposes tenant accessors for the request scope.
- Session validation enforces session-tenant vs user-tenant match and inactive-tenant revocation (non-root).
- Application DbContext applies a global EF filter: auditable entities restricted to current tenant (+ soft-delete filter).
- Nexus/Identity DbContext does **not** use a global tenant EF filter; services apply explicit tenant predicates.
- Roles are physically tenant-scoped (see [Authorization](../Authorization/README.md)).
- Tenants may supply a dedicated Application connection string; initializer switches Application DB connection per tenant during migrate/seed.
- Cache keys default to tenant-unique-id prefixes.
- **Real-time tenant isolation** — SignalR connections join tenant-specific groups using the tenant unique id, enabling tenant-scoped real-time delivery.

## Isolation by layer

| Layer | Mechanism |
|-------|-----------|
| Token | Claims carry tenant id + tenant unique id (+ session id) |
| Session | Validate tenant match; revoke on mismatch / inactive tenant |
| Request ambient | `ICurrentUser` / `CurrentUserMiddleware` |
| Application DB | Global EF filter on `IAuditableEntity.TenantId` |
| Nexus DB | Soft-delete filter only; tenant isolation via query filters in services |
| Roles / permissions | Tenant-prefixed role names + tenant-filtered role APIs |
| Optional DB split | Per-tenant Application `ConnectionString` at initialization |
| Cache | `{tenantUniqueId}-â€¦` key prefix by default |
| Realtime | Tenant-specific SignalR groups |

This is intentionally **not** a single "multi-tenant middleware" feature — isolation depends on the correct combination of these layers.

## Tenant model & lifecycle notes

- Tenant entity includes unique id, admin email, active flag, optional connection string, and related subscription/payment fields.
- Root tenant is special-cased for inactive-tenant checks in login/session validation.
- Login rejects inactive non-root tenants before issuing tokens.
- Session capture stores tenant at session creation time for later mismatch detection.

## Evidence

- [`API/src/Infrastructure/Nexus/Identity/TokenService.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Nexus/Identity/TokenService.cs)
- [`API/src/Infrastructure/Auth/CurrentUser.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Auth/CurrentUser.cs)
- [`API/src/Infrastructure/Auth/CurrentUserMiddleware.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Auth/CurrentUserMiddleware.cs)
- [`API/src/Infrastructure/Auth/Jwt/ConfigureJwtBearerOptions.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Auth/Jwt/ConfigureJwtBearerOptions.cs)
- [`API/src/Infrastructure/Nexus/Identity/UserSessionService.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Nexus/Identity/UserSessionService.cs)
- [`API/src/Infrastructure/Persistence/Context/BaseDbContext.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Persistence/Context/BaseDbContext.cs)
- [`API/src/Infrastructure/Persistence/Context/Nexus/NexusBaseDbContext.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Persistence/Context/Nexus/NexusBaseDbContext.cs)
- [`API/src/Infrastructure/Nexus/MultiTenant/DbModels/Tenants.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Nexus/MultiTenant/DbModels/Tenants.cs)
- [`API/src/Infrastructure/Persistence/Initialization/ApplicationDbInitializer.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Persistence/Initialization/ApplicationDbInitializer.cs)
- [`API/src/Infrastructure/Persistence/Initialization/DatabaseInitializer.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Persistence/Initialization/DatabaseInitializer.cs)
- [`API/src/Infrastructure/Caching/CacheKeyService.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Caching/CacheKeyService.cs)
- [`API/src/Infrastructure/Notifications/NotificationHub.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Notifications/NotificationHub.cs)
- [`API/src/Infrastructure/Notifications/NotificationSender.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Notifications/NotificationSender.cs)
- [`API/src/Core/Shared/Authorization/SystemRoles.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Core/Shared/Authorization/SystemRoles.cs)
