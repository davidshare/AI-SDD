# Coding Standards
<!-- Owner: Systems Architect drafts in Phase 2; Code Reviewer enforces in Phase 3. -->

## Workflow
- Branching: one branch per task (`task/issue-XXX`), see `concurrency-protocol.md`.
- Commits: Conventional Commits (`feat:`, `fix:`, `docs:`, `refactor:`, `test:`).
- No direct commits to `main` — everything merges via the gate process.

## Error Handling
- Never swallow an exception silently. Log with context (request ID, user/agent ID) or re-raise.
- Return structured errors matching `api-contract.md` Conventions — never a raw stack trace to a
  client.
- Fail loudly in development, gracefully in production (user-facing message + logged detail).

## Protected Components
- [List anything requiring extra sign-off before modification, e.g.: "auth middleware — Security
  Analyst must review any change", "database migrations — Database Designer reviews before merge"]

## Code Style
- [Language]: [formatter, linter, line length — name the exact tools, e.g. "Python: black,
  ruff, 88 char"]
- No commented-out code merged to `main`. Delete it; git history has it if needed.
- Functions: prefer small and named for what they do over long and commented.

## Secrets
- Never hardcode a credential, API key, or connection string in source. Reference the
  environment variable name (see `environment.md`) — never its value — anywhere in code, comments,
  or commit messages.
