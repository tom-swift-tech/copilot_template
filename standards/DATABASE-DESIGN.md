# Database Design Standards

> Starter example. Replace or expand for your project.

## Scope

These standards apply to relational databases (PostgreSQL, SQL Server, MySQL) used as systems of record. Document stores and caches are out of scope.

## Schema Conventions

- Table names: `snake_case`, plural nouns: `users`, `order_items`. Never `tbl_` prefixes or Hungarian notation.
- Column names: `snake_case`. Boolean columns named as predicates: `is_active`, `has_verified_email`, not `active` / `verified`.
- Primary keys: `id` (UUID v7 preferred for time-ordered uniqueness, or `bigserial` if you have hard reasons against UUIDs).
- Foreign keys: `<referenced_table_singular>_id` — e.g., `user_id`, `order_id`.
- Timestamps: `created_at`, `updated_at`, `deleted_at` (for soft deletes). All `TIMESTAMPTZ`, never naive.
- Money: integer minor units (`amount_cents`), never `FLOAT`/`DOUBLE`. Currency in a separate `currency` column.

## Constraints and Integrity

- **NOT NULL by default.** Nullable columns are the exception and must be justified.
- Foreign keys are real database constraints, not just application convention. `ON DELETE` behavior is explicit (`CASCADE`, `RESTRICT`, `SET NULL`) and documented in the migration.
- Use `CHECK` constraints for enums and bounded ranges. Don't trust application code to enforce them.
- Unique constraints at the database level for any uniqueness the app relies on.

## Indexes

- Index every foreign key. PostgreSQL doesn't do this automatically.
- Index columns used in `WHERE`, `ORDER BY`, and `JOIN` clauses on hot paths.
- Composite indexes follow the leftmost-prefix rule — order columns by selectivity.
- Don't index everything: indexes cost write throughput and storage. Measure first.
- Drop unused indexes — `pg_stat_user_indexes` will tell you which they are.

## Migrations

- One migration = one logical change. Never bundle unrelated schema changes.
- Migrations are **forward-only** in production. Rollbacks are a new migration, not a `down` script.
- Every migration must be safe to run on a live database with concurrent traffic:
  - No long table locks. Use `ALTER TABLE ... ADD COLUMN` (instant) over `ALTER COLUMN TYPE` (rewrites).
  - For NOT NULL on existing tables: add as nullable → backfill → add constraint.
  - For renames: add new column → dual-write → backfill → swap reads → drop old. Never rename in place under load.
- Migrations are reviewed against the same standards as application code.

## Querying

- Always use parameterized queries. String concatenation is a SQL injection vector — there are no exceptions.
- Avoid `SELECT *` in application code. Name the columns you need; schema changes shouldn't silently break callers.
- N+1 queries are bugs. Catch them in code review or with query-count assertions in tests.
- Long-running queries get a statement timeout. A query without a deadline is a query that can take down the database.

## Soft Deletes vs Hard Deletes

- Default to soft deletes (`deleted_at`) for user-facing records. Hard delete only when there's a compliance reason (GDPR right-to-erasure).
- Soft-deleted rows must be filtered out of every query that doesn't explicitly want them. Use a partial index or row-level security to make this hard to forget.

## Test and Validate (mandatory)

> Schema is the most expensive thing to get wrong. Validate every change before it touches production.

1. **Migration runs against a non-empty staging database** — not just an empty test schema. The bug shows up at scale, not in CI.
2. **Migration is idempotent or guarded.** Re-running it is either a no-op or a clear error. No "oops it ran twice" partial states.
3. **Rollback path tested in non-prod.** A rollback you've never run is not a rollback you have.
4. **Lock impact measured.** For any `ALTER TABLE`, check `pg_stat_activity` during a staging run for blocked queries.
5. **Constraint coverage:** for every new column, write at least one test that exercises the constraint (insert that should fail, insert that should succeed).
6. **Query plans inspected on hot paths.** `EXPLAIN ANALYZE` for any query that runs more than 10x/second.
