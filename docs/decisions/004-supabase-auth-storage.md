# ADR-004: Supabase for OAuth verification and file storage

## Status
Accepted

## Date
2024-03-18

## Context
LocalLoop supports Google and Apple OAuth login. We need to:
- Verify OAuth tokens server-side without building our own OAuth flow
- Store user media (images, videos) with secure, short-lived access URLs
- Avoid running our own auth server for the MVP

## Decision
We use **Supabase** for two purposes only:
1. **OAuth token verification** — Supabase Auth verifies Google/Apple tokens. We extract the user identity, then issue our own JWTs (stored nowhere on Supabase). Supabase Auth is a verification gateway, not a session store.
2. **File storage** — Supabase Storage for user-uploaded media. We generate presigned URLs server-side; clients upload directly to Supabase (bypasses our API for large payloads).

Supabase is NOT used as a database, realtime layer, or primary data store.

## Consequences

### Positive
- Google and Apple OAuth handled without building our own OAuth integration
- Presigned URL pattern keeps large media uploads off our API server
- Supabase has a generous free tier suitable for MVP

### Negative
- External dependency for auth verification — if Supabase is down, logins fail
- Two separate services to configure (Supabase project + our PostgreSQL)

### Neutral
- JWTs are signed with our own `JWT_SECRET` — users are portable if we ever replace Supabase Auth
- Storage keys (not full URLs) are stored in the database; presigned URLs are generated on demand

## Alternatives considered

| Option | Why rejected |
|--------|-------------|
| Build own OAuth flow | Too much complexity for MVP — token verification, PKCE, etc. |
| Firebase Auth + Storage | Works, but mixes Google ecosystem; Supabase is more self-hostable if needed |
| AWS S3 directly | More IAM/infra complexity; Supabase covers both auth + storage in one |
