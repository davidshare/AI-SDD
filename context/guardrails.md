# Guardrails
<!-- Owner: Systems Architect (sole writer, per concurrency-protocol.md's single-writer-per-file
     principle). Security Analyst reviews this file during its Phase 2 design smell check and
     flags gaps in its handoff/security report; it never edits this file directly — Systems
     Architect incorporates the flagged gaps and writes the update. -->

## Security
- Passwords: bcrypt, cost factor 12 minimum.
- Tokens: [lifetime], refresh strategy: [...].
- Rate limiting: [limit] per [window] per [IP/user/API key].
- All external input validated at the boundary before it touches business logic (allow-list, not
  block-list, where the input space is enumerable).

## Invariants
<!-- State machine and business rules that must never be violated by any code path, including
     edge cases and admin overrides. -->
- [e.g. "A task has exactly one owner at a time, or none"]
- [e.g. "A record cannot transition from `done` back to `backlog`"]

## Performance
- [e.g. "API responses under 200ms p95"]
- Database queries must use an index for their primary filter/sort column — no unindexed full
  table scans in a hot path.

## Rollback Protocol
- Deployment failure → automatic rollback to the previous known-good version within [time budget].
- Keep the last [N] deployable artifacts available for immediate rollback.
- Rollback is tested in staging before it's trusted in production (see `gate-checks.md` Phase 4).
