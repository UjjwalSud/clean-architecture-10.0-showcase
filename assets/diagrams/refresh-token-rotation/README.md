# Refresh-token rotation

```mermaid
sequenceDiagram
  participant C as Client
  participant T as Tokens refresh
  participant S as Session service
  participant DB as User session row

  C->>T: POST refresh access + refresh tokens
  T->>T: Parse access JWT lifetime ignored
  T->>S: ValidateSession updateActivity false
  alt Session invalid
    S-->>C: Reject no rotation
  else Session valid
    T->>DB: Load refresh hash and expiry
    alt Hash mismatch
      T->>S: Revoke RefreshTokenReuse
      T-->>C: Reject
    else Hash matches
      T->>DB: Rotate hash concurrency token
      alt Save concurrency conflict
        T-->>C: Reject without revoke
      else Saved
        T-->>C: New access + refresh pair
      end
    end
  end
```

## What this diagram shows

The refresh endpoint accepts an **expired** access token (lifetime not required), re-validates the session, compares the presented refresh token to a stored **hash**, then rotates to a new pair.

## Why it matters

Refresh is session-bound and rotating. Reuse of an old refresh token revokes the session. A multi-tab race rejects the loser **without** revoking the winner.

## Key points

- Only the hash is stored; compare is constant-time.
- `ValidateSessionAsync` is called with `updateActivity: false` — refresh does **not** extend idle.
- Hash mismatch → `RefreshTokenReuse` revoke.
- `DbUpdateConcurrencyException` → reject without revoke.

## Evidence

- `API/src/Infrastructure/Nexus/Identity/TokenService.cs`
- `API/src/Infrastructure/Nexus/Identity/RefreshTokenHasher.cs`
- `API/src/Infrastructure/Nexus/Identity/UserSessionService.cs`

## Related documentation

- [API Authentication](../../../API/Authentication/README.md)
- [FrontEnd single-flight refresh](../frontend-single-flight-refresh/README.md)
