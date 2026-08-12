# Appointment Modal

![Add New Appointment modal](./appointment-modal.png)

## What this shows

The **Add New Appointment** modal: title/location/duration/approval fields, dynamic schedule rows, required/optional participants, description, and Save/Close actions — over the Manage Appointments shell.

## Capability demonstrated

Reusable modal chrome plus a richer Orbit form (nested schedule/participant rows) — evidence for UI architecture and forms patterns beyond simple CRUD grids.

An empty calendar-only capture was skipped; this modal is the stronger architecture signal.

## Evidence

- `FrontEnd/src/pages/orbit/manage-appointments/`
- `FrontEnd/src/routes/orbitRoutes.tsx` (`/administrator/manage-appointments`)
- Appointment modal controller / modal components under the manage-appointments page tree
- `FrontEnd/src/components/` modal / Popup shells (`ModalLayout` and related)

## Related documentation

- [FrontEnd UI Architecture](../../../FrontEnd/UI-Architecture/README.md)
- [FrontEnd Forms & Validation](../../../FrontEnd/Forms-Validation/README.md)
