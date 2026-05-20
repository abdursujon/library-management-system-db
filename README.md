# Library Management System DB

A practical guide to reach **software engineering level SQL proficiency** using PostgreSQL.
The repo starts from beginner level and explores advance topic incrementally. 

> Stack: PostgreSQL 14+ on Linux. Most concepts transfer to MySQL, SQLite, MSSQL and dialect-specific items are marked **[PG]**.

  ---

## How to use the repo to gain proficiency in SQL 
1. Set up PostgreSQL (Phase 0).
2. Work through each phase in sequence — read the topic list, then write the SQL yourself against the `library_management_system` DB.
3. By the end of Phase 10 we will have a production-quality library schema and the SQL skills to own a real database at work.

  ---

## Phase 0 — Setup

### Install PostgreSQL 

**Linux**
  ```bash
  sudo apt update
  sudo apt install postgresql postgresql-contrib
  ```
**macOS**
  ```bash
  brew install postgresql@16
  brew services start postgresql@16
  ```

**Windows**
installer from https://www.postgresql.org/download/windows/

### Service control
  ```bash
  sudo systemctl status postgresql
  sudo systemctl enable postgresql
  sudo systemctl start postgresql
  psql --version
  ```

### Create the database
  ```bash
  sudo -u postgres psql
  ```
  ```sql
  CREATE DATABASE library_management_system;
  \c library_management_system
  ```

### Create a user & grant ownership
  ```sql
  CREATE USER username WITH PASSWORD 'new_password';
  ALTER DATABASE library_management_system OWNER TO username;
  \c library_management_system
  ALTER SCHEMA public OWNER TO username;
  GRANT ALL ON SCHEMA public TO username;
  GRANT CREATE ON SCHEMA public TO username;
  ```

### Dump / restore
  ```bash
  pg_dump library_management_system > library_management_system.sql
  psql library_management_system < library_management_system.sql
  ```

### Useful `psql` meta-commands
`\l` list DBs · `\c db` connect · `\dt` tables · `\d table` describe · `\du` users · `\df` functions · `\di` indexes · `\q` quit · `\timing on` show query time

  ---

## Phase 1 — Data Types

### Numeric
`SMALLINT` `INTEGER` `BIGINT` `DECIMAL(p,s)` `NUMERIC(p,s)` `REAL` `DOUBLE PRECISION`

  ```sql
  CREATE TABLE numeric_demo (
    id          SERIAL PRIMARY KEY,
    small_int   SMALLINT,
    int_val     INTEGER,
    big_int_val BIGINT,
    decimal_val DECIMAL(10, 3),
    real_val    REAL,
    double_val  DOUBLE PRECISION
  );
  ```

### Auto-increment
`SMALLSERIAL` `SERIAL` `BIGSERIAL` — and the modern alternative: **`GENERATED ALWAYS AS IDENTITY`** (SQL standard, preferred for new code).

### Character / Text
`CHAR(n)` `VARCHAR(n)` `TEXT` — in Postgres there is **no performance gap** between `VARCHAR` and `TEXT`; prefer `TEXT` unless you need a length cap.

### Boolean
`BOOLEAN` → `TRUE` / `FALSE` / `NULL`

### Date / Time
`DATE` `TIME` `TIMESTAMP` `TIMESTAMPTZ` `INTERVAL`
**Rule of thumb:** always use `TIMESTAMPTZ` for "when something happened."

### UUID **[PG]**
`UUID` — use with `gen_random_uuid()` (built-in from PG 13+).

### JSON **[PG]**
`JSON` (text-stored) vs `JSONB` (binary, indexable) — **always pick `JSONB`** in new code.

### Binary
`BYTEA`

### Network **[PG]**
`INET` `CIDR` `MACADDR`

### Geometric **[PG]**
`POINT` `LINE` `LSEG` `BOX` `CIRCLE` `PATH` `POLYGON`

### Arrays **[PG]**
`INTEGER[]` `TEXT[]` …

### Special
`NULL` (a state, not a type) · `ENUM` (user-defined) · range types `INT4RANGE` `TSRANGE` `DATERANGE`

  ---

## Phase 2 — Schema & Tables

1. `CREATE TABLE` — column definitions
2. Primary keys (`PRIMARY KEY`, composite keys)
3. Foreign keys (`REFERENCES`, `ON DELETE CASCADE` / `SET NULL` / `RESTRICT`, `ON UPDATE`)
4. Constraints: `NOT NULL`, `UNIQUE`, `CHECK`, `DEFAULT`, named constraints
5. `ALTER TABLE` — add / drop / rename columns, change types, add constraints
6. `DROP TABLE` (and `CASCADE` vs `RESTRICT`)
7. `TRUNCATE` vs `DELETE`
8. Schemas / namespaces (`CREATE SCHEMA`, `search_path`)
9. Temporary tables
10. Generated columns (`GENERATED ALWAYS AS (...) STORED`)

  ---

