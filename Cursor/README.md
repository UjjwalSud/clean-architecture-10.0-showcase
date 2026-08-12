# Cursor / AI-Assisted Engineering

This section shows how the private architecture foundation uses Cursor as a **governed engineering assistant**: rules, architecture docs, reference modules, and plan-first prompts steer changes toward existing Clean Architecture conventions.

It does not claim that AI replaces design review. It documents the controls that keep AI-assisted edits consistent with the codebase.

The reusable patterns behind this approach are developed and shared in:

**[ai-engineering-system](https://github.com/UjjwalSud/ai-engineering-system)**

This project’s `.cursor` tree is a **project-specific application** of that broader system (architecture docs, stack rules, prompts), specialized for the private Clean Architecture foundation represented by this showcase.

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
- [AI engineering system](https://github.com/UjjwalSud/ai-engineering-system)

## Evidence

Paths refer to the private source architecture represented by this showcase:

- `.cursor/AGENTS.md`
- `.cursor/rules/`
- `.cursor/docs/`
- `.cursor/prompts/`
