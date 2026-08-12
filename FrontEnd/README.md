# FrontEnd Showcase

React SPA documentation for the product UI that consumes the Clean Architecture API: Vite + TypeScript, NSwag-generated clients, session-aware HTTP, Redux Toolkit + Saga, and module-based routing.

## Architecture overview

### Bootstrap

`FrontEnd/src/index.tsx` mounts:

1. Redux `Provider` (Auth + Layout store)
2. `BrowserRouter`
3. `App` → route composition + toast portal

### Major layers

| Area | Role |
|------|------|
| `src/routes/` | Public vs authenticated route modules and layout wrapping |
| `src/layouts/` | Default (public) and Vertical (authenticated shell) |
| `src/helpers/api/` | Session helpers, authenticated fetch, NSwag clients |
| `src/services/` | Thin wrappers over generated API clients |
| `src/redux/` | Auth workflows + layout chrome state |
| `src/pages/orbit/` | Product features (users, roles, tenants, appointments, forms, …) |
| `src/pages/{auth,ui,apps,…}/` | Auth screens and template/demo surfaces |
| `src/components/` / `src/hooks/` | Shared UI and hooks (permissions, idle timeout, …) |

### How the pieces connect

- **Routing** separates public and authenticated shells; authenticated access is gated by session helpers (UX), not by backend authorization.
- **API integration** uses NSwag Fetch clients wired to a shared `authenticatedFetch` that attaches Bearer tokens and handles 401 refresh/retry.
- **Authentication/session** stores the login payload in `sessionStorage` and aligns with the API's [session-bound JWT](../API/Authentication/README.md) model (backend still validates session on each request).
- **State** keeps Auth/Layout in Redux; tokens and session fields remain in `sessionStorage` via `APICore`.
- **Lookups / reference data** are consumed on demand via `DropDownService` (Application + Nexus types), not stored in Redux — see [State](./State/README.md) and [API Platform Foundation](../API/Platform-Foundation/README.md#centralized-reference-data-lookups).

## Technology & Libraries

See [Technology-Libraries.md](./Technology-Libraries.md).

## Sections

| Section | Focus |
|---------|--------|
| [Authentication](./Authentication/README.md) | Session storage, refresh single-flight, 401/403, idle warning, 2FA client |
| [Security](./Security/README.md) | sessionStorage, idle UX, DOMPurify; CSP docs vs hosting enforcement |
| [Routing](./Routing/README.md) | Public/protected routes, redirects, lazy loading, access UX |
| [State](./State/README.md) | Redux Toolkit store, Auth saga workflows, Layout state |
| [UI Architecture](./UI-Architecture/README.md) | Shared components, modals, toasts, module vs shared patterns |
| [Forms & Validation](./Forms-Validation/README.md) | VerticalForm/FormInput, yup vs server validation |
| [Grids & Tables](./Grids-Tables/README.md) | AG Grid wrappers, pagination, module column defs |
| [Tenant-Administration](./Tenant-Administration/README.md) | Root Manage Tenants grid + edit tabs |
| [Real-Time & Integrations](./RealTime-Integrations/README.md) | SignalR, Jitsi, Stripe, FullCalendar, social login |

Localization (API + FrontEnd) is documented under [API Localization](../API/Localization/README.md).

## Related API docs

- [API Authentication](../API/Authentication/README.md)
- [API Authorization](../API/Authorization/README.md)
- [API Multi-Tenancy](../API/MultiTenancy/README.md)
- [API Platform Foundation](../API/Platform-Foundation/README.md)
- [API Mailing](../API/Mailing/README.md)
- [API Security](../API/Security/README.md)

Diagrams: [Single-flight refresh](../assets/diagrams/frontend-single-flight-refresh/README.md) · [Routing / access gating](../assets/diagrams/frontend-routing-gating/README.md)

Screenshots: [Screenshots index](../assets/screenshots/README.md)

## Evidence (composition)

- `FrontEnd/src/index.tsx`
- `FrontEnd/src/App.tsx`
- `FrontEnd/src/routes/Routes.tsx`
- `FrontEnd/src/helpers/api/apiCore.ts`
- `FrontEnd/src/helpers/api/httpClient.ts`
- `FrontEnd/src/helpers/api/apiClients.ts`
- `FrontEnd/src/redux/store.ts`
- `FrontEnd/package.json`
