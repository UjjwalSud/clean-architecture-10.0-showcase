# API Showcase

Backend documentation for a .NET 10 Clean Architecture host: layered projects, config-driven infrastructure modules, and verified platform capabilities.

## Architecture overview

### Solution projects

| Project | Role |
|---------|------|
| Domain | Entities, enums, contracts such as auditable/soft-delete bases |
| Application | Service interfaces, request/response DTOs, common abstractions |
| Shared | Authorization constants, shared validation helpers |
| Infrastructure | Service implementations, EF Core, auth, jobs, caching, integrations |
| Host | Controllers, `Program.cs`, configuration loading |
| Migrators.MSSQL | EF Core migrations for SQL Server (shipped) |
| Migrators.PostgreSQL | Migrator project shell (provider wiring exists; migrations not shipped) |

Diagrams: [Solution project dependencies](../assets/diagrams/solution-project-dependencies/README.md) · [Overall Clean Architecture](../assets/diagrams/overall-clean-architecture/README.md)

**Project references** (from `.csproj` files):

```text
Shared              → (none)
Domain              → Shared
Application         → Domain, Shared
Infrastructure      → Application, Domain, Shared
Host                → Application, Infrastructure, Migrators.MSSQL
Migrators.MSSQL     → Infrastructure
Migrators.PostgreSQL → (none; not referenced by Host)
FrontEnd            → HTTP to Host only
```

### Composition root

`API/src/Host/Program.cs` boots the host by:

1. Loading configuration + registering Serilog
2. Adding controllers and calling `AddInfrastructure`
3. Running `InitializeDatabasesAsync` (migrate + seed)
4. Applying `UseInfrastructure` and `MapEndpoints`

Infrastructure modules are composed in `API/src/Infrastructure/Startup.cs` (`AddInfrastructure` / `UseInfrastructure` / `MapEndpoints`), including persistence, auth, Hangfire, caching, file storage, mailing, Stripe, Jitsi, notifications, OpenAPI, health checks, and related middleware.

### API versioning (how this host uses it)

Asp.Versioning is registered in Infrastructure startup:

- Default version **1.0**; **assume default when unspecified**; report versions; ApiExplorer substitutes version in URL (`'v'VVV`).
- Most controllers inherit **`VersionNeutralApiController`** → route `api/[controller]` (version-neutral).
- A smaller set (for example LookUp) inherits **`VersionedApiController`** → route `api/v{version:apiVersion}/[controller]`.
- OpenAPI/NSwag currently exposes a **single** document surface — versioning is present in the MVC stack; it is not a multi-document versioned Swagger catalog.

See [OpenAPI / NSwag](./OpenApi-NSwag/README.md) and [Technology & Libraries](./Technology-Libraries.md).

### SaaS / platform foundation

Reusable tenant + subscription-oriented building blocks (default/free plan assignment, Stripe upgrade path, Root Manage Tenants) are summarized in [Platform Foundation](./Platform-Foundation/README.md).

### Centralized reference data (LookUps)

Application (Orbit) and Nexus (platform) lookup catalogs provide typed reference values for dropdowns and admin maintenance so modules do not each invent their own lookup tables. Admin vs dropdown surfaces, caching, and tenant/global scope are documented under [Platform Foundation — LookUps](./Platform-Foundation/README.md#centralized-reference-data-lookups).

### Controllers and services

- Controllers live under `API/src/Host/Controllers/` and stay thin: they call Application service interfaces.
- Implementations live under `API/src/Infrastructure/` (Orbit business modules and Nexus platform modules) and use DbContexts directly (no repository layer in active DI).

### Configuration style

Feature settings are split JSON files under `API/src/Host/Configurations/` (for example `database.json`, `hangfire.json`, `cache.json`, `fileStorage.json`, `stripe.json`, `openapi.json`, `externalIntegrations.json`), loaded by `API/src/Host/Configurations/Startup.cs`.

## Technology & Libraries

See [Technology-Libraries.md](./Technology-Libraries.md) for library → purpose → capability mappings.

Highlights documented there include EF Core, Hangfire, Serilog, NSwag, JWT bearer, Stripe, Jitsi (JWT package), caching abstractions, and multi-provider file storage (local / AWS S3 / Azure Blob).

## Sections in this folder

| Section | Focus |
|---------|--------|
| [Security](./Security/README.md) | HTTPS/HSTS, headers, CORS, rate limiting, exception boundary, optional external secrets |
| [Authentication](./Authentication/README.md) | Session-bound JWT, refresh rotation/reuse, idle/absolute timeouts, 2FA/authenticator |
| [Authorization](./Authorization/README.md) | Permissions, dynamic policies, role claims, tenant-scoped roles |
| [MultiTenancy](./MultiTenancy/README.md) | Multi-layer tenant isolation (token, session, EF, Nexus, cache, SignalR) |
| [Platform-Foundation](./Platform-Foundation/README.md) | Tenants, subscriptions, Stripe upgrade path, Root admin, Application/Nexus lookups |
| [Observability](./Observability/README.md) | Serilog, security events, audit redaction, ErrorId, health checks |
| [Persistence](./Persistence/README.md) | Multi-DbContext layout, soft delete, auditing, migrators |
| [BackgroundJobs](./BackgroundJobs/README.md) | Hangfire storage, filters, recurring purge job |
| [Caching](./Caching/README.md) | Local/distributed cache + tenant-prefixed keys |
| [FileStorage](./FileStorage/README.md) | Provider selection + upload validation |
| [Mailing](./Mailing/README.md) | Multi-provider outbound email, Hangfire enqueue, logs/templates |
| [Integrations](./Integrations/README.md) | Stripe payments + Jitsi meetings |
| [Notifications](./Notifications/README.md) | SignalR hub + tenant groups |
| [Localization](./Localization/README.md) | Country + string catalog API; FrontEnd i18next runtime |
| [OpenApi-NSwag](./OpenApi-NSwag/README.md) | OpenAPI document + Swagger UI (+ versioning notes) |

## Evidence (composition)

- `API/src/Host/Program.cs`
- `API/src/Infrastructure/Startup.cs`
- `API/src/Host/Configurations/Startup.cs`
- `.cursor/docs/01_SYSTEM_ARCHITECTURE.md` (architecture reference used by the private foundation)

Diagrams: [Overall Clean Architecture](../assets/diagrams/overall-clean-architecture/README.md) · [Request pipeline](../assets/diagrams/request-pipeline/README.md) · [all diagrams](../assets/diagrams/README.md)

Screenshots: [Screenshots index](../assets/screenshots/README.md)
