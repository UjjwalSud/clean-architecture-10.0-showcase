# Architecture diagrams

Editable Mermaid sources for verified architecture and runtime flows. Each folder contains `diagram.mmd` plus a short README that GitHub can render.

These diagrams explain **one idea each**. Linked feature pages hold the deeper evidence.

## Inventory

| Diagram | Primary idea |
|---------|----------------|
| [Overall Clean Architecture](./overall-clean-architecture/README.md) | Layered solution shape |
| [Solution project dependencies](./solution-project-dependencies/README.md) | Exact `ProjectReference` graph |
| [Request / security pipeline](./request-pipeline/README.md) | Actual `UseInfrastructure` middleware order |
| [JWT + session validation](./jwt-session-validation/README.md) | Session-bound JWT on authenticated requests |
| [Refresh-token rotation](./refresh-token-rotation/README.md) | Hash compare, rotate, reuse vs concurrency |
| [Session lifecycle](./session-lifecycle/README.md) | Active → validate → continue or revoke |
| [Authorization policy resolution](./authorization-policy-resolution/README.md) | DB-backed permissions, not JWT roles |
| [Multi-tenant isolation layers](./tenant-isolation-layers/README.md) | Isolation at several cooperating layers |
| [Hangfire job context](./hangfire-job-context/README.md) | User/tenant captured at enqueue, restored at run |
| [Audit trail + soft delete](./audit-soft-delete/README.md) | SaveChanges stamps, soft delete, redacted trails |
| [FrontEnd single-flight refresh](./frontend-single-flight-refresh/README.md) | 401 → one refresh → retry once |
| [FrontEnd routing / access gating](./frontend-routing-gating/README.md) | UX gate; API remains authoritative |
| [Cursor engineering workflow](./cursor-engineering-workflow/README.md) | Plan-first, rules-guided implementation |

Format: **Mermaid** (`.mmd` source + rendered in each README).
