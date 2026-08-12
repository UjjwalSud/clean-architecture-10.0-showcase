# Solution project dependencies

```mermaid
flowchart TB
  FE["FrontEnd SPA<br/>Vite + React"]

  subgraph sln ["Clean.Architecture.sln"]
    direction TB
    subgraph core ["Core class libraries"]
      Shared["Shared"]
      Domain["Domain"]
      App["Application"]
    end
    Infra["Infrastructure"]
    Host["Host"]
    subgraph migrators ["Migrators"]
      Mssql["Migrators.MSSQL"]
      Pg["Migrators.PostgreSQL"]
    end
  end

  FE -.->|"HTTP + JWT<br/>not a project reference"| Host

  Domain -->|"ProjectReference"| Shared
  App -->|"ProjectReference"| Domain
  App -->|"ProjectReference"| Shared
  Infra -->|"ProjectReference"| App
  Infra -->|"ProjectReference"| Domain
  Infra -->|"ProjectReference"| Shared
  Host -->|"ProjectReference"| App
  Host -->|"ProjectReference"| Infra
  Host -->|"ProjectReference"| Mssql
  Mssql -->|"ProjectReference"| Infra
```

## What this diagram shows

Compile-time **project references** inside `Clean.Architecture.sln`, plus the FrontEnd SPA as a separate runtime client (not a `.csproj` reference).

## Why it matters

A first-time visitor can see which class libraries exist, who references whom, and that Application/Domain never reference Infrastructure.

## Key points

| Project | References |
|---------|------------|
| Shared | none |
| Domain | Shared |
| Application | Domain, Shared |
| Infrastructure | Application, Domain, Shared |
| Host | Application, Infrastructure, Migrators.MSSQL |
| Migrators.MSSQL | Infrastructure |
| Migrators.PostgreSQL | **none** — in the solution, not referenced by Host |
| FrontEnd | HTTP to Host only |

Solid arrows = `ProjectReference`. Dashed arrow = runtime HTTP.

## Evidence

- `API/src/Core/Domain/Domain.csproj`
- `API/src/Core/Application/Application.csproj`
- `API/src/Infrastructure/Infrastructure.csproj`
- `API/src/Host/Host.csproj`
- `API/src/Migrators/Migrators.MSSQL/Migrators.MSSQL.csproj`
- `API/src/Migrators/Migrators.PostgreSQL/Migrators.PostgreSQL.csproj`

## Related documentation

- [Root README](../../../README.md)
- [Overall Clean Architecture](../overall-clean-architecture/README.md)
- [API overview](../../../API/README.md)
