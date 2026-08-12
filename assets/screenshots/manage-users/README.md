# Manage Users

![Manage Users](./manage-users.png)

## What this shows

The Orbit **Manage Users** administration page: shared AG Grid list, status badges, row Actions menu, and Add entry point inside the authenticated vertical layout.

## Capability demonstrated

User administration is implemented end-to-end in the UI and sits on the shared grid architecture used across Orbit list screens.

Visible rows are seeded/demo users only.

## Evidence

- `FrontEnd/src/pages/orbit/manage-users/`
- `FrontEnd/src/routes/orbitRoutes.tsx` (`/administrator/manage-users`)
- `FrontEnd/src/components/DataGrid/DataGridWithoutPagination.tsx`
- `API/src/Host/Controllers/Identity/UsersController.cs`

## Related documentation

- [FrontEnd Grids & Tables](../../../FrontEnd/Grids-Tables/README.md)
- [FrontEnd README](../../../FrontEnd/README.md)
- [API Authorization](../../../API/Authorization/README.md)
