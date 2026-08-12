# Session Timeout

![Session timeout warning](./session-timeout.png)

## What this shows

The real idle-session warning modal: **Session expiring soon**, countdown, **Continue** (refresh session), and **Log out**.

Captured against the authenticated layout while Manage Users is in the background.

## Capability demonstrated

UI evidence for session-bound authentication: the client tracks inactivity using idle/warning minutes returned with the token session and prompts before forced logout. Continue refreshes tokens; Log out revokes the server session.

## Evidence

- `FrontEnd/src/components/SessionTimeoutModal.tsx`
- `FrontEnd/src/hooks/useIdleSessionTimeout.ts`
- `FrontEnd/src/layouts/Vertical.tsx`
- `API/src/Infrastructure/Nexus/Identity/UserSessionService.cs`

## Related documentation

- [FrontEnd Authentication](../../../FrontEnd/Authentication/README.md)
- [API Authentication](../../../API/Authentication/README.md)
- [Session lifecycle diagram](../../diagrams/session-lifecycle/README.md)
