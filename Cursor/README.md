# Cursor / AI-Assisted Engineering

This section shows how the architecture repository uses Cursor as a **governed engineering assistant**: rules, architecture docs, reference modules, and plan-first prompts steer changes toward existing Clean Architecture conventions.

It does not claim that AI replaces design review. It documents the controls that keep AI-assisted edits consistent with the codebase.

The reusable patterns behind this approach are developed and shared in:

**[ai-engineering-system](https://github.com/UjjwalSud/ai-engineering-system)**

This project’s `.cursor` tree is a **project-specific application** of that broader system (architecture docs, stack rules, prompts), specialized for [clean-architecture-10.0](https://github.com/UjjwalSud/clean-architecture-10.0).

## Why `.cursor` matters here

The `.cursor` tree is part of the architecture’s delivery system:

- Agents are told **what the architecture is** (layers, DbContexts, service pattern).
- Agents are told **what not to invent** (for example, unsolicited CQRS/MediatR/repositories).
- Larger work is expected to **plan before generating**.
- Changes are expected to **copy reference modules**, not generic internet templates.

Diagram: [Cursor engineering workflow](../assets/diagrams/cursor-engineering-workflow/README.md)

## Explore

| Page | Focus |
|------|--------|
| [Engineering Workflow](./Engineering-Workflow/README.md) | Plan-first flow, reference modules, scoped implementation |
| [Curated Rules](./Rules-Curated/README.md) | Public rule categories + small sanitized excerpts |
| [Sanitization Policy](./Sanitization-Policy/README.md) | What is intentionally not published |

## Design principles (high level)

- Architecture-aware assistance over free-form codegen
- Project conventions over textbook Clean Architecture defaults
- Thin Host controllers; business logic in Infrastructure services
- Explicit dependency boundaries (Application/Domain stay free of Infrastructure)
- Surgical edits; ask before renames or audit/soft-delete/versioning changes

## Related docs

- [API Showcase](../API/README.md)
- [FrontEnd Showcase](../FrontEnd/README.md)
- [Architecture source](https://github.com/UjjwalSud/clean-architecture-10.0)
- [AI engineering system](https://github.com/UjjwalSud/ai-engineering-system)

## Evidence (source of governance)

Paths below are in the architecture repository:

- [`.cursor/AGENTS.md`](https://github.com/UjjwalSud/clean-architecture-10.0/blob/main/.cursor/AGENTS.md)
- [`.cursor/rules/`](https://github.com/UjjwalSud/clean-architecture-10.0/tree/main/.cursor/rules)
- [`.cursor/docs/`](https://github.com/UjjwalSud/clean-architecture-10.0/tree/main/.cursor/docs)
- [`.cursor/prompts/`](https://github.com/UjjwalSud/clean-architecture-10.0/tree/main/.cursor/prompts)
