# Progress Tracker
<!-- WRITTEN ONLY BY: Tech Lead. See handoff-protocol.md §2 (single-writer rule).
     Every other agent proposes changes via state_updates in their handoff JSON — never edits
     this file directly, even in their own worktree copy. -->

## Current Phase
[Phase N: Name]

## Gate Status
<!-- One row per phase. Checklist items reference the exact items in gate-checks.md — don't
     paraphrase them here, just record pass/pending. A gate is "Passed" only when every checklist
     item is checked AND any required human sign-off is recorded below with who/when. -->
- **Phase 1 — Discovery & Definition**: [Passed | Pending] — checklist [N/M items] — human PRD
  sign-off: [recorded by X on date | not yet recorded]
- **Phase 2 — System Design**: [Passed | Pending] — checklist [N/M items]
- **Phase 3 — Implementation**: [Passed | Pending] — checklist [N/M items]
- **Phase 4 — Deployment**: [Passed | Pending] — checklist [N/M items] — human deploy
  sign-off: [recorded by X on date | not yet recorded]
- Major (breaking) spec version sign-offs: [version — recorded by X on date]

## Backend Progress
- **Completed**: N/M tasks
- **In Progress**: [issue-XXX — agent — since when]
- **Blocked**: [issue-XXX — reason — since when]
- **Next Up**: [issue-XXX]

## Frontend Progress
[same shape]

## Integration Progress
[same shape]

## DevOps Progress
[same shape]

## Changelog
<!-- Append-only. Newest last. One line per event: state change, spec amendment, gate pass. -->
- [date]: [event]

## Spec Version
Current: [vX.Y.Z] (see `spec-versions/current.txt`)
