---
name: web-security
description: Write, review, and audit web application code against common vulnerabilities (OWASP Top 10, XSS, CSRF, SQL injection, SSRF, insecure CORS, missing security headers). Use this skill whenever the user builds or reviews anything that handles user input, renders dynamic content, sets headers or cookies, configures CORS, uploads files, or asks "is this secure?", even if they never say the word "security."
---

# Web Security

Treat all input as hostile and all output as a potential injection point. Apply this checklist while writing code, and when reviewing, report findings by severity with a concrete fix.

## Workflow
1. Identify trust boundaries: where does data enter (forms, query strings, headers, cookies, uploads, webhooks, third-party APIs) and where does it leave (HTML, SQL, shell, file paths, redirects, logs)?
2. Walk each boundary through the checklist below.
3. Report: **Critical / High / Medium / Low**, location (file:line), why it is exploitable, and the fix as code.

## Checklist

### Injection
- Use parameterized queries or an ORM's bound parameters. Never build SQL, NoSQL filters, or shell commands by string concatenation.
- Avoid `exec`/`eval`/`child_process.exec` with user data. If unavoidable, use argument arrays and an allowlist.
- Validate paths: resolve and confirm they stay inside the intended directory (blocks `../` traversal).

### XSS
- Rely on framework auto-escaping. Flag `dangerouslySetInnerHTML`, `innerHTML`, `v-html`, `document.write`.
- If raw HTML is required, sanitize with DOMPurify (or equivalent) on a strict allowlist.
- Set a Content-Security-Policy; prefer nonces/hashes over `unsafe-inline`.

### CSRF
- Cookie-based auth requires CSRF tokens or `SameSite=Lax/Strict` plus an Origin/Referer check on state-changing requests.
- State changes never happen on GET.

### Cookies and sessions
- Session cookies: `HttpOnly; Secure; SameSite=Lax` (or Strict), narrow `Path`/`Domain`, sensible expiry.
- See the `auth-and-sessions` skill for deeper auth review.

### CORS
- Never reflect arbitrary `Origin`. Use an explicit allowlist.
- `Access-Control-Allow-Origin: *` is never combined with credentials.

### Security headers
```
Content-Security-Policy: default-src 'self'; object-src 'none'; base-uri 'self'; frame-ancestors 'none'
Strict-Transport-Security: max-age=63072000; includeSubDomains
X-Content-Type-Options: nosniff
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: camera=(), microphone=(), geolocation=()
```

### SSRF and redirects
- When the server fetches a user-supplied URL: allowlist hosts, block private/loopback/link-local ranges (127.0.0.0/8, 10.0.0.0/8, 169.254.0.0/16, etc.), disable redirects or re-validate them.
- Open redirects: only redirect to relative paths or allowlisted hosts.

### File uploads
- Check size, extension, and real MIME type (magic bytes). Rename on store; keep uploads outside the web root or on object storage; never execute them.

### Access control
- Enforce authorization server-side on every request. Check ownership (`resource.userId === session.userId`) to prevent IDOR.
- Deny by default.

### Secrets and errors
- No secrets in source, client bundles, or logs. Use env vars or a secrets manager.
- Return generic errors to clients; log details server-side. Never expose stack traces.

### Rate limiting
- Limit login, signup, password reset, OTP, and expensive endpoints.

## Output format
For reviews, use this structure per finding:

```
[HIGH] Stored XSS in comment renderer: src/components/Comment.tsx:42
Why: user-supplied `body` is rendered via dangerouslySetInnerHTML without sanitization.
Fix: <code showing sanitized version>
```

End with a short list of what was checked and found clean, so the user knows the coverage.
