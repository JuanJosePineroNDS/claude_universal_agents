---
description: Initialize or configure database schema, migrations, and seed data for the project
---

# Database Setup

Set up the database layer for the project including schema, migrations, ORM config, and seed data.

## Workflow

1. **Detect existing database setup**:
   - Prisma (`prisma/schema.prisma`)
   - TypeORM/MikroORM (`ormconfig`, `data-source.ts`)
   - Sequelize (`sequelize.config.js`)
   - Django (`models.py`, `settings.py DATABASES`)
   - Laravel (`database/migrations/`, `.env DB_*`)
   - Rails (`db/schema.rb`, `database.yml`)
   - Raw SQL migrations folder

2. **If no database setup exists**, ask user for:
   - Database engine: PostgreSQL, MySQL, SQLite, MongoDB
   - ORM preference (or raw queries)
   - Initial entities/models to create

3. **Generate**:
   - Connection configuration (via environment variables)
   - Initial schema/models
   - Migration files (reversible up/down)
   - Seed data for development
   - Database utility scripts (reset, seed, migrate)

4. **Configure**:
   - `.env.example` with database variables
   - Connection pooling settings
   - Test database configuration (separate DB)

5. **Verify**:
   - Connection works
   - Migrations run successfully
   - Seeds populate correctly

## Rules

- Database credentials always in environment variables
- All schema changes via migrations (never manual)
- Migrations must be reversible
- Foreign keys and indexes on all relationships
- Separate test database configuration
