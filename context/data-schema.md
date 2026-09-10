# Data Schema
<!-- Owner: Database Designer. Produced in Phase 2.
     Rule: relationships between TASKS/ISSUES live in dependency-graph.json ONLY — do not
     duplicate task dependency info here. This file is for actual database tables. -->

## Normalization policy
Default to 3NF. Denormalize only with a stated, measured reason (e.g. a read-heavy counter that
would otherwise require an expensive join on every request) — note the reason inline where you do.

## Tables
<!-- Syntax below (DEFAULT NOW(), etc.) assumes Postgres-family SQL — adjust to whatever engine
     architecture.md's Tech Stack actually names (e.g. GETDATE() on SQL Server). -->

### [table_name]
| Column | Type | Constraints |
|---|---|---|
| id | UUID | PRIMARY KEY |
| [column] | [type] | [NOT NULL / UNIQUE / DEFAULT / FK -> table.column] |
| created_at | TIMESTAMP | DEFAULT NOW() |
| updated_at | TIMESTAMP | DEFAULT NOW(), updated on write |

## Relationships
- `[table].[fk_column]` → `[table].[column]` — [ON DELETE behavior and why]

## Concurrency & Transactions
<!-- Any row two agents/requests could try to update at the same time (a claimable task, a
     counter, anything guardrails.md's Invariants call "exactly one X at a time") needs a stated
     mechanism here, not just a constraint that assumes writes never race. -->
- Isolation level: [e.g. "READ COMMITTED, default" — state it, don't leave it implicit]
- `[table]` (contended on: [column, e.g. "status"]): [locking pattern — e.g. conditional
  `UPDATE ... WHERE status = 'backlog'` and check rows-affected = 1; or `SELECT ... FOR UPDATE`;
  or an optimistic-lock `version` column] — enforces guardrails.md's "[invariant]"

## Indexes
- `[table].[column]` — [what query pattern this serves; don't index speculatively]

## Migration convention
Numbered, forward-only: `NNNN_description.sql`. Never edit a migration once merged to `main` —
write a new one. Backend Engineer owns writing migrations; Database Designer reviews before merge.

---
### Example
| id | UUID | PK |
|owner_id | UUID | FK -> agents.id, NULLABLE — null means unclaimed |
Index: `tasks.status` — every claim-check query filters by status first.
