# API Persistence

EF Core persistence uses three DbContexts, provider switching, soft delete, change auditing, and startup migrate/seed.

Diagrams: [Audit trail + soft delete](../../assets/diagrams/audit-soft-delete/README.md) · [Multi-tenant isolation](../../assets/diagrams/tenant-isolation-layers/README.md)

## Verified capabilities

- **Three-database layout** — Nexus (identity/platform), Application (tenant business data), Auditing (change trails), each with its own provider/connection settings.
- **Provider selection** — `"mssql"` and `"postgresql"` are wired in persistence startup; default config uses SQL Server.
- **Soft delete** — entities implementing `ISoftDelete` convert `Deleted` to `Modified` with `IsDeleted` / deleted metadata instead of hard delete.
- **Audit field stamps** — `IAuditableEntity` gets created/modified metadata on save.
- **Change trails** — Create/Update/Delete trails are written to `AuditingDbContext`, with sensitive property redaction.
- **Global query filters** — soft-delete filter on applicable contexts; **tenant filter on Application (`BaseDbContext`) only**.
- **Startup migrate + seed** — pending migrations applied; Nexus platform seed + per-tenant Application seed.
- **Per-tenant Application connection** — tenant connection string can override Application DB during initialization.
- **Migrator packaging** — MSSQL migrations are shipped; PostgreSQL migrator project is an empty shell; Host references MSSQL migrator.

## Context ownership (high level)

| Context | Owns |
|---------|------|
| `ApplicationDbContext` | Orbit business entities (forms, appointments, email logs/templates, lookups, settings, â€¦) |
| `NexusDbContext` | Identity + sessions, tenants, subscriptions, Nexus lookups, localization, Stripe payment initialization |
| `AuditingDbContext` | `Trail` / audit trail store (`Auditing` schema) |

## Configuration

- Settings model: `API/src/Infrastructure/Persistence/DatabaseSettings.cs`
- Config file: `API/src/Host/Configurations/database.json`

## Evidence

- [`API/src/Infrastructure/Persistence/Startup.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Persistence/Startup.cs)
- [`API/src/Infrastructure/Persistence/Context/ApplicationDbContext.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Persistence/Context/ApplicationDbContext.cs)
- [`API/src/Infrastructure/Persistence/Context/BaseDbContext.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Persistence/Context/BaseDbContext.cs)
- [`API/src/Infrastructure/Persistence/Context/Nexus/NexusDbContext.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Persistence/Context/Nexus/NexusDbContext.cs)
- [`API/src/Infrastructure/Persistence/Context/Nexus/NexusBaseDbContext.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Persistence/Context/Nexus/NexusBaseDbContext.cs)
- [`API/src/Infrastructure/Persistence/Context/Auditing/AuditingDbContext.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Persistence/Context/Auditing/AuditingDbContext.cs)
- [`API/src/Core/Domain/Common/Contracts/ISoftDelete.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Core/Domain/Common/Contracts/ISoftDelete.cs)
- [`API/src/Core/Domain/Common/Contracts/IAuditableEntity.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Core/Domain/Common/Contracts/IAuditableEntity.cs)
- [`API/src/Infrastructure/Auditing/AuditTrail.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Auditing/AuditTrail.cs)
- [`API/src/Infrastructure/Auditing/Trail.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Auditing/Trail.cs)
- [`API/src/Infrastructure/Persistence/Initialization/DatabaseInitializer.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Persistence/Initialization/DatabaseInitializer.cs)
- [`API/src/Infrastructure/Persistence/Initialization/ApplicationDbInitializer.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Persistence/Initialization/ApplicationDbInitializer.cs)
- [`API/src/Infrastructure/Persistence/Initialization/ApplicationDbSeeder.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Persistence/Initialization/ApplicationDbSeeder.cs)
- [`API/src/Migrators/Migrators.MSSQL/`](https://github.com/UjjwalSud/clean-architecture-10.0/tree/main/API/src/Migrators/Migrators.MSSQL/)
- [`API/src/Migrators/Migrators.PostgreSQL/`](https://github.com/UjjwalSud/clean-architecture-10.0/tree/main/API/src/Migrators/Migrators.PostgreSQL/)
- [`API/src/Host/Program.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Host/Program.cs)
