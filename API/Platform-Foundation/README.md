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
