# API Background Jobs

Background processing is implemented with Hangfire, SQL Server storage, request-context propagation into jobs, dashboard auth controls, and a daily session purge.

Diagram: [Hangfire job context](../../assets/diagrams/hangfire-job-context/README.md)

## Verified capabilities

- **Hangfire server + storage** — configured from Hangfire settings; storage provider currently implemented for SQL Server (`mssql`).
- **Job API** — `HangfireService` implements `IJobService` for enqueue / schedule / delete / requeue.
- **Tenant/user propagation** — `SystemJobFilter` captures user/tenant job parameters from the HTTP context; `SystemJobActivator` restores them into `ICurrentUserInitializer` when the job runs.
- **Job logging filter** — `LogJobFilter` logs create/perform/state/failure events.
- **Dashboard** — optional Hangfire dashboard with authorization modes (including Basic and network allowlists); enabled by environment-specific config (disabled in base `hangfire.json`, enabled in Development overlay).
- **Recurring job** — `purge-expired-user-sessions` runs daily and deletes aged session rows via `IUserSessionService.PurgeExpiredSessionsAsync`.
- **Readiness health check** — Hangfire storage connectivity/recurring-job readability is part of readiness checks.

## Configuration

- `API/src/Host/Configurations/hangfire.json`
- `API/src/Host/Configurations/hangfire.Development.json`

## Libraries

- `Hangfire`
- `Hangfire.Console`

## Evidence

- [`API/src/Infrastructure/BackgroundJobs/Startup.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/BackgroundJobs/Startup.cs)
- [`API/src/Infrastructure/BackgroundJobs/HangfireService.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/BackgroundJobs/HangfireService.cs)
- [`API/src/Infrastructure/BackgroundJobs/SystemJobFilter.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/BackgroundJobs/SystemJobFilter.cs)
- [`API/src/Infrastructure/BackgroundJobs/SystemJobActivator.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/BackgroundJobs/SystemJobActivator.cs)
- [`API/src/Infrastructure/BackgroundJobs/LogJobFilter.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/BackgroundJobs/LogJobFilter.cs)
- [`API/src/Infrastructure/BackgroundJobs/HangfireDashboardAuthorizationFilter.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/BackgroundJobs/HangfireDashboardAuthorizationFilter.cs)
- [`API/src/Infrastructure/Startup.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Startup.cs) (`UseHangfireDashboard`, `RegisterRecurringJobs`)
- [`API/src/Infrastructure/Nexus/Identity/UserSessionService.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/Nexus/Identity/UserSessionService.cs) (purge implementation)
- [`API/src/Infrastructure/HealthChecks/HangfireReadinessHealthCheck.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Infrastructure/HealthChecks/HangfireReadinessHealthCheck.cs)
- [`API/src/Core/Application/Common/Interfaces/IJobService.cs`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/API/src/Core/Application/Common/Interfaces/IJobService.cs)
