# API Design Standards

> Starter example. Replace or expand for your project.

## Scope

These standards apply to HTTP APIs (REST and RPC-style) exposed from services in this project. GraphQL and gRPC are out of scope for now.

## URL and Verb Conventions

- Resources are nouns, plural: `/users`, `/orders`, not `/getUser`.
- Verbs are HTTP methods: `GET /users/{id}`, `POST /users`, `PATCH /users/{id}`, `DELETE /users/{id}`.
- Sub-resources express relationships: `/users/{id}/orders`.
- Query params for filtering, sorting, and pagination: `?status=active&sort=-created_at&limit=50`.

## Versioning

- Version in URL path: `/v1/users`. Breaking changes require a new version.
- Within a version: additive changes only (new fields, new endpoints). Never remove or rename.

## Request/Response Format

- JSON only. `Content-Type: application/json; charset=utf-8`.
- Field names: `snake_case` (consistent across projects in this org).
- Timestamps: ISO 8601 with timezone (`2026-04-12T20:45:00Z`).
- Money: integer minor units (cents), never floats.
- Enums: lowercase strings (`"active"`, `"pending"`), never integers.

## Errors

- Use standard HTTP status codes. Don't return `200 OK` with `{"error": ...}` in the body.
- Error response body shape:

  ```json
  {
    "error": {
      "code": "validation_failed",
      "message": "Human-readable summary",
      "details": [ {"field": "email", "issue": "invalid_format"} ]
    }
  }
  ```

- `code` is a stable, machine-readable identifier. `message` is for humans and may change.

## Pagination

- Cursor-based for lists that can grow unbounded: `?cursor=<opaque>&limit=50`.
- Response includes `next_cursor` (null when exhausted) and `limit`.
- Avoid offset pagination for large collections — it degrades and drifts under writes.

## Authentication

- Bearer tokens via `Authorization: Bearer <token>`.
- Never accept credentials in query strings (they leak into logs and referrers).
- All endpoints require auth by default; public endpoints are the exception and must be explicitly annotated.

## Documentation

- Every endpoint has an OpenAPI entry. The spec is the source of truth — client SDKs are generated from it, not hand-written.
- Every new endpoint lands with its OpenAPI update in the same PR.
