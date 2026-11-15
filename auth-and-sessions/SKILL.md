---
name: auth-and-sessions
description: Design, implement, and review authentication, session management, OAuth/OIDC, JWT usage, password handling, MFA, and role-based access control for web apps. Use this skill whenever the user builds login, signup, password reset, "remember me", API tokens, social login, roles/permissions, or asks whether to use JWT or sessions, even if they only say "user accounts."
---

# Auth and Sessions

Prefer a proven library or provider (Auth.js, Lucia-style patterns, Clerk, Supabase Auth, Keycloak, framework built-ins) over custom crypto or custom flows. If custom is required, follow the rules below exactly.

## Choosing a session model
| Situation | Use |
|---|---|
| Server-rendered or same-site SPA + API | Server-side sessions in an HttpOnly cookie |
| Mobile app or third-party API clients | Short-lived access token + rotating refresh token |
| Microservices needing stateless verification | Short-lived signed JWT, with a revocation strategy |

JWTs are not automatically better. They are hard to revoke; keep them short-lived (5 to 15 minutes) and never store them in localStorage when a cookie will do.

## Passwords
- Hash with **Argon2id** (preferred), scrypt, or bcrypt (cost ≥ 12). Never MD5/SHA-1/plain SHA-256.
- Enforce length (≥ 12 recommended) over composition rules; check against breached-password lists.
- Constant-time comparison; identical error message and timing for "no such user" vs "wrong password".
- Rate limit and lock out progressively on repeated failures.

## Sessions and cookies
- Generate session IDs with a CSPRNG (≥ 128 bits). Regenerate the ID on login and privilege change.
- Cookie: `HttpOnly; Secure; SameSite=Lax; Path=/`, with idle and absolute expiry.
- Logout destroys the server-side session, not only the cookie.

## Password reset and email verification
- Single-use, high-entropy token, stored hashed, expiring in ≤ 1 hour.
- Same response whether or not the email exists.
- Invalidate existing sessions after a password change.

## OAuth / OIDC
- Authorization Code flow with **PKCE**. Do not use the implicit flow.
- Validate `state` and `nonce`; match `redirect_uri` exactly against an allowlist.
- Verify ID token signature, `iss`, `aud`, `exp`.

## JWT rules
- Pin the algorithm server-side (reject `none`); prefer asymmetric (RS256/ES256) when multiple services verify.
- Keep payload minimal, with no sensitive data.
- Rotate signing keys; support `kid`.

## MFA
- Offer TOTP or WebAuthn/passkeys; SMS only as a fallback. Provide recovery codes (hashed).

## Authorization (RBAC/ABAC)
- Check permissions server-side on every request; deny by default.
- Verify resource ownership, not just role (prevents IDOR).
- Centralize policy checks in one module or middleware.

## Review output
List issues as **[severity] location, problem, fix**. Finish with a short summary of which of the above areas were covered.
