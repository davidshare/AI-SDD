# Gate Checks

No phase starts until the previous phase's exit gate passes. Each gate names its keeper(s) —
the agent(s) verifying it — and, where marked, a **required human sign-off** that no agent can
substitute for.

## Phase 1: Discovery & Definition
**Entry:** User has provided a raw idea. Product Discovery Agent initialized.
**Exit:**
- [ ] `project-overview.md` complete (all sections filled, no placeholder text remaining)
- [ ] `glossary.md` defined
- [ ] Personas and core flows identified
- [ ] **Human has explicitly approved the PRD** ← required human sign-off, no exceptions
**Gate keeper:** Tech Lead verifies checklist; human approval is separate and mandatory.

## Phase 2: System Design
**Entry:** Phase 1 exit passed. `project-overview.md` versioned as `v1.0.0`.
**Exit:**
- [ ] `architecture.md`, `api-contract.md`, `data-schema.md` complete
- [ ] `design-system.md` complete (if the project has a frontend)
- [ ] ADRs written for every major decision (db choice, auth model, API style — not variable naming)
- [ ] `dependency-graph.json` created and passes cycle detection (topological sort succeeds)
- [ ] No relationship data duplicated between `dependency-graph.json` and issue files
**Gate keeper:** Tech Lead + Systems Architect sign off on consistency across files. Security
Analyst does a pass for obvious design-level issues (missing auth model, secrets in plaintext
spec) — this is a smell check, not the full audit that happens in Phase 3.

## Phase 3: Implementation
**Entry:** Phase 2 exit passed. Specs versioned. Issues created with Definition of Done and
correct `depends_on` entries in `dependency-graph.json`.
**Exit:**
- [ ] All code written, each task's worktree merged (see `concurrency-protocol.md`)
- [ ] Code Reviewer approved every PR against `coding-standards.md`
- [ ] QA: 100% of critical-path tests pass; contract tests pass against `api-contract.md`
- [ ] Security Analyst: zero unresolved Critical or High findings
- [ ] `handoff-log.jsonl` reviewed for recurring failure patterns before closing the phase
**Gate keeper:** Tech Lead verifies all of the above; Code Reviewer, QA, and Security Analyst each
sign off independently — one of them objecting blocks the gate regardless of the others.

## Phase 4: Deployment
**Entry:** Phase 3 exit passed. All code merged to `main`.
**Exit:**
- [ ] Docker images build cleanly
- [ ] CI/CD pipeline green
- [ ] Staging deploy succeeds, smoke tests pass
- [ ] Rollback procedure verified reachable (not just documented — actually tested in staging)
- [ ] **Human has explicitly approved the production deploy** ← required human sign-off, no exceptions
**Gate keeper:** DevOps Engineer verifies deployment mechanics; QA runs smoke tests; human sign-off
is separate and mandatory regardless of how clean the automated checks are.

## Re-opening a closed gate

A gate that already passed can be re-opened only through `spec-amendment-protocol.md` — never by
an agent quietly redoing work because it disagrees with a prior decision. If Phase 2 already
closed and a Phase 3 agent finds the API contract wrong, that's a spec amendment, not a silent
Phase 2 redo.
