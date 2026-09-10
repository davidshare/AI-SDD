# Issue: [Title]

## Metadata
- **ID**: issue-XXX  <!-- must match the key in dependency-graph.json -->
- **Domain**: backend | frontend | integration | devops
- **Priority**: high | medium | low

<!-- Status, Owner, Depends On, Blocks are NOT recorded here — see context/dependency-graph.json,
     which is the single source of truth for all of that. Recording them here too is exactly the
     duplication that caused spec drift in v1.0.0. -->

## Description
[What needs to be built, specifically enough that the assigned agent doesn't have to guess.]

## Acceptance Criteria
<!-- Be as thorough here as the feature itself. Cover realistic failure/misuse modes, not just the
     happy path — for EVERY domain, not just backend/auth: backend (bad/boundary/malicious input,
     concurrency, partial failures), frontend (empty/loading/error states, offline, double-submit,
     accessibility), devops (failed/partial deploy, missing secret, rollback), integration
     (third-party timeout, malformed/duplicate/out-of-order events). Pull concrete numbers from
     context/guardrails.md where relevant (rate limits, hashing cost, latency budget). "Passes
     Security Analyst review" below is a verification gate, not a substitute for specifying what
     should be true. -->
- [ ] [Criterion 1 — testable, not "works correctly"]
- [ ] Passes QA test suite
- [ ] Passes Security Analyst review (if it touches auth, input handling, or data access)

## Notes
[Anything the assigned agent needs that doesn't fit elsewhere — e.g. a known constraint from a
prior spec amendment.]
