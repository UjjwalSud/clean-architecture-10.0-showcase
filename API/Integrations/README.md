# API Integrations

External integrations currently verified in source: **Stripe** (payments/subscriptions) and **Jitsi** (meeting tokens).

## Stripe

### Verified capabilities

- Payment intent / subscription creation flows with persisted payment initialization records.
- Customer create/get patterns tied to tenant metadata.
- Webhook endpoint with Stripe signature validation and payment-succeeded / payment-failed handling paths.
- Payment status lookup and invoice PDF download path.
- Currency helper support verified for USD and INR amount conversion.
- Dedicated rate-limit policy name for Stripe webhook traffic.

### Configuration / libraries

- Config file: `API/src/Host/Configurations/stripe.json` (secrets not documented here)
- Library: `Stripe.net`
- Wired via `AddStripePayment` in Infrastructure startup

### Evidence

- `API/src/Infrastructure/ExternalIntegrations/Stripe/Startup.cs`
- `API/src/Infrastructure/ExternalIntegrations/Stripe/StripeService.cs`
- `API/src/Infrastructure/ExternalIntegrations/Stripe/StripeService.Helpers.cs`
- `API/src/Core/Application/ExternalIntegrations/Stripe/IStripeService.cs`
- `API/src/Host/Controllers/Subscription/SubscriptionController.Payments.cs`

## Jitsi

### Verified capabilities

- HMAC-SHA256 JWT generation for meetings (expiry, room, moderator, user context claims).
- Used when appointment location type is Jitsi during participant start-meeting flow.
- Returns domain/room/token (+ config overwrite DTOs) for the client meeting SDK.

### Configuration / libraries

- Config file: `API/src/Host/Configurations/externalIntegrations.json` (`ExternalIntegrations:JitsiSettings`)
- Library: `JWT`
- Wired via `AddJitsi` in Infrastructure startup

### Evidence

- `API/src/Infrastructure/ExternalIntegrations/Jitsi/Startup.cs`
- `API/src/Infrastructure/ExternalIntegrations/Jitsi/JitsiService.cs`
- `API/src/Infrastructure/Orbit/Appointment/AppointmentParticipant/ParticipantAppointmentBookingService.cs`
- `API/src/Host/Controllers/Appointment/ParticipantRequestController.cs`
- `API/src/Core/Application/ExternalIntegrations/Jitsi/IJitsiService.cs`
