# Multi-tenant isolation layers

```mermaid
flowchart TB
  subgraph tokenLayer [Token]
    jwt[JWT tenant id + unique id + sid]
  end
  subgraph sessionLayer [Session]
    sess[Validate tenant match and tenant active]
  end
  subgraph requestLayer [Request]
    cu[CurrentUser tenant accessors]
  end
  subgraph appDb [Application DB]
    efg[EF global filter TenantId]
  end
  subgraph nexusDb [Nexus DB]
    svc[Service-level tenant predicates]
    roles[Tenant-prefixed roles]
  end
  subgraph optionalDb [Optional]
    cs[Per-tenant Application connection]
  end
  subgraph cacheRt [Cache and realtime]
    keys[Tenant-prefixed cache keys]
    groups[Tenant-specific SignalR groups]
  end

  jwt --> sess --> cu
  cu --> efg
  cu --> svc
  cu --> roles
  cu --> cs
  cu --> keys
  cu --> groups
```

## What this diagram shows

Tenant isolation is **several cooperating layers**, not one middleware feature.

## Why it matters

Application data uses an EF global tenant filter. Nexus/identity data does **not** — services filter by tenant explicitly. Optional per-tenant Application connection strings can split databases.

## Key points

- JWT carries tenant context; session validation can revoke on mismatch or inactive tenant (non-root).
- Cache keys default to tenant unique id prefixes.
- Realtime delivery uses tenant-specific SignalR groups.

## Evidence

- `API/src/Infrastructure/Nexus/Identity/TokenService.cs`
- `API/src/Infrastructure/Persistence/Context/BaseDbContext.cs`
- `API/src/Infrastructure/Persistence/Context/Nexus/NexusBaseDbContext.cs`
- `API/src/Infrastructure/Caching/CacheKeyService.cs`
- `API/src/Infrastructure/Notifications/NotificationHub.cs`

## Related documentation

- [API Multi-Tenancy](../../../API/MultiTenancy/README.md)
- [API Persistence](../../../API/Persistence/README.md)
- [API Notifications](../../../API/Notifications/README.md)
