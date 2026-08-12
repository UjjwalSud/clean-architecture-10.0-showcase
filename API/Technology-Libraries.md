# API Technology & Libraries

Explicit mapping of major backend packages to purpose and verified capability. Package versions are taken from `API/src/Infrastructure/Infrastructure.csproj` and `API/src/Host/Host.csproj` unless noted.

## Persistence & identity

| Library | Purpose | Capability enabled |
|---------|---------|-------------------|
| `Microsoft.EntityFrameworkCore.SqlServer` | EF Core SQL Server provider | Primary persistence provider used by shipped migrations |
| `Npgsql.EntityFrameworkCore.PostgreSQL` | EF Core PostgreSQL provider | Alternate provider selectable via database settings |
| `Microsoft.AspNetCore.Identity.EntityFrameworkCore` | Identity stores on EF | Users, roles, claims persistence in Nexus DB |
| `Microsoft.EntityFrameworkCore.Tools` / `Design` | EF tooling | Migration generation and design-time support |

## Auth & security building blocks

| Library | Purpose | Capability enabled |
|---------|---------|-------------------|
| `Microsoft.AspNetCore.Authentication.JwtBearer` | JWT bearer authentication | API token authentication scheme |
| `JWT` | JWT create/verify helper | Jitsi meeting token generation |
| `Otp.NET` / `QRCoder` | TOTP + QR encoding | Authenticator-style 2FA support surfaces |

## Jobs, logging, API docs

| Library | Purpose | Capability enabled |
|---------|---------|-------------------|
| `Hangfire` / `Hangfire.Console` | Background job processing | Enqueue/schedule jobs, dashboard, console logging, recurring purge |
| `Serilog.AspNetCore` + Serilog sinks/enrichers | Structured logging | Host logging with console/file and optional Elastic/MSSQL/Seq sinks |
| `NSwag.AspNetCore` | OpenAPI generation + UI | Swagger document and Swagger UI |
| `ZymLabs.NSwag.FluentValidation.AspNetCore` | FluentValidation → OpenAPI | Schema processor integration for validators |

## Mapping, HTTP, mail, storage, payments

| Library | Purpose | Capability enabled |
|---------|---------|-------------------|
| `Mapster.Core` / `Mapster.DependencyInjection` | Object mapping | DTO/entity mapping in Infrastructure services |
| `RestSharp` | HTTP client | External HTTP calls (for example Stripe invoice PDF download) |
| `Newtonsoft.Json` | JSON serialization | Selected serialization paths (including Stripe-related payloads) |
| `MailKit` | SMTP client | Mail provider path when configured for AWS-style SMTP |
| `SendGrid` | SendGrid API | Mail provider path when `Provider=SendGrid` |
| `Resend` | Resend API | Mail provider path when `Provider=Resend` |
| `AWSSDK.S3` | AWS S3 | File storage when provider is AWS |
| `Azure.Storage.Blobs` / `Azure.Identity` | Azure Blob (+ managed identity) | File storage when provider is Azure |
| `Stripe.net` | Stripe API client | Payment intents, subscriptions, webhooks, invoices |

## Host-only configuration providers

| Library | Purpose | Capability enabled |
|---------|---------|-------------------|
| `Amazon.Extensions.Configuration.SystemsManager` | AWS config/secrets | Optional external secrets provider |
| `Azure.Extensions.AspNetCore.Configuration.Secrets` | Azure Key Vault config | Optional external secrets provider |

## Framework capabilities (no separate NuGet)

| Capability | Source | Capability enabled |
|------------|--------|-------------------|
| SignalR | `Microsoft.AspNetCore.App` | Real-time notifications hub at `/notifications` |
| Rate limiting | `Microsoft.AspNetCore.RateLimiting` | Named policies (auth, refresh, webhook, etc.) from `ratelimit.json` |
| API versioning | `Asp.Versioning.Mvc` (+ ApiExplorer) | Default API version `1.0`, URL version substitution |

## Notes (verified limits)

- Redis cache wiring exists as commented code; no Redis package is active in `Infrastructure.csproj`. Distributed cache currently falls back to in-memory distributed cache when enabled.
- PostgreSQL EF provider is wired, but `Migrators.PostgreSQL` has no migration assets and Host references `Migrators.MSSQL`.
- Application/Shared/Domain projects do not carry the primary feature NuGet packages; those live mainly in Infrastructure/Host.

## Evidence

- `API/src/Infrastructure/Infrastructure.csproj`
- `API/src/Host/Host.csproj`
- `API/src/Infrastructure/Startup.cs`
- Module Startups under `API/src/Infrastructure/{Persistence,BackgroundJobs,Caching,FileStorage,OpenApi,Notifications,ExternalIntegrations}/`
