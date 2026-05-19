# Library Management System DB 

This repo contains everything needed to reach software engineering level SQL proficiency.

## Topic Covered In Sequence 
1. Database creation and selection
- Creating The Database 
```bash
sudo systemctl enable postgresql
sudo systemctl start postgresql
sudo -u postgres psql
CREATE DATABASE library_management_system;
\c library_management_system
```

### Export database schema to project file 
pg_dump library_management_system > library_management_system.sql

2. Data types

### Numeric 
SMALLINT
INTEGER (INT)
BIGINT
DECIMAL
NUMERIC
REAL
DOUBLE PRECISION

Before creating table must do 
GRANT CREATE ON SCHEMA public TO username;

```sql
CREATE TABLE numeric_data_type_demo(
 id SERIAL Primary KEY,
 small_int SMALLINT,
 int_val  INTEGER,
 big_int_val BIGINT,
 decimal_val DECIMAL(10, 3),
 numeric_val REAL, 
 double_val DOUBLE PRECISION 
)
```
### Serial type demo
SMALLSERIAL
SERIAL
BIGSERIAL

```sql
CREATE TABLE serial_types_demo (
    small_id SMALLSERIAL,
    id SERIAL,
    big_id BIGSERIAL
);
```
### Character / Text 
CHAR(n)
VARCHAR(n)
TEXT

### Boolean 
TRUE / FALSE 

### Date/Time 
DATE
TIME
TIMESTAMP
TIMESTAMPTZ
INTERVAL

### UUID 
UUID

### JSON 
JSON
JSONB

### BINARY 
BYTEA

### NETWORK 
INET
CIDR
MACADDR

### GEOMETRIC 
POINT
LINE
LSEG
BOX
CIRCLE
PATH
POLYGON

### ARRAYS 
INTEGER[]
TEXT[]

### SPECIAL 
NULL (not a type, but a value state)
ENUM (user-defined)
RANGE types (INT4RANGE, TSRANGE, etc)


3. Table creation (CREATE TABLE)
4. Primary keys
5. Foreign keys
6. Constraints (NOT NULL, UNIQUE, CHECK, DEFAULT)
7. Insert data (INSERT INTO)
8. SELECT
9. WHERE filtering
10. ORDER BY
11. LIMIT and OFFSET
12. UPDATE
13. DELETE
14. Aggregate functions (COUNT, SUM, AVG, MIN, MAX)
15. GROUP BY
16. HAVING
17. Joins (INNER JOIN, LEFT JOIN, RIGHT JOIN, FULL JOIN)
18. Self join
19. Subqueries
20. Correlated subqueries
21. Set operations (UNION, UNION ALL, INTERSECT, EXCEPT)
22. CASE expressions
23. Indexes
24. Transactions (BEGIN, COMMIT, ROLLBACK)
25. Views
26. CTE (WITH clause)
27. Window functions (ROW_NUMBER, RANK, DENSE_RANK, LAG, LEAD)
28. Query performance (EXPLAIN, EXPLAIN ANALYZE)
29. Normalization (1NF, 2NF, 3NF)
30. Database design (relationships, ER modeling)

## PostgreSQL Setup
### Install PostgreSQL in linux 
```bash
sudo apt update 
sudo apt install postgresql postgresql-contrib
```

### Check Service Status 
```bash
sudo systemctl status postgresql
```
### Enable/Start PostgreSQL 
```bash
sudo systemctl enable postgresql
sudo systemctl start postgresql
```

### Verify Version 
```bash
psql --version
```

### Change owner name 
sudo -u postgres psql
ALTER DATABASE library_management_system OWNER TO username;
\c library_management_system
ALTER SCHEMA public OWNER TO username;
GRANT ALL ON SCHEMA public TO username;
