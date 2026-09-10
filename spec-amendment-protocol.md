# Spec Amendment Protocol

Gates in `gate-checks.md` are one-directional by design — but real projects discover spec problems
after the phase that owned them has closed. This protocol is the sanctioned way to fix that
without silently reopening a phase or letting agents work from stale specs.

## 1. Who can trigger this

Any agent, on discovering that a file in `context/` is wrong, ambiguous, or missing something it
needs. This is a normal, expected event — not a failure of the agent that finds it.

## 2. Procedure

1. Agent sends `status: "blocked"` with `blocked_reason` describing the specific spec defect
   (quote the exact section, not a general complaint).
2. Tech Lead evaluates: is this a real defect, or a misreading of an existing spec? If the latter,
   resolve by pointing the agent to the correct section — no amendment needed.
3. If real, Tech Lead routes to the file's owning agent (Systems Architect for
   `architecture.md`/`api-contract.md`, Database Designer for `data-schema.md`, UI/UX Designer for
   `design-system.md`, Product Manager/Discovery for `project-overview.md`).
4. Owning agent drafts the correction and assigns a version bump per `spec-versions/` rules:
   - **Patch** (v1.0.**1**): clarification, no behavior change for already-built code.
   - **Minor** (v1.**1**.0): additive — existing code still works, new capability added.
   - **Major** (v**2**.0.0): breaking — existing code built against the old version must change.
     Requires human sign-off (see `gate-checks.md`).
5. Tech Lead creates the new version folder under `spec-versions/`, updates the `current` pointer
   (a plain-text file `spec-versions/current.txt` containing e.g. `v1.1.0` — not a symlink; this
   keeps the pointer readable by every agent regardless of OS or how the repo is checked out).
6. Tech Lead checks `dependency-graph.json` for every task whose `depends_on` or file-reads
   overlap the changed section, and re-notifies each: their `spec_version_used` is now stale
   (`handoff-protocol.md` §1). In-flight tasks are re-scoped or re-run at Tech Lead's judgment,
   depending on whether the change actually affects work already in progress.
7. Changelog entry added to `progress-tracker.md` (Tech Lead is the writer, per the single-writer
   rule).

## 3. What this is not

This is not a way to skip Phase 2 rigor by "amending" a spec that was simply never thought
through the first time. If Phase 2's exit gate is being re-opened more than once or twice per
project, that's a signal Phase 2 needs more time before the exit gate is passed next time — not a
signal this protocol needs to run faster.
