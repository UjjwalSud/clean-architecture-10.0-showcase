# FrontEnd single-flight refresh

```mermaid
flowchart TD
  call[NSwag client authenticatedFetch]
  auth[Attach Bearer from sessionStorage]
  resp{HTTP status}
  ok[Return response]
  skip{Auth or token URL?}
  retryHdr{Already retried?}
  wait[Share in-flight refresh promise]
  refresh[POST tokens/refresh via window.fetch]
  upd[Update sessionStorage]
  retry[Retry original request once]
  fail[Revoke logout then unauthorized redirect]
  forbidden[Navigate to access-denied]

  call --> auth --> resp
  resp -->|2xx / other handled| ok
  resp -->|403| forbidden
  resp -->|401| skip
  skip -->|yes| fail
  skip -->|no| retryHdr
  retryHdr -->|yes| fail
  retryHdr -->|no| wait --> refresh
  refresh -->|success| upd --> retry --> resp
  refresh -->|failure| fail
```

## What this diagram shows

How the SPA recovers from an expired access token: concurrent 401s share **one** refresh; the original call retries **once**.

## Why it matters

Without single-flight, parallel API calls would stampede `/api/tokens/refresh` and collide with rotation/reuse detection on the server.

## Key points

- Refresh uses raw `window.fetch` so the interceptor does not recurse.
- Backend refresh still re-validates the session and does **not** extend idle.
- 403 goes to `/access-denied` and does **not** clear the session.
- Idle-modal “Continue” is a separate NSwag refresh path (not this interceptor).

## Evidence

- `FrontEnd/src/helpers/api/httpClient.ts`
- `FrontEnd/src/helpers/api/sessionRefreshQueue.ts`
- `FrontEnd/src/helpers/api/apiCore.ts`
- `FrontEnd/src/services/TokenService.ts`

## Related documentation

- [FrontEnd Authentication](../../../FrontEnd/Authentication/README.md)
- [API Authentication](../../../API/Authentication/README.md)
- [Refresh-token rotation](../refresh-token-rotation/README.md)
