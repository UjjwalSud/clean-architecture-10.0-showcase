# FrontEnd Real-Time & Integrations

Interactive integrations verified in the FrontEnd: SignalR notifications hook, Jitsi meeting embed, Stripe Elements checkout, FullCalendar appointments, and social login providers. Secrets and customer data are not documented here.

Related: [API Notifications](../../API/Notifications/README.md) · [API Integrations](../../API/Integrations/README.md) · [API Authentication](../../API/Authentication/README.md) · [Authentication](../Authentication/README.md)

## Verified capabilities

### SignalR

- Hook builds a hub connection to `{API_URL}/notifications`.
- Access token supplied via `accessTokenFactory` from the logged-in session; credentials cookie mode off.
- Automatic reconnect enabled.
- Mounted from Topbar notification dropdown.
- Client listens for `NotificationFromServer`.
- **Current UI gap (factual):** the dropdown still uses static props; hub messages are logged rather than driving the list UI today.
- Backend hub path and tenant-group model are documented under API Notifications / Multi-Tenancy.

### Jitsi

- Public start-meeting route loads meeting settings from the participant API.
- FrontEnd renders `@jitsi/react-sdk` with domain/room/JWT returned by the API (client does not mint meeting JWTs).
- Presence callbacks notify the API on join/close style events.
- Cross-link: [API Integrations — Jitsi](../../API/Integrations/README.md)

### Stripe

- Subscription plan checkout loads a **publishable** key from a public config file, then `loadStripe` + `Elements`.
- Uses `CardElement` + `confirmCardPayment` with a client secret from the subscription payment intent API.
- Confirmation page polls/loads payment status by transaction key.
- No Stripe secret keys in FrontEnd usage.
- Cross-link: [API Integrations — Stripe](../../API/Integrations/README.md)

### FullCalendar

- Appointments management calendar (`dayGrid` / `timeGrid` / `list` / interaction plugins).
- Range loads via appointment API; event click/date click drive modal workflows.

### Social login

- Google and Facebook UI components gather provider tokens/profile payloads.
- FrontEnd posts to social token API, then Redux persists session and loads profile/permissions (same overall login success path as password login when tokens are returned).

## Evidence

- `FrontEnd/src/hooks/useSignalR.ts`
- `FrontEnd/src/components/NotificationDropDown.tsx`
- `FrontEnd/src/layouts/Topbar.tsx`
- `FrontEnd/src/pages/orbit/start-meeting/index.tsx`
- `FrontEnd/src/pages/orbit/start-meeting/Components/RenderJitsiMeeting.tsx`
- `FrontEnd/src/pages/orbit/buy-subscription-plan/Index.tsx`
- `FrontEnd/src/pages/orbit/buy-subscription-plan/Components/PayForSubscriptionPlan.tsx`
- `FrontEnd/src/pages/orbit/manage-appointments/Components/ViewAppointment.tsx`
- `FrontEnd/src/pages/auth/GoogleLoginForm.tsx`
- `FrontEnd/src/pages/auth/FacebookLoginForm.tsx`
- `FrontEnd/src/redux/auth/saga.ts`
- `FrontEnd/public/stripeConfig.json` (publishable key file; do not treat as secret material)
