# Testing Guidelines
<!-- Owner: QA Engineer. Produced in Phase 2, applied through Phase 3. -->

## Test Pyramid (target ratio, not a hard rule — adjust with a stated reason)
- Unit: ~70% of tests. Pure functions, business logic, no I/O — mock external dependencies.
- Integration: ~20%. Real database (test instance, never production), real internal services.
- End-to-end: ~10%. Critical user flows only, through the actual UI/API surface.

## Contract Tests
Verify implementation matches `api-contract.md` exactly — every documented status code, every
required field, every error shape. Run on every PR touching backend or frontend code that crosses
the contract boundary. A contract test failure blocks merge regardless of other test results.

## Coverage
- Target: 80% line coverage as a floor, not a goal to optimize toward — 100% coverage with no
  edge-case tests is worse than 80% that covers every branch in `guardrails.md` Invariants.
- Every invariant in `guardrails.md` gets at least one test that tries to violate it.

## Concurrency Tests
Every resource `data-schema.md`'s Concurrency & Transactions section names as contended gets at
least one test that fires two (or more) requests at it concurrently and asserts exactly one
succeeds — not a sequential test that happens to touch the same row twice. A locking pattern that
only passes under sequential testing hasn't actually been tested. Backend Engineer writes the
first one; QA and Security Analyst independently re-verify it rather than trusting it passed.

## Flaky Test Protocol
A test that fails intermittently is quarantined (marked skip, ticket filed) within one failure,
not re-run until green. A flaky test hides real bugs; it does not get to stay in the required
suite while "probably fine."

## Performance Tests
- Load: [target, e.g. 1000 req/s]
- Latency: matches the budget in `guardrails.md` Performance section — same number, not a
  separately invented one.
