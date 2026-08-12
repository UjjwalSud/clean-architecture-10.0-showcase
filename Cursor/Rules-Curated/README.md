# Curated Rules

Public summary of engineering rules that guide AI-assisted and human changes in the source architecture. Full `.mdc` files are **not** published. Small sanitized excerpts below illustrate the discipline without dumping internal agent configuration.

Related: [Engineering Workflow](../Engineering-Workflow/README.md) · [Sanitization Policy](../Sanitization-Policy/README.md)

## Rule categories (verified)

| Category | Source rule (relative) | What it enforces |
|----------|------------------------|------------------|
| Core architecture guardrails | `.cursor/rules/general-clean-architecture.mdc` | Layer direction, conventions over textbook defaults, no unsolicited CQRS/repos |
| API / Host discipline | `.cursor/rules/controller-thin-rule.mdc` | Controllers delegate to services; permissions + OpenAPI on protected actions |
| Persistence boundary | `.cursor/rules/persistence-boundary-rule.mdc` | No DbContext / Infrastructure references from Application or Domain |
| DTO naming | `.cursor/rules/dto-naming.mdc` | Create/Update/Search request + response naming conventions |
| Module creation checklist | `.cursor/rules/new-module-creation.mdc` | Layer-by-layer creation expectations (published as summary only) |

Supporting docs (not rules files): anti-patterns list, architecture reference, module plan format, reference modules.

## Sanitized excerpts

These are short, public-safe restatements of rule intent:

**Thin controllers**

> Controllers inject services and delegate. No business logic in controllers. Protected actions use permission attributes; OpenAPI documents the operation.

**Persistence boundary**

> Application must not reference Infrastructure. Domain must not reference Application or Infrastructure. Keep DbContext out of Application and Domain.

**DTO naming**

> Requests: Create/Update/Search{Entity}. Responses: Create{Entity}Response, View{Entity}DetailResponse. Search requests extend the shared search base.

**Prefer existing service patterns**

> Prefer the existing service layer. Do not introduce MediatR, CQRS, or repositories unless explicitly requested. Follow project conventions over generic textbook patterns.

**Plan first (module work)**

> For new modules, return a Module Plan first; generate code only after the plan is confirmed.

## Evolution notes (heavily sanitized)

From architecture-decision material, the public-safe themes are:

- **Intentionally deferred** unless requested: MediatR/CQRS, repository layers, domain-event frameworks, singular entity renames.
- **Preferred for new work:** pass `CancellationToken` on async APIs; keep controllers thin; follow reference modules.
- **Strengths to preserve:** layered projects, permission model, multi-DbContext persistence, config-driven infrastructure modules.

Candid internal backlog / deviation inventories are **not** published (see Sanitization Policy).

## Anti-pattern themes (public)

- Business logic in controllers
- Introducing CQRS/MediatR/repositories by default
- Renaming plural entities to singular without agreement
- Putting DbContext into Application/Domain
- Changing audit/soft-delete or route versioning behavior without confirmation

## Evidence

- `.cursor/rules/general-clean-architecture.mdc`
- `.cursor/rules/controller-thin-rule.mdc`
- `.cursor/rules/persistence-boundary-rule.mdc`
- `.cursor/rules/dto-naming.mdc`
- `.cursor/rules/new-module-creation.mdc`
- `.cursor/docs/ANTI_PATTERNS.md`
- `.cursor/docs/02_ARCHITECTURE_DECISIONS.md` (sanitized themes only)
- `.cursor/AGENTS.md`
