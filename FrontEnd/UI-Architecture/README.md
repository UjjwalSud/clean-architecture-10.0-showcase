# FrontEnd UI Architecture

Reusable UI is organized as shared building blocks (forms, modals, toasts, grids, page chrome) plus module-specific controllers where workflows are more complex. This is not a single design-system package — Orbit product screens compose shared shells with page-local columns, actions, and modal orchestration.

Related: [Forms & Validation](../Forms-Validation/README.md) · [Grids & Tables](../Grids-Tables/README.md) · [Authentication](../Authentication/README.md) · [Routing](../Routing/README.md)

## Verified capabilities

- Shared component barrel for common form/page primitives (`VerticalForm`, `FormInput`, Popup chrome, Pagination, EmptyState, FileUploader, â€¦).
- Headless UI wrappers for modal/offcanvas/popover (`ModalLayout` and related layouts).
- Route-level `ErrorBoundary` around routed content.
- Central toast/error pipeline via `messageHelper` (+ optional expandable technical toast content).
- Async helper `runWithToast` for consistent success/error toast handling.
- HTTP-layer dedupe so the same API error is not toasted twice.
- Dominant Orbit modal shell: `ModalLayout` + Popup header/body/footer.
- Delete confirmation uses a shared destructive confirm component across many Orbit screens.
- Appointment workflows use a dedicated modal state + controller pattern (module-specific).
- Many list screens use a lighter `useModalState` pattern instead of a full controller package.
- Shared hooks for permissions, object/modal state, domain data loaders, idle timeout, SignalR, grid ready.

![Add New Appointment modal](../../assets/screenshots/appointment-modal/appointment-modal.png)

See [appointment-modal screenshot](../../assets/screenshots/appointment-modal/README.md).

## How reusable UI is structured

### Centralized

| Concern | Approach |
|---------|----------|
| Form fields / vertical form | Shared `VerticalForm` + `FormInput` |
| Modal chrome | `ModalLayout` + Popup variants |
| Toasts / API errors | `messageHelper`, `ErrorToast`, `httpClient` display guard |
| List tables | Shared DataGrid wrappers (see Grids) |
| Page chrome | breadcrumbs, preloaders, EmptyState, PageFilter (path-imported) |

### Module-specific

| Concern | Approach |
|---------|----------|
| Column definitions / row actions | Defined per Orbit page |
| Appointment multi-modal flow | `AppointmentModalState` + `useAppointmentModalController` + `AppointmentModals` |
| Complex appointment forms | Page-local `useForm` composition (see Forms) |
| Settings editors | Sometimes FormInput outside VerticalForm |

## Appointment modal controller (pattern)

Appointments centralize open/close/reload for add/view/reschedule/cancel in a small b-logic package, then render multiple `ModalLayout` hosts. Most other Orbit CRUD pages keep modal open state closer to the page (`useModalState`) rather than extracting a controller.

## Evidence

- `FrontEnd/src/components/index.tsx`
- `FrontEnd/src/components/HeadlessUI/ModalLayout.tsx`
- `FrontEnd/src/components/ErrorBoundary/index.tsx`
- `FrontEnd/src/components/ErrorToast/index.tsx`
- `FrontEnd/src/helpers/message.helper.ts`
- `FrontEnd/src/helpers/asyncToast.helper.ts`
- `FrontEnd/src/components/ConfirmationModal.tsx`
- `FrontEnd/src/components/DeleteConfirmation.tsx`
- `FrontEnd/src/components/Popup/`
- `FrontEnd/src/pages/orbit/manage-appointments/b-logic/AppointmentModalState.ts`
- `FrontEnd/src/pages/orbit/manage-appointments/b-logic/useAppointmentModalController.ts`
- `FrontEnd/src/pages/orbit/manage-appointments/Components/AppointmentModals.tsx`
- `FrontEnd/src/hooks/`
- `FrontEnd/src/routes/Routes.tsx`
- `FrontEnd/src/App.tsx`
