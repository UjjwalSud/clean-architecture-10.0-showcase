# FrontEnd State

Global client state uses **Redux Toolkit's store** with **redux-saga** for auth side effects. The store is intentionally small: **Auth** and **Layout** only. Tokens/session fields live in `sessionStorage` (see [Authentication](../Authentication/README.md)), not in a persisted Redux store.

Related: [Authentication](../Authentication/README.md) · [Routing](../Routing/README.md) · [Technology & Libraries](../Technology-Libraries.md)

## Verified capabilities

- `configureStore` registers `Auth` + `Layout` reducers and saga middleware.
- Root saga runs the auth saga only.
- No redux-persist — Redux Auth rehydrates from session + API calls on boot.
- Auth saga covers login, social login, 2FA verify/email-code, logout, signup, forgot/reset/confirm-mail flows.
- Successful login/2FA writes session via `APICore.setLoggedInUser`, then loads profile/permissions into Redux.
- Auth state holds token mirror, profile, permission string list, subscription plan snapshot, 2FA challenge flags, and UI loading/error flags.
- Layout state holds theme/direction/sidebar chrome (optionally seeded from URL query params).
- Permission hooks read `Auth.userPermissions` for UI gating (menus/buttons), not for Redux-persisted ACLs.
- `serializableCheck` is disabled on the store (pragmatic for current auth payloads).

## Store shape

```text
RootState {
  Auth: {
    userTokenData, userData, userPermissions,
    tenantSubscriptionPlan,
    requiresTwoFactor, twoFactorSessionId,
    loading/error/flow flags...
  }
  Layout: {
    theme / direction / width / sidebar / topbar / ...
  }
}
```

## Auth workflows (actual)

| Flow | Behavior |
|------|----------|
| Login | Call auth API → if 2FA required, set challenge flags; else persist session + LOGIN success + fetch profile/permissions |
| 2FA verify | Verify → persist session + treat as logged in + fetch profile/permissions |
| Logout | Call logout API when session present → clear session → broadcast logout signal → clear Redux user |
| Boot init | Auth reducer initialization loads profile/permissions when already authenticated (skips on logout route) |

Server/API domain data for Orbit screens is generally fetched in page/services code via NSwag clients; it is **not** mirrored into additional Redux slices in this architecture.

## Centralized lookups (not Redux)

Reference-data dropdowns use **`DropDownService`** over `DataControllers` (`getLookUpCodeValues` / `getNexusLookUpCodeValues`), keyed by `LookUpCodeTypes` / `NexusLookUpCodeTypes`. Values are loaded **on demand** in forms (users, profile, appointments) — not preloaded into Redux after login.

Admin catalogs (`LookUpService` / `NexusLookUpService` + Manage Lookups / Manage Nexus Lookups pages) use the versioned LookUp APIs and Manage* permissions. Runtime dropdown calls do not require those admin permissions.

API/platform detail: [Platform Foundation — LookUps](../../API/Platform-Foundation/README.md#centralized-reference-data-lookups).

## What is not in Redux

| Concern | Location |
|---------|----------|
| Access/refresh tokens & session fields | `sessionStorage` via `APICore` |
| Silent 401 refresh | `httpClient` / `sessionRefreshQueue` |
| Idle activity timer | `useIdleSessionTimeout` (layout) |
| Cross-tab logout signal | `localStorage` timestamp |
| Lookup / dropdown reference data | `DropDownService` + page-local fetch (not Auth/Layout) |

## Evidence

- `FrontEnd/src/redux/store.ts`
- `FrontEnd/src/redux/sagas.ts`
- `FrontEnd/src/redux/auth/saga.ts`
- `FrontEnd/src/redux/auth/reducers.ts`
- `FrontEnd/src/redux/auth/actions.ts`
- `FrontEnd/src/redux/layout/reducers.ts`
- `FrontEnd/src/helpers/api/apiCore.ts`
- `FrontEnd/src/hooks/usePermission.ts`
- `FrontEnd/src/services/DropDownService.ts`
- `FrontEnd/src/services/LookUpService.ts`
- `FrontEnd/src/services/NexusLookUpService.ts`
- `FrontEnd/src/pages/orbit/manage-lookups/`
- `FrontEnd/src/pages/orbit/manage-nexus-lookups/`
