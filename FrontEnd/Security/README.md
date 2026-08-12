# FrontEnd Security

Browser-side security posture for the SPA: session storage choices, idle timeout UX, HTML sanitization, and how Content Security Policy is handled.

This page does **not** replace [API Security](../../API/Security/README.md). Backend authentication, authorization, rate limiting, and API response headers remain authoritative for protected data.

Related: [FrontEnd Authentication](../Authentication/README.md) · [API Security](../../API/Security/README.md)

## Verified capabilities

### Session & client auth controls

- Access/refresh session payload stored in **`sessionStorage`** (`portal_user`) — tab-scoped, not HttpOnly cookies.
- Idle warning + Continue (refresh) / Log out via authenticated layout (see Authentication).
- `authenticatedFetch` attaches Bearer tokens; 401 refresh/retry; 403 → access-denied UX.

### HTML sanitization

- Shared `SanitizedHtml` wrapper uses **DOMPurify** for untrusted HTML (for example email log bodies).

### Content Security Policy (CSP)

**Important:** The FrontEnd repository contains **CSP planning/documentation and sample host configs**, not an in-repo enforced CSP for the Vite SPA.

Verified facts:

- `FrontEnd/docs/csp/README.md` describes a phased report-only → enforce approach and sample Nginx/IIS headers.
- Vite config has **no** CSP plugin; `index.html` has **no** CSP meta/nonce.
- API security-headers middleware sets framing/content-type/referrer/permissions-policy style headers on **API** responses — not a substitute for hosting CSP on the SPA origin.

Public takeaway: CSP enforcement is expected at the **hosting/reverse-proxy** layer using the documented guidance; this showcase does not claim browser CSP is active from the SPA build alone.

## Related local storage notes

Language preference and some auth UX helpers may also use `localStorage` (for example localization cache keys). Token session material for API calls remains in `sessionStorage`.

## Evidence

- `FrontEnd/docs/csp/README.md`
- `FrontEnd/vite.config.ts`
- `FrontEnd/index.html`
- `FrontEnd/src/helpers/api/apiCore.ts`
- `FrontEnd/src/helpers/api/httpClient.ts`
- `FrontEnd/src/hooks/useIdleSessionTimeout.ts`
- `FrontEnd/src/components/SanitizedHtml/index.tsx`
- `API/src/Infrastructure/SecurityHeaders/` (API response headers — cross-link only)
