# Architecture
<!-- Owner: Systems Architect. Produced in Phase 2. -->

## Tech Stack
- Backend: [language, framework, version]
- Database: [engine, version]
- Frontend: [framework, version] (omit section if backend-only, see Configuration Options)
- Deployment: [target]

## System Boundaries
- External: [service] — [why, what data crosses the boundary]
- Failure behavior: [what happens when this boundary is unreachable, slow, or returns garbage —
  timeout budget, retry policy, fail-open vs. fail-closed]

## Authentication Model
- [mechanism]: [who uses it, token lifetime, refresh strategy]

## System Design
```
[ASCII or mermaid diagram of components and data flow — keep it text-based so it stays
diffable and every agent can read it without rendering]
```

## Integration Points
- [webhook/API] — [trigger, payload shape, idempotency handling]
- Failure behavior: [timeout/retry policy, circuit breaker if any, what the caller does on a
  failed or duplicate delivery — not just the happy-path payload]

## Non-Functional Requirements
- Latency budget: [e.g. p95 < 200ms for API responses]
- Availability target: [e.g. 99.5%]
- Expected scale: [requests/day, data volume — sizing decisions trace back to this]

---
### Note on ADRs
Anything in this file that involved rejecting an alternative (Postgres over Mongo, REST over
GraphQL, JWT over sessions) gets its own file in `adr/`, not just a line here. This file states
the decision; the ADR states *why*.
