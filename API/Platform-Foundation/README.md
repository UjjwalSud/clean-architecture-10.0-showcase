# API Platform Foundation

Reusable SaaS-oriented building blocks in the private foundation: tenants, default/free subscription assignment, Stripe-backed upgrades, roles/permissions, and Root operator tooling.

This is not a claim of a complete commercial billing product. It documents the **platform pieces that already exist** so new applications can inherit multi-tenant SaaS structure instead of inventing it.

Related: [Multi-Tenancy](../MultiTenancy/README.md) · [Integrations](../Integrations/README.md) · [Authorization](../Authorization/README.md) · [FrontEnd Tenant Administration](../../FrontEnd/Tenant-Administration/README.md)

## Verified building blocks

| Building block | What exists |
|----------------|-------------|
| Tenants | Nexus tenant entity; seed Root; registration can create tenants; Root Manage Tenants UI |
| Isolation | Token/session/EF Application filter/Nexus service filters/cache keys/SignalR groups (see Multi-Tenancy) |
| Roles & permissions | Tenant-scoped roles; DB-backed permission policies |
| Default subscription | New tenants can receive a seeded free “registration” plan |
| Stripe path | Customer linkage, payment intents, webhooks assigning plans, invoice download |
| FrontEnd surfaces | Buy subscription plan, my-subscriptions, subscription status gating, Manage Tenants edit tabs |
| Settings (Orbit) | Tenant-scoped appointment/approved-appointment settings — product config, not billing catalog |
| Reference data (LookUps) | Dual catalogs: **Application** (Orbit/tenant DB) and **Nexus** (platform DB); typed codes + admin + dropdown APIs |

## Centralized reference data (LookUps)

Common dropdown/reference values are not reinvented per module. Two parallel catalogs share the same shape (code → values with display order / active / default) but live in different DbContexts:

| Catalog | Persistence | Typed dispatch | Admin API | Runtime dropdown API |
|---------|-------------|----------------|-----------|----------------------|
| **Application** | `ApplicationDbContext` (`LookUpCodes` / `LookUpCodeValues`) | `LookUpCodeTypes` | Versioned `LookUpController` + `ManageLookUps` permissions | `DataControllers` `drp-get-look-up-values` |
| **Nexus / platform** | `NexusDbContext` (`NexusLookUpCodes` / `NexusLookUpCodeValues`) | `NexusLookUpCodeTypes` | Versioned `NexusLookUpController` (`nexus-lookup`) + `ManageNexusLookUps` | `DataControllers` `drp-nexus-get-look-up-values` |

- Admin endpoints require Manage* permissions. Dropdown endpoints on `DataControllers` are authenticated (host default authorize) but **not** gated by ManageLookUps — modules can read values without admin rights.
- Application lookups inherit Application EF tenant filtering (auditable entities). Nexus dropdown cache uses a **global** cache key (`includeTenantId: false`) — platform shared values, not per-tenant packs.
- Nexus dropdown reads are cached (`ICacheService` / `CacheKeys.NexusLookUpValuesByCode`); create/update refreshes that cache. Application `LookUpService` dropdown reads hit the DB (no equivalent cache path verified).
- Seeded product usage today is thin: Nexus `UserTimeZone` is consumed in user/appointment UI; Application enum types include placeholders (`Temp` / `Temp1`) awaiting product-specific codes.

FrontEnd consumption: [State](../../FrontEnd/State/README.md) (`DropDownService`, not Redux).

## Lifecycle sketch (verified)

```text
Tenant created (seed or registration)
  → assign default/free subscription plan (when configured/seeded)
    → users + roles inside tenant
      → optional Stripe upgrade / webhook → TenantSubscriptionPlan + invoices
        → My Subscriptions / status screens; Root can inspect tenant subscriptions
```

## Honest limitations

- **Paid plan catalog** is not richly seeded out of the box; buy UI may show primarily the free active plan until plans exist and Stripe is configured.
- **No Host controller** for full subscription-catalog CRUD (service-level helpers may exist without a public admin API).
- Some buy-plan marketing bullets on the FrontEnd are **hardcoded**, not loaded from the database.
- Purchase CTA behavior can depend on current plan expiry rules.

## Evidence

- `API/src/Host/Controllers/MultiTenant/MultiTenantController.cs`
- `API/src/Host/Controllers/Subscription/`
- `API/src/Infrastructure/ExternalIntegrations/Stripe/`
- `API/src/Infrastructure/Nexus/Subscription/`
- `API/src/Infrastructure/Persistence/Initialization/`
- `FrontEnd/src/pages/orbit/manage-tenants/`
- `FrontEnd/src/pages/orbit/buy-subscription-plan/`
- `FrontEnd/src/pages/orbit/my-subscriptions/`
- `FrontEnd/src/pages/auth/SubscriptionStatus.tsx`
- `API/src/Host/Controllers/LookUp/LookUpController.cs`
- `API/src/Host/Controllers/LookUp/NexusLookUpController.cs`
- `API/src/Host/Controllers/DataControllers.cs`
- `API/src/Infrastructure/Orbit/LookUp/LookUpService.cs`
- `API/src/Infrastructure/Nexus/Lookup/NexusLookUpService.cs`
- `FrontEnd/src/services/DropDownService.ts`
- `FrontEnd/src/pages/orbit/manage-lookups/`
- `FrontEnd/src/pages/orbit/manage-nexus-lookups/`
