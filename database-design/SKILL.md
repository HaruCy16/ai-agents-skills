---
name: database-design
description: Model schemas and optimize queries for relational databases (PostgreSQL, MySQL, SQLite) and advise on NoSQL choices: normalization, keys, relationships, indexes, constraints, migrations, transactions, and N+1/slow-query fixes. Use this skill whenever the user designs tables or an ERD, writes migrations, picks a database, uses an ORM (Prisma, Drizzle, Sequelize, SQLAlchemy), or has slow queries, even if they only say "store this data."
---

# Database Design

Default to PostgreSQL. Model the domain first, then optimize from measurements.

## Process
1. List entities, their attributes, and relationships (1:1, 1:N, N:M) and the main access patterns (what is read/written most).
2. Normalize to 3NF; denormalize only for a measured read bottleneck.
3. Add constraints, then indexes, then migrations.

## Schema rules
- Every table has a primary key (`bigint generated always as identity` or UUIDv7).
- `NOT NULL` by default; use `CHECK`, `UNIQUE`, and `FOREIGN KEY` constraints. Let the database enforce integrity.
- Types: `timestamptz` (store UTC), `numeric`/integer minor units for money (never float), `text` over arbitrary `varchar(n)` in Postgres, `boolean`, `jsonb` for genuinely flexible data only.
- N:M relationships use a join table with a composite key.
- Soft delete (`deleted_at`) only when required; remember to filter and index for it.
- Add `created_at` / `updated_at`.
- Naming: `snake_case`, plural table names, `<table>_id` foreign keys.

## Indexing
- Index foreign keys and columns used in `WHERE`, `JOIN`, `ORDER BY`.
- Composite index order: equality columns first, then range/sort columns.
- Partial indexes for filtered subsets; covering indexes (`INCLUDE`) for hot queries.
- Verify with `EXPLAIN (ANALYZE, BUFFERS)`. Watch for sequential scans on big tables.
- Don't over-index: each index slows writes.

## Query performance
- Select only needed columns; avoid `SELECT *`.
- Paginate with keyset (`WHERE id > $last ORDER BY id LIMIT n`) over large `OFFSET`.
- **N+1:** in ORMs use eager loading/joins (`include`, `joinedload`) or batch with `IN`.
- Use connection pooling (PgBouncer or the ORM pool).

## Transactions and integrity
- Wrap multi-step writes in a transaction; keep transactions short.
- Understand isolation: use `SELECT ... FOR UPDATE` or optimistic version columns to prevent lost updates.
- Make writes idempotent (unique constraint on idempotency key).

## Migrations
- Versioned, reviewed, committed. Never edit an applied migration.
- Zero-downtime pattern: add nullable column → backfill → add constraint → switch code → drop old.
- Always have a rollback plan and a backup before destructive changes.

## Security
- Parameterized queries only (see `web-security`); least-privilege DB roles; encrypt sensitive columns where required; never log credentials.

## SQL vs NoSQL
Relational for structured, relational, transactional data (most apps). Document stores for flexible, aggregate-shaped data; Redis for cache/queues/rate limits; search engines for full-text. State the trade-off when recommending.

## Output
Provide DDL (`CREATE TABLE ...`, indexes, constraints), an ERD in Mermaid, and the migration. For performance reviews: query → plan finding → fix → expected effect.
