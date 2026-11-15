---
name: api-design
description: Design and review REST (and GraphQL) APIs: resource naming, HTTP methods and status codes, request/response shapes, pagination, filtering, versioning, error formats, idempotency, validation, rate limiting, and OpenAPI docs. Use this skill whenever the user creates endpoints, a backend, an API route, a webhook, or asks how to structure requests and responses, even if they don't say "API design."
---

# API Design

Design for the consumer first. Consistency beats cleverness.

## Resources and methods
- Nouns, plural, lowercase, kebab-case: `/users`, `/users/{id}/orders`.
- `GET` read (safe, cacheable), `POST` create, `PUT` replace, `PATCH` partial update, `DELETE` remove.
- Limit nesting to two levels; beyond that, use query filters.
- Actions that don't fit CRUD: `POST /orders/{id}/cancel`.

## Status codes
`200` OK · `201` Created (+ `Location`) · `204` No Content · `400` malformed · `401` unauthenticated · `403` forbidden · `404` not found · `409` conflict · `422` validation failed · `429` rate limited · `500` server error.

## Request/response shape
- JSON, camelCase (or snake_case, but pick one and stay consistent).
- Return the created/updated resource.
- ISO 8601 UTC timestamps; money as integer minor units + currency code.
- Don't leak internal fields (password hashes, internal IDs, stack traces).

## Errors: one format everywhere
```json
{
  "error": {
    "code": "VALIDATION_FAILED",
    "message": "Email is invalid",
    "details": [{ "field": "email", "issue": "must be a valid email" }],
    "requestId": "req_8f2c..."
  }
}
```
(Or RFC 9457 Problem Details.) Machine-readable `code`, human-readable `message`.

## Collections
- **Pagination:** cursor-based for large or changing sets (`?limit=25&cursor=...`), offset only for small stable sets. Return `nextCursor`.
- **Filtering/sorting:** `?status=active&sort=-createdAt`. Allowlist sortable fields.
- Cap `limit` server-side.

## Validation
- Validate at the edge with a schema (Zod, Joi, Pydantic, etc.). Reject unknown fields or ignore them deliberately.

## Idempotency and concurrency
- `PUT`/`DELETE` are idempotent by definition; support an `Idempotency-Key` header for `POST` payments/orders.
- Use `ETag` / `If-Match` or a version field for optimistic concurrency.

## Versioning
- Prefer additive, backward-compatible changes. When breaking: `/v2/...` or a version header. Deprecate with `Sunset` headers and a documented timeline.

## Security and operations
- Auth on every route (see `auth-and-sessions`), rate limits with `Retry-After`, CORS allowlist, request size limits, request IDs in logs.

## Documentation
- Produce an OpenAPI 3 spec; include examples for every endpoint, error cases included.

## Review output
Table of issues: endpoint, problem, recommended change. Suggest the corrected route/response where useful.
