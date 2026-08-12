# Login

![Login screen](./login.png)

## What this shows

The real Sign In page from the FrontEnd SPA: email/password fields, validation feedback, forgot-password link, and social login entry points (Facebook / Google).

## Capability demonstrated

Authentication is implemented as part of the product UI. The page is the client entry point into the session-bound JWT flow documented under API / FrontEnd Authentication.

## Evidence

- `FrontEnd/src/pages/auth/Login.tsx`
- `FrontEnd/src/routes/authRoutes.tsx` (`/auth/login`)
- `FrontEnd/src/components/AuthPageLayout/`
- `API/src/Host/Controllers/Identity/TokensController.cs`

## Related documentation

- [FrontEnd Authentication](../../../FrontEnd/Authentication/README.md)
- [API Authentication](../../../API/Authentication/README.md)
