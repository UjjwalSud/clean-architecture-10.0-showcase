# Manage Language

![Manage Language](./manage-language.png)

## What this shows

Orbit **Manage Languages** administration: country/language catalog grid (country name, code, display order) with Add and row Actions.

## Capability demonstrated

Localization admin UI over the shared country + localization catalog (see Localization docs). Visible row is the seeded USA/`us` entry.

## Evidence

- `FrontEnd/src/pages/orbit/manage-language/`
- `FrontEnd/src/routes/orbitRoutes.tsx` (`/administrator/manage-language`)
- `API/src/Host/Controllers/Localization/LocalizationController.cs`

## Related documentation

- [API Localization](../../../API/Localization/README.md)
- [FrontEnd Grids & Tables](../../../FrontEnd/Grids-Tables/README.md)