## Phase 3 — CRUD

1. `INSERT INTO ... VALUES (...)`, bulk insert
2. `INSERT ... SELECT` (copy between tables)
3. `SELECT` — columns, `*`, expressions, aliases
4. `UPDATE` (always with a `WHERE` — be paranoid)
5. `DELETE` (same warning)
6. `RETURNING` clause **[PG]** — get back the rows you just changed
7. **UPSERT**: `INSERT ... ON CONFLICT (col) DO UPDATE SET ...` **[PG]**

  ---

## Phase 4 — Querying

1. `WHERE` filtering: `=`, `<>`, `<`, `>`, `BETWEEN`, `IN`, `IS NULL`, `IS NOT NULL`
2. Logical operators: `AND`, `OR`, `NOT` (and operator precedence)
3. Pattern matching: `LIKE`, `ILIKE`, `~` (regex) **[PG]**
4. `ORDER BY` (asc/desc, multi-column, `NULLS FIRST/LAST`)
5. `LIMIT` / `OFFSET` — and why offset is bad for deep pagination (use **keyset / cursor pagination**)
6. `DISTINCT` and `DISTINCT ON (col)` **[PG]**
7. Aggregates: `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`, `STRING_AGG`, `ARRAY_AGG`
8. `GROUP BY` (and the rule: every non-aggregate column must appear in GROUP BY)
9. `HAVING` (filtering on aggregates)
10. `CASE WHEN ... THEN ... ELSE ... END`
11. Null handling: `COALESCE`, `NULLIF`, three-valued logic gotchas

  ---

## Phase 5 — Joins & Relations

1. `INNER JOIN`
2. `LEFT JOIN` / `RIGHT JOIN`
3. `FULL OUTER JOIN`
4. `CROSS JOIN` (Cartesian product)
5. Self join (table joined to itself, e.g. employees → manager)
6. `USING(col)` vs `ON a.col = b.col`
7. Anti-join pattern: `LEFT JOIN ... WHERE b.id IS NULL`
8. Semi-join pattern: `EXISTS` / `NOT EXISTS`
9. Multi-table joins and join order
10. Lateral join (`LATERAL`) **[PG]** — per-row subqueries

  ---

## Phase 6 — Advanced Queries

1. Subqueries (in `SELECT`, `FROM`, `WHERE`)
2. Correlated subqueries (depend on the outer row)
3. Set operations: `UNION`, `UNION ALL`, `INTERSECT`, `EXCEPT`
4. Common Table Expressions: `WITH name AS (...) SELECT ...`
5. Recursive CTEs: `WITH RECURSIVE` — trees, hierarchies, graph traversal
6. Window functions:
    - `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`
    - `LAG()`, `LEAD()`, `FIRST_VALUE()`, `LAST_VALUE()`
    - `SUM()/AVG() OVER (PARTITION BY ... ORDER BY ...)` — running totals & moving averages
    - Frame clauses: `ROWS BETWEEN ... AND ...`
7. Pivoting with `CASE` + `GROUP BY` (and `crosstab` **[PG]**)

  ---

## Phase 7 — Indexes & Performance

1. **Why** indexes — B-tree as ordered lookup; tradeoff with write speed.
2. Index types **[PG]**: `BTREE` (default), `HASH`, `GIN` (JSONB, full-text, arrays), `GiST`, `BRIN` (huge time-series tables), `SP-GiST`
3. Composite indexes & **column order matters** (leftmost prefix rule)
4. Partial indexes: `CREATE INDEX ... WHERE is_active`
5. Covering / `INCLUDE` indexes
6. Expression indexes: `CREATE INDEX ON t (LOWER(email))`
7. Unique indexes (and the implicit one behind `UNIQUE` constraints)
8. When indexes **hurt**: write-heavy tables, low-cardinality columns
9. `EXPLAIN` and `EXPLAIN ANALYZE` — read seq scan vs index scan vs bitmap scan
10. `ANALYZE` / `VACUUM` / `VACUUM FULL` — statistics & bloat
11. N+1 query problem (mostly an ORM trap)
12. Connection pooling (PgBouncer concept) — why opening a connection per request kills you

  ---

## Phase 8 — Transactions & Concurrency

1. ACID properties (Atomicity, Consistency, Isolation, Durability)
2. `BEGIN` / `COMMIT` / `ROLLBACK`
3. `SAVEPOINT` and `ROLLBACK TO SAVEPOINT`
4. Isolation levels: `READ COMMITTED` (default), `REPEATABLE READ`, `SERIALIZABLE`
5. Read phenomena: dirty read, non-repeatable read, phantom read
6. Row-level locks: `SELECT ... FOR UPDATE`, `FOR SHARE`, `FOR UPDATE SKIP LOCKED` (job-queue pattern)
7. Deadlocks — what causes them, how Postgres resolves them
8. Optimistic vs pessimistic locking
9. MVCC (Multi-Version Concurrency Control) **[PG]** — high-level mental model

  ---

