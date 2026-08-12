# API Caching

Caching is config-selected between local memory cache and distributed cache, with tenant-aware cache key generation.

## Verified capabilities

- **Local cache** — when distributed cache is off, `LocalCacheService` uses `IMemoryCache`.
- **Distributed cache** — when enabled, `DistributedCacheService` uses `IDistributedCache` plus serializer; with Redis preference off, registration uses `AddDistributedMemoryCache()`.
- **Redis note** — Redis registration code is present but commented out; no Redis package is active in Infrastructure.
- **Tenant-prefixed keys** — `CacheKeyService` builds keys as `{tenantUniqueId}-{CacheKey}-{id}` (or `GLOBAL-â€¦` when tenant inclusion is disabled).
- **Shared contract** — Application defines `ICacheService` / `ICacheKeyService` and cache key names such as permissions and Nexus lookup values.
- **Default sliding expiry** — local/distributed implementations use a short default sliding window (10 minutes in current services).

## Configuration

- Settings: `API/src/Infrastructure/Caching/CacheSettings.cs`
- Config file: `API/src/Host/Configurations/cache.json`

## Evidence

- `API/src/Infrastructure/Caching/Startup.cs`
- `API/src/Infrastructure/Caching/LocalCacheService.cs`
- `API/src/Infrastructure/Caching/DistributedCacheService.cs`
- `API/src/Infrastructure/Caching/CacheKeyService.cs`
- `API/src/Core/Application/Common/Caching/ICacheService.cs`
- `API/src/Core/Application/Common/Caching/ICacheKeyService.cs`
- `API/src/Core/Application/Common/Caching/CacheKeys.cs`
- `API/src/Host/Configurations/cache.json`
