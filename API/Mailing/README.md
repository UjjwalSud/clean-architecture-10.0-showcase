# API Mailing

Outbound email is a reusable pipeline: Hangfire-backed enqueue for many identity/appointment flows, settings-driven message content, multi-provider send, and tenant-scoped email logs. SignalR [Notifications](../Notifications/README.md) are a separate real-time concern.

Related: [Background Jobs](../BackgroundJobs/README.md) · [Integrations](../Integrations/README.md) · [Technology & Libraries](../Technology-Libraries.md)

## Verified capabilities

- **Mail abstraction** — Application `IMailService` / `MailDto`; Infrastructure `MailService` selects provider from `MailSettings.Provider`.
- **Providers wired in DI** — SendGrid (API), Resend (API), and **Aws** (MailKit SMTP to configured host — not an AWS SES SDK client). Unknown provider throws at send time.
- **Default config shape** — `mail.json` selects provider and holds per-provider settings (keys/credentials stay in environment/secrets; not published here).
- **Hangfire enqueue** — registration verification, forgot-password, 2FA email codes, and many appointment reminder/created/reschedule mails are enqueued or scheduled via `IJobService` / Hangfire.
- **Template content** — outbound bodies for identity and appointments are resolved from **Nexus/Orbit settings** (JSON `MailDto`-shaped content + placeholders). The `EmailTemplates` table is a separate admin catalog (CRUD) and is **not** the send-path resolver today.
- **Email logging** — each send attempt persists an `EmailLog` (success/failure metadata; provider details redacted where applicable). Logs are tenant-filtered via Application DbContext.
- **Admin UI** — Orbit **Email Log** and **Email Templates** screens call the corresponding controllers.
- **Test mode** — providers can rewrite recipients/subjects for non-production sends when configured.

## Pipeline (verified)

```text
Business action (identity / appointment / …)
  → Hangfire enqueue/schedule (common) or direct IMailService call
    → optional SetCurrentUserAndTenant for background identity mail
      → resolve content from settings (not EmailTemplates CRUD)
        → MailDto
          → provider send (SendGrid | Resend | Aws/SMTP)
            → EmailLog persistence
```

## Provider notes (factual)

| Provider setting | Transport |
|------------------|-----------|
| `SendGrid` | SendGrid API client |
| `Resend` | Resend API client |
| `Aws` | MailKit SMTP using configured host/credentials |

Empty API keys may register dummy clients for local boot; real sends require configured secrets.

## Configuration

- `API/src/Host/Configurations/mail.json`
- `API/src/Infrastructure/Mailing/` (Startup + provider folders)

## Evidence

- `API/src/Infrastructure/Mailing/Startup.cs`
- `API/src/Infrastructure/Mailing/MailService.cs`
- `API/src/Infrastructure/Mailing/MailService.EmailUser.cs`
- `API/src/Core/Application/Common/Mailing/IMailService.cs`
- `API/src/Host/Controllers/Email/EmailLogController.cs`
- `API/src/Host/Controllers/Email/EmailTemplateController.cs`
- `API/src/Core/Domain/Email/`
- `API/src/Infrastructure/BackgroundJobs/HangfireService.cs`
- `FrontEnd/src/pages/orbit/manage-email-log/`
- `FrontEnd/src/pages/orbit/manage-email-templates/`
