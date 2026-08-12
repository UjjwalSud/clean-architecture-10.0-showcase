# Access Denied

![Access Denied](./access-denied.png)

## What this shows

The product **Access Denied** page (403 UX): padlock treatment, explanation text, and Back to Home.

## Capability demonstrated

FrontEnd authorization UX corresponding to denied access. `authenticatedFetch` navigates here on HTTP 403. This page is **not** the security boundary — API permission policies authorize every protected request.

## Evidence

- `FrontEnd/src/pages/error/AccessDenied` (lazy route)
- `FrontEnd/src/routes/authRoutes.tsx` (`/access-denied`)
- `FrontEnd/src/helpers/api/httpClient.ts` (403 → `/access-denied`)
- `API/src/Infrastructure/Auth/Permissions/PermissionAuthorizationHandler.cs`

## Related documentation

- [FrontEnd Routing](../../../FrontEnd/Routing/README.md)
- [API Authorization](../../../API/Authorization/README.md)
- [FrontEnd routing / access gating diagram](../../diagrams/frontend-routing-gating/README.md)
