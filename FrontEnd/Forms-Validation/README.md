# FrontEnd Forms & Validation

Forms are primarily React Hook Form–based. Orbit CRUD typically uses shared `VerticalForm` + `FormInput` with **server-driven field errors**. Yup-backed resolvers are used mainly on auth (and some template) screens — not as the Orbit CRUD standard.

Related: [UI Architecture](../UI-Architecture/README.md) · [Authentication](../Authentication/README.md) · [API Security](../../API/Security/README.md)

## Verified capabilities

- `VerticalForm` wraps `useForm` + `FormProvider`, injects `register` / `errors` / `control` into children, supports optional resolvers, `mode: 'onChange'`.
- `FormInput` supports password (show/hide), textarea, select, bottom-sheet/mobile select, checkbox/radio, and native input types.
- Dominant Orbit pattern: modal + Popup + `VerticalForm` + submit via `runWithToast`.
- Server validation rendering via `messageHelper.showErrorResult` (maps API field errors onto DOM inputs with invalid styling).
- Auth screens commonly use `yup` + `@hookform/resolvers`.
- Appointment add/reschedule/cancel flows often use raw `useForm` for nested UI instead of VerticalForm.
- Some settings/filter UIs use `FormInput` without a VerticalForm wrapper.
- Loading/disabled submit behavior is page-local (typically tied to toast/async helpers and button state).

## Dominant vs alternate patterns

| Pattern | Where it shows up |
|---------|-------------------|
| VerticalForm + FormInput, little/no yup | Most Orbit CRUD modals (users, roles, tenants, forms, lookups, language, email templates, profile, …) |
| VerticalForm + yupResolver | Login/register/password/lock/2FA (+ auth2); some template apps |
| Raw useForm + FormInput | Appointment modal forms with richer nested controls ([screenshot](../../assets/screenshots/appointment-modal/README.md)) |
| FormInput without VerticalForm | Selected settings views / filter controls ([Manage Settings](../UI-Architecture/README.md)) |
| Template UI demos | `pages/ui/forms/*` (not Orbit product standard) |

**Honest note:** yup is installed and used, but Orbit business forms rely heavily on HTML constraints + API validation feedback rather than shared client schema libraries.

Settings (Appointment / ApprovedAppointment) is a representative Orbit admin module with nested editors — not a second forms framework.

## Server error rendering

`messageHelper.showErrorResult` is the shared path for API `ErrorResult`-style payloads: toast message plus attempts to bind field errors onto inputs by name. The HTTP client coordinates with this path to avoid duplicate toasts when an error was already displayed.

## Evidence

- `FrontEnd/src/components/VerticalForm.tsx`
- `FrontEnd/src/components/FormInput.tsx`
- `FrontEnd/src/helpers/message.helper.ts`
- `FrontEnd/src/helpers/asyncToast.helper.ts`
- `FrontEnd/src/pages/auth/Login.tsx` (and related auth forms)
- `FrontEnd/src/pages/orbit/manage-users/` (representative VerticalForm CRUD)
- `FrontEnd/src/pages/orbit/manage-settings/` (nested settings editors)
- `FrontEnd/src/pages/orbit/manage-appointments/Components/` (raw useForm examples)
- `FrontEnd/package.json` (`react-hook-form`, `yup`, `@hookform/resolvers`)