## Phase 9 — Programming the Database

1. **Views** — `CREATE VIEW` for query reuse / encapsulation
2. **Materialized views** — `CREATE MATERIALIZED VIEW`, `REFRESH MATERIALIZED VIEW [CONCURRENTLY]`
3. **Functions** in PL/pgSQL **[PG]** — `CREATE FUNCTION ... RETURNS ... LANGUAGE plpgsql`
4. **Stored procedures** (`CREATE PROCEDURE`, `CALL`) — can manage transactions
5. **Triggers** — `BEFORE/AFTER INSERT/UPDATE/DELETE FOR EACH ROW` (audit logs, derived columns, `updated_at`)
6. Built-in functions worth knowing:
    - String: `CONCAT`, `LENGTH`, `SUBSTRING`, `TRIM`, `LOWER`, `UPPER`, `REPLACE`, `SPLIT_PART`
    - Date/time: `NOW()`, `CURRENT_DATE`, `AGE()`, `DATE_TRUNC`, `EXTRACT`, `INTERVAL '1 day'`
    - Conditional: `COALESCE`, `NULLIF`, `GREATEST`, `LEAST`
    - Generate: `generate_series(1, 10)` **[PG]**

  ---

## Phase 10 — PostgreSQL Power Features

1. **JSONB** operators: `->`, `->>`, `#>`, `#>>`, `@>` (contains), `?` (key exists), `||` (merge), `-` (remove)
2. JSONB indexing with GIN
3. **Full-text search**: `tsvector`, `tsquery`, `to_tsvector`, `@@`, ranking with `ts_rank`
4. **Arrays**: `ANY`, `ALL`, `unnest`, `array_agg`, array containment
5. **Range types** & exclusion constraints (e.g. no overlapping bookings)
6. **Extensions**: `pg_trgm` (fuzzy search), `uuid-ossp`, `postgis` (geo), `pgcrypto`
7. **`LISTEN` / `NOTIFY`** — pub/sub inside Postgres
8. **`COPY`** — bulk import/export, vastly faster than `INSERT`s

  ---

## Phase 11 — Design & Modeling

1. ER modeling — entities, relationships, cardinality (1:1, 1:N, M:N)
2. **M:N via junction tables** (e.g. `book_authors`)
3. Normalization: **1NF**, **2NF**, **3NF**, BCNF (1 sentence each is enough to recognize them)
4. **Denormalization tradeoffs** — when read performance beats purity
5. Surrogate vs natural keys (`id BIGSERIAL` vs `isbn`)
6. **UUID vs integer keys** — distributed systems vs locality
7. Soft deletes (`deleted_at TIMESTAMPTZ`) — and what they cost
8. Audit pattern — `created_at`, `updated_at` (driven by trigger)
9. History / temporal tables (`*_history` shadow tables)
10. Choosing data types: cost of `TEXT` vs `VARCHAR(50)`, why `TIMESTAMPTZ` over `TIMESTAMP`
11. **Reading an existing schema** — `\d+`, `information_schema`, `pg_catalog`

  ---

## Phase 12 — Production / Engineering Skills

1. **Schema migrations** — versioned, idempotent, reversible. Tools: Flyway, Liquibase, Alembic, Prisma Migrate, golang-migrate, Knex
2. **Zero-downtime DDL** — adding `NOT NULL` columns to large tables safely (expand → backfill → contract)
3. **SQL injection** — never interpolate user input; always use **parameterized queries** / prepared statements
4. **Roles & permissions** — `CREATE ROLE`, `GRANT`, `REVOKE`, principle of least privilege
5. **Row-level security** (`RLS`) **[PG]** — per-tenant isolation
6. Backups: `pg_dump`, `pg_dumpall`, `pg_basebackup`, WAL archiving, PITR
7. **Replication** (conceptual): primary/replica, sync vs async, read replicas
8. **Partitioning** (conceptual): range / list / hash; archiving old data
9. **Sharding** (conceptual): when one DB stops being enough
10. **Monitoring**: `pg_stat_statements`, `pg_stat_activity`, slow query log
11. **Connection pooling**: PgBouncer transaction-mode vs session-mode
12. ORMs vs raw SQL — what they hide and when to bypass them (`Sequelize`, `Prisma`, `SQLAlchemy`, `Hibernate`, `GORM`, `Diesel`)
13. Idempotency: writing migrations and jobs you can safely re-run