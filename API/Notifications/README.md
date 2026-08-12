# API Notifications

Real-time notifications use ASP.NET Core SignalR with an authorized hub and tenant-scoped groups. Outbound email is a separate pipeline — see [Mailing](../Mailing/README.md).

## Verified capabilities

- **SignalR registration** — `AddSignalR()` and hub mapping at `/notifications`.
- **Authorized hub** — `NotificationHub` requires authentication; connections join/leave `GroupTenant-{tenantUniqueId}`.
- **Sender abstractions** — `NotificationSender` can target all, tenant group, named groups, and users.
- **Client method** — server pushes via `NotificationFromServer`.
- **Auth expiry behavior** — hub mapping uses `CloseOnAuthenticationExpiration`.
- **CORS support** — CORS configs allow SignalR user-agent header for client connections.
- **Framework-based** — SignalR comes from the ASP.NET Core shared framework (no separate SignalR NuGet in Infrastructure).

## Configuration notes

- There is no active dedicated `signalr.json` load in Host configuration startup (signalr config load is commented out).
- Hub wiring is code-driven from Notifications startup / endpoint mapping.

## Evidence

- `API/src/Infrastructure/Notifications/Startup.cs`
- `API/src/Infrastructure/Notifications/NotificationHub.cs`
- `API/src/Infrastructure/Notifications/NotificationSender.cs`
- `API/src/Core/Application/Common/Interfaces/INotificationSender.cs`
- `API/src/Core/Shared/Notifications/INotificationMessage.cs`
- `API/src/Infrastructure/Startup.cs` (`MapNotifications`)
- `API/src/Host/Configurations/cors.json` / `cors.Development.json`
