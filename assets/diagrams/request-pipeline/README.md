# API request / security pipeline

```mermaid
flowchart TD
  req[Incoming request]
  fwd[Forwarded headers]
  ex[Exception middleware]
  https[HSTS / HTTPS redirect]
  hdr[Security headers]
  files[Static files / file storage]
  route[Routing]
  cors[CORS]
  authn[Authentication]
  current[Current user]
  rl[Rate limiting]
  authz[Authorization]
  log[Request / response logging]
  hang[Hangfire dashboard]
  oai[OpenAPI]
  ep[Controllers require auth<br/>health + SignalR hubs]

  req --> fwd --> ex --> https --> hdr --> files --> route --> cors --> authn --> current --> rl --> authz --> log --> hang --> oai --> ep
```

## What this diagram shows

The **actual** `UseInfrastructure` order, then `MapEndpoints`.

## Why it matters

Security stages are ordered on purpose: exception handling wraps later failures; HSTS/HTTPS redirect run after the exception middleware; rate limiting runs **after** authentication so user-keyed partitions are possible.

## Key points

- Several stages are config-gated (forwarded headers, HSTS, rate limiting, request logging, Hangfire dashboard, OpenAPI).
- Controllers are mapped with `RequireAuthorization()`; anonymous is explicit.
- Exact rate-limit numbers are not shown.

## Evidence

- `API/src/Infrastructure/Startup.cs`
- `API/src/Infrastructure/Https/Startup.cs`
- `API/src/Infrastructure/RateLimiting/Startup.cs`

## Related documentation

- [API Security](../../../API/Security/README.md)
- [API overview](../../../API/README.md)
