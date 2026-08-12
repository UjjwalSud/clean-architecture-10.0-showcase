# Cursor Engineering Workflow

Derived from `.cursor/AGENTS.md`, module output format, reference modules, and prompt templates. The intended flow is **understand → plan → validate against rules → implement narrowly → verify**, not “generate everything first.”

This workflow is the project-level application of patterns from the reusable **[ai-engineering-system](https://github.com/UjjwalSud/ai-engineering-system)** (planning conventions, stack guidance, and engineering controls), specialized for [clean-architecture-10.0](https://github.com/UjjwalSud/clean-architecture-10.0).

Related: [Curated Rules](../Rules-Curated/README.md) · [Sanitization Policy](../Sanitization-Policy/README.md) · [Cursor overview](../README.md)

Diagram: [Cursor engineering workflow](../../assets/diagrams/cursor-engineering-workflow/README.md)

## Verified capabilities

- Agent identity doc defines golden rules and layer responsibilities.
- New-module work is instructed to return a **Module Plan first** (entities, DbContext, DTOs, permissions, files, migration impact) before code generation.
- Reference modules tell agents which existing feature to copy (FormDesigner for standard CRUD; others for relational/Nexus concerns).
- Prompt templates exist for common tasks (new module, add entity, add endpoint, refactor service) — summarized here, not reproduced in full.
- Rules enforce thin controllers, persistence boundaries, DTO naming, and anti-pattern avoidance.
- Agents are steered to prefer existing service + DbContext patterns over introducing repositories/CQRS/MediatR unless explicitly requested.
- Uncertainty guidance: find the closest module, follow it, ask before inventing a new pattern.

## Workflow (as encoded in `.cursor`)

```text
Requirement
  → Identify module / DbContext / entities / DTOs / permissions / controller style
  → Scan reference architecture + closest existing module
  → Produce plan (especially for new modules)
  → Validate against rules & anti-patterns
  → Scoped implementation (thin controller + Application interface + Infrastructure service)
  → Verify (permissions, OpenAPI attributes, CancellationToken discipline where required)
  → Review / confirm before broader refactors
```

## Reference-module strategy

| Reference | Use when |
|-----------|----------|
| FormDesigner | Standard Orbit-style CRUD vertical slice |
| LookUp | Smaller list/value modules |
| Appointment | More relational/workflow complexity |
| Nexus modules | Tenant/identity/subscription/localization concerns (not as Orbit CRUD templates) |

## Prompt templates (existence only)

The repository includes paste-ready prompt shells for:

- Creating a new module (plan-first)
- Adding an entity to an existing module
- Adding an endpoint
- Refactoring a service without unsolicited architectural rewrites

Full prompt bodies are **not** published (see [Sanitization Policy](../Sanitization-Policy/README.md)).

## Evidence

Paths in [clean-architecture-10.0](https://github.com/UjjwalSud/clean-architecture-10.0):

- [`.cursor/AGENTS.md`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/.cursor/AGENTS.md)
- [`.cursor/docs/MODULE_OUTPUT_FORMAT.md`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/.cursor/docs/MODULE_OUTPUT_FORMAT.md)
- [`.cursor/docs/REFERENCE_MODULES.md`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/.cursor/docs/REFERENCE_MODULES.md)
- [`.cursor/docs/ANTI_PATTERNS.md`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/.cursor/docs/ANTI_PATTERNS.md)
- [`.cursor/docs/01_SYSTEM_ARCHITECTURE.md`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/.cursor/docs/01_SYSTEM_ARCHITECTURE.md)
- [`.cursor/prompts/`](https://github.com/UjjwalSud/clean-architecture-10.0/tree/main/.cursor/prompts) (existence documented; full prompt bodies not published — see [Sanitization Policy](../Sanitization-Policy/README.md))
- `.cursor/rules/new-module-creation.mdc` (summary only; full file not published)
