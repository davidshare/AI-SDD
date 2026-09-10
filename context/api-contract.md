# API Contract
<!-- Owner: Systems Architect. Produced in Phase 2. THE authority for request/response shapes —
     Backend Engineer implements to this exactly; Frontend Engineer integrates against this
     exactly; QA writes contract tests directly from this. -->

## Conventions (apply to every endpoint below)
- Base path: `/api/v1` — version in the path, not a header, so old clients keep working.
- All error responses share one shape:
  ```json
  { "error": { "code": "string_snake_case", "message": "human readable", "details": {} } }
  ```
- Timestamps: ISO 8601 UTC (`2026-09-10T14:30:00Z`).
- Pagination (any list endpoint): `?cursor=&limit=` request params;
  `{ "items": [...], "next_cursor": "string|null" }` response shape.
- Idempotency: any `POST` that creates a resource accepts an optional `Idempotency-Key` header;
  a repeated key returns the original result instead of creating a duplicate.

## [Resource Name]

### POST /api/v1/[resource]
**Auth:** [required scope, or "none"]
**Request:**
```json
{ "field": "type — constraints" }
```
**Response 201:**
```json
{ "id": "uuid", "field": "type", "created_at": "timestamp" }
```
**Response 400:** validation error, shape per Conventions above.
**Response 409:** [when applicable — e.g. duplicate claim]

---
### Example: POST /api/v1/tasks/:id/claim
**Auth:** agent API key
**Request:** `{ "agent_id": "string" }`
**Response 200:** `{ "status": "claimed" }`
**Response 409:** `{ "error": { "code": "already_claimed", "message": "Task already claimed by another agent", "details": { "owner": "agent-002" } } }`
