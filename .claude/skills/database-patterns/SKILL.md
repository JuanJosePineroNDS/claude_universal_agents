---
name: database-patterns
description: Universal database patterns for schema design, migrations, indexing, and query optimization across SQL and NoSQL
origin: manual
---

# Database Patterns

## When to Activate

- Working with any database (PostgreSQL, MySQL, MongoDB, Redis, SQLite)
- Designing schemas, writing migrations, or optimizing queries
- Setting up ORM configuration (Prisma, TypeORM, Sequelize, Eloquent, Active Record)

## Schema Design Principles

### Normalization vs Denormalization
- **Normalize** (3NF) by default for transactional data
- **Denormalize** only for read-heavy paths with measured performance needs
- Document the denormalization reason in a comment or ADR

### Naming Conventions
- Tables: plural snake_case (`user_accounts`, `order_items`)
- Columns: singular snake_case (`created_at`, `user_id`)
- Foreign keys: `{referenced_table_singular}_id`
- Indexes: `idx_{table}_{columns}`
- Constraints: `chk_{table}_{rule}`, `uq_{table}_{columns}`

## Migration Best Practices

```
1. Every schema change via migration (never manual DDL)
2. Migrations MUST be reversible (up + down)
3. Data migrations separate from schema migrations
4. Never modify a migration that has been applied in production
5. Test migrations against a copy of production data
```

### Safe Migration Patterns
- **Add column**: add nullable first, backfill, then add NOT NULL constraint
- **Remove column**: stop reading first, deploy, then drop in next migration
- **Rename column**: add new, backfill, update code, drop old (3 migrations)
- **Add index**: `CREATE INDEX CONCURRENTLY` (PostgreSQL) or equivalent

## Indexing Strategy

### When to Add an Index
- Columns in WHERE clauses with high selectivity
- Foreign key columns (always)
- Columns used in ORDER BY
- Columns in JOIN conditions

### When NOT to Add an Index
- Tables with <1000 rows (full scan is faster)
- Columns with low cardinality (boolean, status with 3 values)
- Write-heavy tables where read performance isn't critical
- Columns rarely queried

### Composite Index Order
Follow the ESR rule: **Equality > Sort > Range**
```sql
-- Query: WHERE status = 'active' AND created_at > '2024-01-01' ORDER BY name
-- Index: (status, name, created_at) -- equality, sort, range
```

## Query Optimization

### Anti-Patterns to Avoid
- `SELECT *` — always specify columns
- N+1 queries — use eager loading or JOINs
- Functions on indexed columns in WHERE (`WHERE YEAR(created_at) = 2024`)
- Missing LIMIT on unbounded queries
- Implicit type conversions in comparisons

### Performance Checklist
- [ ] Every query has an execution plan review (`EXPLAIN ANALYZE`)
- [ ] No full table scans on tables >10K rows
- [ ] Pagination uses cursor-based (keyset) for large datasets
- [ ] Batch operations for bulk inserts/updates
- [ ] Connection pooling configured (PgBouncer, HikariCP, etc.)

## Transaction Patterns

- Keep transactions short (no external API calls inside)
- Use appropriate isolation level (READ COMMITTED default, SERIALIZABLE for money)
- Implement idempotency keys for retryable operations
- Always handle deadlocks with retry logic

## Backup & Recovery

- Automated daily backups with tested restore procedure
- Point-in-time recovery enabled for production
- Backup retention: 7 days minimum, 30 days for compliance
- Test restore quarterly
