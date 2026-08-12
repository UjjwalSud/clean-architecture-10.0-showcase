# FrontEnd routing / access gating

```mermaid
flowchart TD
  hit[Route match]
  pub{Public route list?}
  def[DefaultLayout]
  sess{APICore authenticated?}
  vert[VerticalLayout page]
  login[Redirect login]
  unauth[Redirect unauthorized logout]
  page[Page may call API]
  api{API result}
  ok[Render data]
  denied[403 to AccessDenied]
  note[Menu usePermission is UX only]

  hit --> pub
  pub -->|yes| def
  pub -->|no| sess
  sess -->|yes| vert --> page --> api
  sess -->|refresh still valid? no| unauth
  sess -->|no session| login
  api -->|authorized| ok
  api -->|403| denied
  vert -.-> note
```

## What this diagram shows

The **current** composition in `Routes.tsx`: public vs authenticated lists, session helper gate, then API as the real permission check.

## Why it matters

This is navigation UX, not a security boundary. `PrivateRoute` and per-route `roles` metadata exist in source but are **not mounted** here.

## Key points

- Authenticated = valid access JWT **or** still-valid refresh expiry (then 401 refresh may run).
- Expired refresh → unauthorized logout redirect.
- Menu/button `usePermission` hides UI; deep links can still render until the API returns 403.
- Backend [authorization](../../../API/Authorization/README.md) remains authoritative.

## Evidence

- `FrontEnd/src/routes/Routes.tsx`
- `FrontEnd/src/helpers/api/apiCore.ts`
- `FrontEnd/src/helpers/api/httpClient.ts`
- `FrontEnd/src/routes/PrivateRoute.tsx`

## Related documentation

- [FrontEnd Routing](../../../FrontEnd/Routing/README.md)
- [FrontEnd Authentication](../../../FrontEnd/Authentication/README.md)
- [API Authorization](../../../API/Authorization/README.md)
