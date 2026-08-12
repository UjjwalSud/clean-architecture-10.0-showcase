# Manage Roles

![Manage Roles](./manage-roles.png)

![Manage Role Permissions](./manage-role-permissions.png)

## What this shows

1. **Role list** — AG Grid of tenant roles (for example Admin / Basic) with Actions.
2. **Manage Permissions** — modal that assigns granular permissions by module tab, with human-readable descriptions and select checkboxes.

## Capability demonstrated

This is visual evidence of the permission/role architecture: roles are manageable in the UI, and permissions are editable per role across resources (Users, Roles, Appointments, Forms, Tenants, …). Backend permission policies remain authoritative; this UI is how administrators configure the claim set those policies evaluate.

## Evidence

- `FrontEnd/src/pages/orbit/manage-roles/`
- `FrontEnd/src/pages/orbit/manage-roles/Components/ManageRolePermissions.tsx`
- `FrontEnd/src/pages/orbit/manage-roles/Components/ViewRolesActionButtons.tsx`
- `FrontEnd/src/routes/orbitRoutes.tsx` (`/administrator/manage-roles`)
- `API/src/Host/Controllers/Identity/RolesController.cs`
- `API/src/Core/Shared/Authorization/SystemPermissions.cs`

## Related documentation

- [API Authorization](../../../API/Authorization/README.md)
- [FrontEnd Grids & Tables](../../../FrontEnd/Grids-Tables/README.md)
- [Authorization policy resolution diagram](../../diagrams/authorization-policy-resolution/README.md)
