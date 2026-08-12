# FrontEnd Tenant Administration

Root operators manage tenants from an Orbit administration screen that complements the backend [multi-tenancy](../../API/MultiTenancy/README.md) model.

Related: [API Multi-Tenancy](../../API/MultiTenancy/README.md) · [API Platform Foundation](../../API/Platform-Foundation/README.md) · [Grids & Tables](../Grids-Tables/README.md)

## Verified capabilities

- **Manage Tenants** list at `/administrator/manage-tenants` (Root / Nexus Management menu).
- Shared AG Grid pattern: tenant name, admin email, Stripe customer id (when present), created date, Active/Inactive status, Actions.
- Filters: free-text search and Active/Inactive/All.
- **Edit tenant** landing with tabs: Tenant Details (name, email, isActive), Subscription Details (plan history / invoices), Tenant Users.
- Permissions: `Permissions.Tenants.View` / `Update` — catalog marks these as **Root-only**.
- APIs via `MultiTenantController` (search/get/update tenant, tenant users, tenant subscriptions, invoice download).

## Creation model (factual)

There is **no** in-app “Add Tenant” HTTP/UI path for operators. Tenants are created by **seed** (Root) and by **self-registration** flows that provision a tenant. Admin UI focuses on view/update and subscription/user inspection.

## Evidence

- `FrontEnd/src/pages/orbit/manage-tenants/`
- `FrontEnd/src/pages/orbit/manage-tenants/Components/EditTenantLandingPage.tsx`
- `FrontEnd/src/routes/orbitRoutes.tsx`
- `FrontEnd/src/pages/orbit/view-sub-menus/Components/AdministratorMenus.tsx`
- `API/src/Host/Controllers/MultiTenant/MultiTenantController.cs`
- `API/src/Core/Shared/Authorization/SystemPermissions.cs` (Tenants resource)
