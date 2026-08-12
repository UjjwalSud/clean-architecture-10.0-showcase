# Manage Tenants

![Manage Tenants](./manage-tenants.png)

## What this shows

Root **Manage Tenants** grid: tenant name, admin email, Stripe customer id (when present), created date, Active status, and Actions — with search/status filters and pagination.

Visible rows use **demo tenants** (`*.example.com`) plus Root (`admin@root.example` sanitized for public capture). Stripe ids shown are `cus_demo_*` placeholders, not real customers.

## Capability demonstrated

Root-only tenant administration over the multi-tenant platform model.

## Evidence

- `FrontEnd/src/pages/orbit/manage-tenants/`
- `API/src/Host/Controllers/MultiTenant/MultiTenantController.cs`

## Related documentation

- [FrontEnd Tenant Administration](../../../FrontEnd/Tenant-Administration/README.md)
- [API Multi-Tenancy](../../../API/MultiTenancy/README.md)
- [API Platform Foundation](../../../API/Platform-Foundation/README.md)
