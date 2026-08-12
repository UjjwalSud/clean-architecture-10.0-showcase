# API OpenAPI / NSwag

API documentation is generated with NSwag OpenAPI documents and Swagger UI, gated by configuration.

## Verified capabilities

- **Config-gated OpenAPI** — enabled via `SwaggerSettings` from `openapi.json`.
- **NSwag document + UI** — `AddOpenApiDocument`, `UseOpenApi`, and `UseSwaggerUi`.
- **Security schemes** — JWT Bearer HTTP scheme by default; Azure AD OAuth2 authorization-code + PKCE path when auth provider is configured as AzureAd.
- **Processors** — global auth processor, header attribute processor, GUID schema processor, AspNetCore security scope processor, FluentValidation schema processor.
- **Controller annotations** — endpoints commonly use `[OpenApiOperation]` (and related NSwag attributes).
- **UI behavior** — models collapsed by default; tags sorted alphabetically.

## Configuration

- Settings: `API/src/Infrastructure/OpenApi/SwaggerSettings.cs`
- Config file: `API/src/Host/Configurations/openapi.json`

## Libraries

- `NSwag.AspNetCore`
- `ZymLabs.NSwag.FluentValidation.AspNetCore`

## Evidence

- `API/src/Infrastructure/OpenApi/Startup.cs`
- `API/src/Infrastructure/OpenApi/SwaggerSettings.cs`
- `API/src/Infrastructure/OpenApi/SwaggerGlobalAuthProcessor.cs`
- `API/src/Infrastructure/OpenApi/SwaggerHeaderAttribute.cs`
- `API/src/Infrastructure/OpenApi/SwaggerHeaderAttributeProcessor.cs`
- `API/src/Infrastructure/OpenApi/SwaggerGuidSchemaProcessor.cs`
- `API/src/Host/Configurations/openapi.json`
- `API/src/Infrastructure/Startup.cs` (`AddOpenApiDocumentation` / `UseOpenApiDocumentation`)
